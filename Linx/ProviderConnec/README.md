# 🤖 Motor de Operadoras: Automação NOC com IA (n8n)

Este repositório contém a solução completa para automação de abertura e acompanhamento de protocolos técnicos junto a operadoras de telecomunicações, utilizando **n8n**, **Inteligência Artificial (Google Gemini)** e **Evolution API**.

A solução é composta por dois agentes de IA especializados que orquestram todo o ciclo de vida de um incidente, desde o alerta inicial até o fechamento com o número do protocolo.

---

## 🏗️ Arquitetura da Solução

A automação é dividida em dois workflows principais, que compartilham uma base de dados no Google Sheets para persistência de estado e histórico.

### 1️⃣ Agente de Primeiro Contato (Parte 1)

Responsável por iniciar o atendimento assim que um incidente é detectado por sistemas de monitoramento (ex: Zabbix, PRTG).

- **Gatilho:** Webhook (Recebe `operadora`, `designacao`, `eventid` e `numero`).
- **Enriquecimento:** Consulta a `base_contatos_operadoras` (Google Sheets) para identificar o canal de atendimento correto.
- **IA (Google Gemini):** Atua como um especialista em NOC, gerando uma mensagem técnica, formal e objetiva.
- **Persistência:** Registra o início do atendimento na planilha de controle com status `processando`.
- **Canal:** Envia a mensagem inicial via WhatsApp através da **Evolution API**.

### 2️⃣ Agente de Interação - "Liz" (Parte 2)

Responsável por manter a conversa ativa com o suporte da operadora no WhatsApp até a obtenção do protocolo.

- **Gatilho:** Webhook da Evolution API (Mensagens recebidas).
- **Controle de Fluxo:** Utiliza um nó de **Message Debounce (Redis)** para evitar processamento duplicado ou respostas atropeladas.
- **Contexto & Memória:**
  - Recupera o histórico do chamado na planilha de controle.
  - Utiliza `Window Buffer Memory` para manter o contexto das últimas 30 mensagens da conversa atual.
- **IA (Liz):** Responde a perguntas técnicas do suporte, fornece dados do circuito (respeitando a LGPD) e monitora a conversa para extrair o número do protocolo.
- **Finalização:** Ao detectar o protocolo, atualiza a planilha de controle para o status `finalizado` e extrai o número do chamado.

---

## 🛠️ Requisitos de Infraestrutura

Para implementar esta automação, você deve ter os seguintes serviços operacionais:

| Serviço | Finalidade |
| :--- | :--- |
| **n8n** | Orquestrador dos fluxos e agentes de IA. |
| **Evolution API** | Gateway para comunicação via WhatsApp. |
| **Redis** | Necessário para o gerenciamento de filas e debounce de mensagens. |
| **Google Sheets** | Banco de dados relacional para contatos e logs de chamados. |
| **Google Gemini API** | Modelos de linguagem (LLM) para os agentes. |

---

## ⚙️ Configuração e Instalação

### 1. Importação dos Workflows

1. No seu n8n, crie dois novos workflows.
2. Importe os arquivos `Motor Operadoras - Parte 1.json` e `Motor Operadora - Parte 2.json` respectivamente.

### 2. Configuração de Credenciais

Certifique-se de configurar as seguintes credenciais no n8n:
- **Google Gemini(PaLM) Api**: Obtenha sua chave no [Google AI Studio](https://aistudio.google.com/).
- **Google Sheets OAuth2**: Configure o acesso à sua conta Google.
- **Evolution API**: Configure o `server_url` e o `apikey`.
- **Redis**: Informe os dados de conexão para o nó `Message Debounce`.

### 3. Estrutura das Planilhas (Google Sheets)

A automação espera duas tabelas (ou abas) com as seguintes colunas:

#### **Aba: base_contatos_operadoras**
>
> Usada para saber para quem e como mandar a mensagem inicial.
- `operadora` (Chave de busca)
- `contato` (Número do WhatsApp da operadora)
- `detalhes_tecnicos` (Informações adicionais para a IA)

#### **Aba: controle_de_acionamento**
>
> Usada para gerenciar o estado dos chamados ativos.
- `eventid` (ID único do evento de monitoramento)
- `Operadora`, `numero`, `Status` (processando/finalizado)
- `designacao`, `data`, `mensagem_inicial`, `numero_protocolo`

---

## 🛡️ Diretrizes de Operação da IA (System Prompts)

Os agentes possuem diretrizes rígidas configuradas nos `System Messages`:

- **LGPD:** A IA está instruída a nunca fornecer dados sensíveis de colaboradores (CPF, e-mail pessoal, etc).
- **Escopo Técnico:** O foco é estritamente em infraestrutura (NOC).
- **Formalidade:** Tom profissional e direto, sem "chitchat" desnecessário.
- **Precisão:** A IA não inventa dados; ela utiliza apenas o que está na planilha ou no input do webhook.

---

## 🚀 Exemplo de Acionamento (API)

Para disparar a abertura de um protocolo via sistema externo:

```bash
curl -X POST https://seu-n8n.url/webhook/d8c55e12-dc16-4006-8a88-d86c4397caae \
-H "Content-Type: application/json" \
-d '{
  "operadora": "VIVO",
  "numero": "5511999999999",
  "designacao": "SP-CTO-01-VIVO",
  "eventid": "99842"
}'
```
