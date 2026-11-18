# 🚌 Pátio Monitor | Automação RPA

**Global Solution 2026.2 - FIAP**   
**Disciplina:** Robotic Process Automation (RPA)

## 👥 Integrantes

| Nome | RM |
|---|---|
| Camila do Prado Padalino | 98316 |
| Felipe Cavalcante Bressane | 97688 |
| Gabriel Teixeira Machado | 551570 |

---

## 🔗 Links do projeto

- **MVP Pátio Monitor:**  
  https://patio-monitor-production.up.railway.app/

- **Vídeo demonstrativo RPA:**  
  https://www.youtube.com/watch?v=5r3gIS0Ebfg

- **Documentação Técnica:**
  O relatório técnico completo da automação RPA está disponível para consulta:<br>
  [Clique aqui para visualizar o Relatório Técnico em PDF](docs/relatorio_tecnico_rpa.pdf)

- **Repositório Smart Bus Gate - Maker Lab:**  
  https://github.com/camilapadalino/SmartBusGate_PatioMonitorGS26

---

## 📌 Sobre o projeto

O **Pátio Monitor** é uma proposta de monitoramento inteligente voltada à organização, ao acompanhamento e à rastreabilidade das operações em pátios e terminais de ônibus. O projeto integra tecnologias de Internet das Coisas (IoT), Inteligência Artificial (IA) e automação de processos para demonstrar como soluções digitais podem apoiar a gestão operacional do transporte coletivo.

Como parte da solução, desenvolvemos o **Smart Bus Gate**, um protótipo de cancela automatizada que utiliza um microcontrolador ESP32, sensor ultrassônico, sensor PIR e servomotor para controlar a abertura e o fechamento da cancela de acordo com as condições identificadas no ambiente.

Na disciplina de **Robotic Process Automation (RPA)**, desenvolvemos uma automação utilizando a plataforma **n8n Cloud**, responsável pelo recebimento, validação, processamento e armazenamento dos eventos operacionais gerados pelo protótipo.

A solução também disponibiliza uma API HTTP GET para consulta dos registros, permitindo sua utilização pelo MVP do Pátio Monitor.

O desenvolvimento do protótipo IoT, sua lógica de funcionamento e a integração com Inteligência Artificial estão documentados no repositório da disciplina de Maker Lab, que complementa esta entrega.

