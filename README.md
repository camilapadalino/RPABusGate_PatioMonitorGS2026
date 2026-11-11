
# 🚌 Pátio Monitor | Automação RPA

### Global Solution 2026.2 — FIAP
**Tema: CitizenTech | Robotic Process Automation**

## 👥 Integrantes

| Nome | RM |
|---|---|
| Camila do Prado Padalino | 98316 |
| Felipe Cavalcante Bressane | 97688 |
| Gabriel Teixeira Machado | 551570 |

---

## 📌 Sobre o projeto

O **Pátio Monitor** é uma proposta de monitoramento inteligente voltada à organização e ao acompanhamento das operações em pátios e terminais de ônibus, utilizando tecnologias de automação, IoT e Inteligência Artificial.

Como parte da solução, desenvolvemos o **Smart Bus Gate**, um protótipo de cancela automatizada que utiliza um microcontrolador ESP32, sensores ultrassônico e PIR, além de um servomotor para controlar a abertura e o fechamento da cancela.

Na disciplina de **Robotic Process Automation (RPA)**, desenvolvemos automações utilizando a plataforma **n8n**, responsáveis por receber, processar e registrar os eventos gerados pelo protótipo. A solução também disponibiliza os registros por meio de uma API, permitindo sua utilização pelo MVP do Pátio Monitor.

O desenvolvimento do protótipo físico simulado, sua lógica de funcionamento e a integração com Inteligência Artificial estão documentados no repositório da disciplina de **Maker Lab**, que complementa esta entrega de RPA.

