# 🚀 Meus Workflows n8n: Pessoais & Profissionais

Bem-vindo ao meu repositório de automações e pipelines desenvolvidos no **n8n**. Este espaço reúne soluções criadas para otimizar processos profissionais (NOC, Segurança, Telecom) e automações de uso pessoal.

---

## 📂 Estrutura do Repositório

O repositório está organizado por contexto e projeto:

### 💼 Profissional (`/Linx`)
Automações focadas em infraestrutura, telecomunicações e cibersegurança.

*   **[Fortigate Vulnerability API](./Linx/Fortigate%20Vulnerability%20API.json):** 
    *   **Descrição:** Webhook que recebe uma versão do FortiOS, utiliza Puppeteer para navegar no portal FortiGuard PSIRT e IA (LangChain/Gemini) para extrair e estruturar vulnerabilidades críticas em JSON.
    *   **Tecnologias:** n8n, Puppeteer, AI Agent (Gemini), LangChain.

*   **[Motor de Operadoras (ProviderConnec)](./Linx/ProviderConnec/):**
    *   **Descrição:** Sistema completo de automação para NOC. Utiliza agentes de IA para abrir e acompanhar protocolos técnicos com operadoras via WhatsApp.
    *   **Componentes:** Parte 1 (Abertura) e Parte 2 (Interação com IA "Liz").
    *   **Documentação Detalhada:** [Ver README do Projeto](./Linx/ProviderConnec/README.md)

### 🏠 Pessoal (`/Pessoal`) - *Em breve*
*Workflows para produtividade pessoal, finanças e domótica.*

---

## 🛠️ Como Utilizar

Todos os workflows estão disponíveis no formato `.json`. Para utilizá-los:

1.  **Instale o n8n:** Recomendado via Docker.
2.  **Importe o Workflow:** No painel do n8n, vá em `Workflows` > `Import from File` e selecione o arquivo `.json` desejado.
3.  **Configure as Credenciais:** Cada workflow possui nós que exigem chaves de API (ex: Google Gemini, Evolution API, Google Sheets). Certifique-se de configurá-las adequadamente.
4.  **Ajuste os Parâmetros:** Verifique as variáveis de ambiente e nós de configuração (Set/Edit Fields) para adaptar ao seu ambiente.

---

## ⚙️ Requisitos Gerais

Para rodar a maioria destes fluxos, recomenda-se:
- **n8n** (Self-hosted ou Cloud)
- **Docker & Docker Compose** (para serviços auxiliares como Redis ou Puppeteer)
- **Acesso a LLMs** (Google AI Studio / OpenAI)

---

## 📝 Licença
Este repositório é para fins de estudo e portfólio. Sinta-se à vontade para clonar e adaptar, mas lembre-se de respeitar os termos de serviço das APIs utilizadas.

---
*Mantido por Bigas*
