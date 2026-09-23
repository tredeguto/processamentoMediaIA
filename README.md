# 🤖 Agente de IA para Processamento de Mídias e Documentos via WhatsApp (n8n + WAHA)

Uma solução completa e automatizada construída no **n8n** para captura, roteamento, decodificação e análise inteligente de mensagens de texto, arquivos **PDF** e planilhas **Excel (XLSX)** recebidas via WhatsApp, integrando inteligência artificial (OpenAI) e memória persistente em banco de dados.

---

### 🛠️ Badges & Habilidades Utilizadas

![n8n](https://img.shields.io/badge/n8n-FF6D5A?style=for-the-badge&logo=n8n&logoColor=white)
![OpenAI](https://img.shields.io/badge/OpenAI_GPT--4o--mini-412991?style=for-the-badge&logo=openai&logoColor=white)
![WhatsApp](https://img.shields.io/badge/WhatsApp_WAHA-25D366?style=for-the-badge&logo=whatsapp&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![JSON](https://img.shields.io/badge/JSON_Processing-000000?style=for-the-badge&logo=json&logoColor=white)
![Docker](https://img.shields.io/badge/Docker_VPS-2496ED?style=for-the-badge&logo=docker&logoColor=white)

---

## 📌 Visão Geral do Fluxo

O objetivo principal desta automação é atuar como um **Agente Integrador Inteligente**:

1. **Recepção e Filtragem:** Recebe webhooks do WhatsApp (via WAHA) e ignora mensagens enviadas pelo próprio sistema ou vindas do app do dispositivo.
2. **Classificação Multimídia (Switch):** Identifica o formato da mensagem enviada pelo usuário:
   * **Texto Direto:** Encaminhado imediatamente para o Agente de IA.
   * **Documento PDF:** Realiza a busca do binário criptografado, efetua o *decrypt* e extrai o conteúdo de texto para análise.
   * **Planilha Excel (XLSX):** Efetua o *download*, descriptografa o arquivo, converte e consolida os dados tabulares em JSON estruturado para envio ao modelo de IA.
   * **Arquivos Não Suportados:** Dispara uma resposta automática com instruções sobre os formatos aceitos.
3. **Agente de IA com Memória Persistente:** O motor OpenAI (GPT-4o-mini) processa o conteúdo com auxílio do nó `Postgres Chat Memory`, mantendo o histórico e contexto da conversa por número de telefone.
4. **Disparo de Resposta:** Envia a resposta formatada de volta ao usuário no WhatsApp.

---

## 🧩 Arquitetura do Workflow

```text
[ Webhook (WAHA) ] ➔ [ Filtro ] ➔ [ Roteador Switch ]
                                        │
    ┌───────────────────────────────────┼───────────────────────────────────┐
    ▼                                   ▼                                   ▼
[ Texto ]                         [ PDF ]                              [ XLSX ]
    │                                   │                                   │
    │                       [ Download Binário ]                [ Download Binário ]
    │                                   │                                   │
    │                    [ Decrypt WhatsApp Media ]          [ Decrypt WhatsApp Media ]
    │                                   │                                   │
    │                         [ Extract PDF Text ]                 [ Extract XLSX ]
    │                                   │                                   │
    │                                   │                        [ Aggregate Items ]
    └───────────────────────────────────┼───────────────────────────────────┘
                                        ▼
                                [ Set Prompt ]
                                        ▼
                   [ AI Agent (OpenAI + Postgres Memory) ]
                                        ▼
                        [ HTTP Request (Envia WAHA) ]