🔗 [Acessar o repositório Smart Bus Gate — Maker Lab](https://github.com/camilapadalino/SmartBusGate_PatioMonitorGS26)

---

## 🎯 Objetivos

A automação RPA foi desenvolvida com os seguintes objetivos:

- Automatizar o recebimento dos eventos gerados pelo Smart Bus Gate.
- Validar e classificar as ocorrências operacionais.
- Registrar eventos de abertura, fechamento e possíveis obstruções.
- Manter um histórico organizado para auditoria e rastreabilidade.
- Processar múltiplos registros por meio de estruturas de repetição.
- Aplicar regras de decisão durante a execução.
- Identificar, tratar e registrar falhas em requisições HTTP.
- Disponibilizar os registros por meio de uma API para integração com o MVP.

---

## 🛠️ Tecnologias utilizadas

| Tecnologia | Aplicação |
|---|---|
| n8n Cloud | Orquestração dos fluxos de automação |
| Webhooks HTTP | Recebimento de eventos e consulta à API |
| Data Tables | Persistência dos registros operacionais |
| JavaScript | Validação, filtragem e organização dos dados |
| JSON | Comunicação e intercâmbio de informações |
| Wokwi | Simulação do protótipo Smart Bus Gate |
| ESP32 | Controle dos sensores e da cancela |
| PowerShell | Execução de testes HTTP e validação da API |
| GitHub | Versionamento e disponibilização dos artefatos |

---

## 🔄 Arquitetura da solução

A automação foi desenvolvida na plataforma **n8n Cloud**, sendo organizada em um único workflow denominado **Smart Bus Gate - RPA Completo**.

Esse workflow reúne três fluxos funcionais, cada um com responsabilidades e formas de acionamento específicas.

### Fluxo 1 — Auditoria de Eventos

Responsável pelo recebimento, validação, classificação e armazenamento
dos eventos operacionais do Smart Bus Gate, representados por
requisições HTTP POST ao webhook do n8n.

```text
ESP32 / Wokwi
      |
      v
Webhook HTTP POST
      |
      v
Validação dos dados
      |
      v
Classificação do evento
      |
      v
Registro nas Data Tables
```

### Fluxo 2 — Processamento em Lote e Tratamento de Falhas

Responsável pela execução automatizada de múltiplos eventos de teste, utilizando estruturas de repetição, requisições HTTP e mecanismos de tratamento de exceções.

```text
Gatilho manual
      |
      v
Geração de eventos de teste
      |
      v
Loop Over Items
      |
      v
HTTP Request
      |
      +---- Sucesso ----> Próximo item
      |
      +---- Erro -------> Registrar falha
                                |
                                v
                           Data Table
                                |
                                v
                           Próximo item
```

### Fluxo 3 — Consulta de Eventos para o MVP

Responsável pela disponibilização dos registros armazenados por meio de uma API HTTP GET autenticada.

```text
Requisição HTTP GET
      |
      v
Webhook de consulta
      |
      v
Get row(s) — Data Table
      |
      v
Code in JavaScript
      |
      v
Respond to Webhook
      |
      v
Resposta JSON
```

Os três fluxos estão organizados dentro do mesmo workflow, mas possuem gatilhos e responsabilidades independentes.

O **Fluxo 1** armazena os eventos operacionais, enquanto o **Fluxo 3** consulta esses registros quando uma requisição GET é realizada. O **Fluxo 2** permite testar o processamento automatizado e os mecanismos de tratamento de falhas.

Essa organização centraliza as funcionalidades de RPA e mantém a separação lógica das operações.

---

## ⚙️ Funcionalidades desenvolvidas

### 1. Auditoria de Eventos

O fluxo de auditoria recebe eventos do Smart Bus Gate por meio de requisições HTTP POST.

Após o recebimento, as informações passam por etapas de validação e classificação, permitindo identificar ocorrências relacionadas ao funcionamento da cancela.

Os eventos são armazenados na Data Table `smart_bus_gate_auditoria`.

Entre os campos registrados estão:

| Campo | Descrição |
|---|---|
| `dispositivo` | Identificação do dispositivo IoT |
| `evento` | Tipo de ocorrência identificada |
| `distancia_cm` | Distância detectada pelo sensor ultrassônico |
| `movimento` | Estado do sensor de movimento |
| `tipo_registro` | Classificação utilizada na auditoria |
| `data_hora` | Data e hora da ocorrência |

Entre os eventos observados nos testes estão `CANCELA_ABERTA` e `CANCELA_FECHADA`.

A automação também contempla o direcionamento de possíveis ocorrências de obstrução, conforme as regras configuradas.

### 2. Processamento em Lote e Tratamento de Falhas

O segundo fluxo demonstra a aplicação de conceitos de automação de processos, incluindo:

- Geração de eventos de teste utilizando JavaScript.
- Processamento individual de múltiplos itens.
- Estruturas de repetição com `Loop Over Items`.
- Execução de requisições HTTP.
- Diferenciação entre resultados de sucesso e erro.
- Tratamento de exceções.
- Registro estruturado das falhas identificadas.

Durante os testes, foi executado um cenário controlado de falha HTTP 404, utilizando uma rota inexistente.

A ocorrência foi encaminhada ao mecanismo de tratamento de erros e registrada para auditoria, demonstrando a capacidade da automação de identificar e documentar falhas de integração.

### 3. Consulta de Eventos para o MVP

O terceiro fluxo disponibiliza uma API HTTP GET para consulta dos registros operacionais armazenados.

O processo é composto pelos seguintes nós:

1. `Webhook` — recebe a requisição GET.
2. `Get row(s)` — consulta os registros da Data Table.
3. `Code in JavaScript` — filtra, seleciona e organiza os dados.
4. `Respond to Webhook` — retorna a resposta JSON.

O JavaScript realiza o tratamento das informações, desconsiderando registros sem identificação de dispositivo ou tipo de evento e organizando os resultados do mais recente para o mais antigo.

A API estabelece a interface de comunicação entre a automação RPA e o MVP Pátio Monitor.

---

## 🔌 Integração com o MVP

A API disponibiliza os eventos operacionais para consulta pela plataforma Pátio Monitor.

### Endpoint

**Método:** `GET`

**Caminho:**

```http
/webhook/eventos-smart-bus-gate
```

**Autenticação:** cabeçalho HTTP `x-api-key`.

A URL completa depende da instância n8n utilizada na implantação.

### Estrutura da resposta

A API retorna um objeto JSON contendo:

| Campo | Descrição |
|---|---|
| `sucesso` | Indica o resultado da consulta |
| `total` | Quantidade de registros válidos |
| `eventos` | Lista de eventos operacionais |

Cada evento contém os seguintes campos:

- `id`
- `dispositivo`
- `evento`
- `distancia_cm`
- `movimento`
- `tipo_registro`
- `data_hora`

### Exemplo de resposta

```json
{
  "sucesso": true,
  "total": 1,
  "eventos": [
    {
      "id": 22,
      "dispositivo": "SMART_BUS_GATE_01",
      "evento": "CANCELA_ABERTA",
      "distancia_cm": 13.11975,
      "movimento": false,
      "tipo_registro": "ABERTURA_CANCELA",
      "data_hora": "2026-11-09T23:35:31.147Z"
    }
  ]
}
```

O exemplo apresenta a estrutura de um registro individual. O campo `total` varia conforme a quantidade de eventos válidos disponíveis no momento da consulta.

---

## 📊 Regras de negócio

As regras implementadas na automação contemplam:

| Regra | Descrição |
|---|---|
| Recebimento de eventos | Receber dados operacionais por webhook HTTP POST |
| Validação | Verificar as informações necessárias ao processamento |
| Classificação | Identificar o tipo de ocorrência recebida |
| Auditoria | Armazenar os eventos nas Data Tables |
| Processamento em lote | Executar múltiplas requisições de forma automatizada |
| Tratamento de falhas | Direcionar erros HTTP para registro estruturado |
| Consulta | Recuperar eventos armazenados por meio da API GET |
| Formatação | Disponibilizar os dados em JSON padronizado |

---

## 🧪 Testes realizados

Foram realizados testes para verificar o funcionamento dos fluxos de automação e a comunicação entre os componentes da solução.

| Cenário | Resultado |
|---|---|
| Recebimento de eventos por webhook HTTP POST | Validado |
| Registro de eventos na Data Table | Validado |
| Aplicação de regras de decisão | Validado |
| Processamento de múltiplos itens | Testado |
| Execução de requisições HTTP | Testada |
| Tratamento de falha HTTP 404 | Validado |
| Registro estruturado de exceções | Validado |
| Consulta de eventos por API HTTP GET | Validada em teste |
| Filtragem e organização dos dados em JavaScript | Validada |
| Visualização dos eventos na interface do MVP | Demonstrada com evidências no relatório técnico |

### Resultados observados

Durante os testes de auditoria, foram registrados eventos relacionados à abertura e ao fechamento da cancela.

No processamento em lote, foram avaliados cenários de execução de requisições HTTP e tratamento de falhas, incluindo um teste controlado com resposta HTTP 404.

Na execução de consulta apresentada no relatório técnico, o fluxo GET retornou **29 registros válidos**, organizados em JSON pelo código JavaScript.

As evidências estão documentadas no relatório técnico e nos materiais de testes disponibilizados no repositório.

---

## 📋 Documentação técnica

A pasta `docs/` reúne a documentação do projeto, incluindo:

- Contextualização do problema CitizenTech.
- Objetivos e escopo da automação.
- Arquitetura da solução.
- Modelagem do processo em BPMN.
- Process Definition Document (PDD).
- Regras de negócio.
- Tratamento de exceções.
- Auditoria e rastreabilidade.
- Integração com o MVP.
- Evidências dos testes.
- Segurança, privacidade e LGPD.
- Conclusão e resultados obtidos.

O relatório técnico apresenta a descrição dos três fluxos funcionais e as evidências utilizadas para validar as funcionalidades desenvolvidas.

---

## 📁 Estrutura do repositório

A organização prevista para os arquivos da entrega é:

```text
Patio-Monitor-RPA/
│
├── README.md
│
├── N8N/
│   └── smart_bus_gate_rpa_completo.json
│
├── docs/
│   ├── relatorio_tecnico_rpa.pdf
│   ├── bpmn_smart_bus_gate.png
│   └── fluxograma.png
│
├── assets/
│   ├── fluxo_auditoria.png
│   ├── fluxo_processamento_lote.png
│   └── fluxo_consulta_get.png
│
├── logs/
│   └── auditoria_smart_bus_gate.csv
│
└── tests/
    ├── errodevalidacao.png
    └── testedofluxocompleto.png
```
---

## ▶️ Como reproduzir a automação

### Pré-requisitos

- Acesso a uma instância do n8n.
- Arquivo JSON exportado do workflow.
- Data Tables configuradas.
- Credenciais necessárias para os endpoints autenticados.
- Ferramenta para realizar requisições HTTP, como PowerShell ou Postman.

### Etapas

1. Acessar uma instância do n8n.
2. Importar o arquivo JSON disponível na pasta `N8N/`.
3. Verificar os três fluxos funcionais do workflow.
4. Configurar os nós Webhook e seus respectivos caminhos.
5. Criar ou selecionar as Data Tables utilizadas pela automação.
6. Ajustar os nós de armazenamento e consulta para as tabelas correspondentes.
7. Configurar as credenciais de autenticação necessárias.
8. Salvar e publicar ou ativar o workflow, conforme a versão do n8n.
9. Executar os testes de recebimento de eventos.
10. Conferir os registros armazenados.
11. Executar o processamento em lote.
12. Testar a consulta GET e verificar a resposta JSON.

### Observações importantes

As credenciais e chaves de autenticação não são disponibilizadas no repositório público.

Após a importação, é necessário revisar as configurações de ambiente, as referências às Data Tables e os endpoints utilizados, pois esses elementos podem variar entre instâncias do n8n.

---

## 🔐 Segurança, Privacidade e LGPD

A segurança da informação foi considerada nas etapas de armazenamento e disponibilização dos registros operacionais.

A API HTTP GET utiliza autenticação por meio do cabeçalho `x-api-key`, restringindo a consulta aos clientes que possuam a credencial configurada.

As chaves de autenticação não devem ser expostas no repositório público nem incorporadas diretamente ao código executado pelo navegador.

Para integração com o MVP, recomenda-se utilizar uma camada de backend para realizar as requisições autenticadas e proteger as credenciais.

A resposta JSON disponibiliza apenas os campos necessários para consulta e visualização dos eventos, seguindo o princípio da minimização de dados.

Na implementação atual, os registros são predominantemente técnicos e operacionais, sem identificação direta de passageiros.

O projeto considera princípios da **Lei Geral de Proteção de Dados (LGPD — Lei nº 13.709/2018)**, especialmente segurança, necessidade e prevenção.

Uma eventual implantação em ambiente real exigirá avaliações adicionais relacionadas a controle de acesso, proteção das informações, retenção dos registros e tratamento de possíveis dados pessoais.

---

## 🌍 Contribuição para CitizenTech e ODS

O Pátio Monitor está alinhado à proposta **CitizenTech**, que incentiva o desenvolvimento de soluções tecnológicas voltadas à melhoria dos serviços e da qualidade de vida nas cidades.

A automação desenvolvida demonstra como a integração entre IoT, Inteligência Artificial e RPA pode contribuir para a modernização de processos operacionais relacionados ao transporte coletivo.

O projeto está relacionado a dois Objetivos de Desenvolvimento Sustentável (ODS):

### ODS 9 — Indústria, Inovação e Infraestrutura

A solução utiliza sensores, integração entre sistemas e automação de processos para demonstrar possibilidades de modernização e monitoramento de infraestrutura operacional.

### ODS 11 — Cidades e Comunidades Sustentáveis

O projeto apresenta uma proposta com potencial para apoiar a organização e a eficiência das operações relacionadas ao transporte coletivo, contribuindo indiretamente para os objetivos de mobilidade urbana sustentável.

A iniciativa também se relaciona à **meta 11.2**, voltada ao acesso a sistemas de transporte seguros, acessíveis e sustentáveis.

Por se tratar de um protótipo acadêmico, os benefícios operacionais e sociais representam resultados esperados, ainda não mensurados em ambiente real.

---

## 🎥 Vídeo demonstrativo

**Link:** https://www.youtube.com/watch?v=5r3gIS0Ebfg

O vídeo demonstrativo apresenta o funcionamento da automação desenvolvida no n8n, incluindo:

- Estrutura dos três fluxos funcionais.
- Recebimento e registro de eventos.
- Processamento em lote.
- Aplicação de regras de decisão.
- Tratamento de falhas HTTP.
- Consulta dos registros por meio da API GET.
- Integração com o projeto Smart Bus Gate.

---

**FIAP — Global Solution 2026.2**  
**Pátio Monitor | Smart Bus Gate | CitizenTech**