🔗 **[Acessar o repositório Smart Bus Gate — Maker Lab](https://github.com/camilapadalino/SmartBusGate_PatioMonitorGS26.git)**

## 🎯 Objetivos

- Automatizar o recebimento de eventos do Smart Bus Gate.
- Registrar as operações realizadas pela cancela.
- Aplicar regras de decisão aos eventos recebidos.
- Processar múltiplos registros de forma automatizada.
- Identificar e tratar falhas em requisições HTTP.
- Manter um histórico para auditoria e rastreabilidade.
- Disponibilizar os dados para integração com o MVP.

## 🛠️ Tecnologias utilizadas

| Tecnologia | Aplicação |
|---|---|
| n8n Cloud | Desenvolvimento dos workflows RPA |
| Webhooks HTTP | Recebimento e consulta de eventos |
| Data Tables | Armazenamento dos registros |
| JavaScript | Tratamento e formatação dos dados |
| JSON | Comunicação entre os componentes |
| Wokwi | Simulação do Smart Bus Gate |
| ESP32 | Controle do protótipo IoT |
| PowerShell | Testes das requisições HTTP |

## 🔄 Arquitetura da solução

O sistema utiliza o ESP32 para gerar eventos operacionais,
que são enviados ao n8n por meio de requisições HTTP.

O RPA recebe essas informações, executa as regras
configuradas e registra os dados para consulta posterior.

```text
ESP32 / Wokwi
      |
      v
Webhook de auditoria
      |
      v
Regras de decisão
      |
      v
Data Tables do n8n
      |
      v
API de consulta (GET)
      |
      v
MVP Pátio Monitor
```

A API de consulta disponibiliza os dados para o MVP.
A conexão com a interface do portal é uma etapa de
integração entre os componentes do projeto.

## ⚙️ Workflows desenvolvidos

### 1. Smart Bus Gate - Auditoria RPA

Responsável por receber os eventos enviados pelo
ESP32 e registrar as informações na Data Table
`smart_bus_gate_auditoria`.

Entre os dados registrados estão:

- Identificação do dispositivo.
- Tipo de evento.
- Distância identificada pelo sensor.
- Estado de movimento.
- Tipo de registro.
- Data e hora da ocorrência.

O histórico permite acompanhar os eventos de
abertura e fechamento da cancela.

### 2. Smart Bus Gate - RPA Completo

Responsável pelas funcionalidades complementares
de automação, incluindo:

- Processamento de eventos em lote.
- Repetição de operações.
- Aplicação de regras de decisão.
- Requisições HTTP.
- Tratamento de falhas.
- Integração para consulta dos dados pelo MVP.

A API de consulta está implementada neste workflow,
não sendo necessário um terceiro fluxo independente.

## 🔌 Integração com o MVP

O RPA disponibiliza uma API HTTP para consulta
dos eventos armazenados.

**Método:** GET

**Endpoint:**

```text
/webhook/eventos-smart-bus-gate
```

**Autenticação:** Header `x-api-key`

A resposta utiliza JSON e contém os seguintes campos:

| Campo | Descrição |
|---|---|
| sucesso | Indica o resultado da consulta |
| total | Quantidade de eventos válidos |
| eventos | Lista de registros retornados |

Cada evento pode conter:

- `id`
- `dispositivo`
- `evento`
- `distancia_cm`
- `movimento`
- `tipo_registro`
- `data_hora`

Exemplo simplificado de resposta:

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

A consulta deve ser realizada preferencialmente
pelo backend do MVP, evitando a exposição da
chave de autenticação no frontend.

## 📊 Regras de negócio

As automações foram organizadas para:

1. Receber os eventos gerados pelo protótipo.
2. Identificar o tipo de ocorrência.
3. Registrar as informações de auditoria.
4. Processar múltiplos eventos quando necessário.
5. Verificar o resultado das requisições HTTP.
6. Tratar falhas identificadas.
7. Disponibilizar o histórico para consulta.

## 🧪 Testes realizados

Foram realizados testes para verificar o
funcionamento das automações e suas integrações.

| Cenário | Resultado |
|---|---|
| Recebimento de eventos do ESP32 | Validado |
| Registro na Data Table | Validado |
| Processamento em lote | Testado |
| Requisições HTTP | Testadas |
| Tratamento de falhas HTTP | Testado |
| Consulta de eventos via API | Validada em teste |
| Formatação da resposta JSON | Validada |

Durante a validação da API, foram retornados
21 eventos válidos, organizados do mais recente
para o mais antigo.

As evidências das execuções estão disponíveis
nas pastas `assets/`, `logs/` e `tests/`.

## 📋 Documentação

A pasta `docs/` contém o relatório técnico do projeto,
incluindo:

- Contextualização do problema CitizenTech.
- Objetivos e escopo da automação.
- Mapeamento do processo em BPMN.
- Process Definition Document (PDD).
- Regras de negócio.
- Tratamento de exceções.
- Estratégia de auditoria.
- Integração com o MVP.
- Resultados dos testes.

A pasta também contém o Fluxograma do projeto.

## ▶️ Como reproduzir a automação

1. Criar uma conta ou acessar uma instância do n8n.
2. Importar os arquivos JSON disponíveis em `workflows/`.
3. Configurar os nós Webhook e Data Table.
4. Criar as tabelas utilizadas pela automação.
5. Configurar as credenciais de autenticação.
6. Ativar os workflows conforme necessário.
7. Enviar eventos de teste aos webhooks.
8. Conferir os registros e resultados das execuções.
9. Testar a consulta GET da API MVP.

As credenciais e chaves de autenticação não são
disponibilizadas neste repositório por motivos
de segurança.

## 🔐 Segurança e privacidade

O projeto utiliza autenticação por chave para
proteger a consulta aos eventos.

As informações armazenadas são relacionadas ao
funcionamento operacional do protótipo, sem
necessidade de identificação pessoal de passageiros.

## 🌍 Contribuição para CitizenTech

A automação desenvolvida demonstra como soluções
de IoT e RPA podem contribuir para a modernização
de processos relacionados à mobilidade urbana.

O registro automatizado de eventos permite maior
rastreabilidade e fornece informações que podem
apoiar o acompanhamento operacional de pátios
e terminais de ônibus.

## 🎥 Vídeo demonstrativo

**Link:** [Adicionar link do vídeo]

O vídeo apresenta o funcionamento das automações,
as regras de decisão, os testes realizados e a
integração com o Smart Bus Gate.

## 🔗 Links do projeto

- **Simulação Wokwi:**
  https://wokwi.com/projects/480155608742885377
- **Repositório Maker Lab:** https://github.com/camilapadalino/SmartBusGate_PatioMonitorGS26.git
- **MVP Pátio Monitor:** [Adicionar link]
- **Vídeo RPA:** [Adicionar link]

---

**FIAP — Global Solution 2026.2**

**Pátio Monitor | Smart Bus Gate | CitizenTech**
