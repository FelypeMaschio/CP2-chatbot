# CP2-chatbot
# 🤖 Central de Atendimento Digital – Integração Telegram, Node-RED e Watson Assistant

Este projeto integra um bot do Telegram com o IBM Watson Assistant utilizando o Node-RED. A solução permite receber mensagens de usuários, processá-las com inteligência artificial e retornar respostas automatizadas ou botões interativos.

---

## 📌 Visão Geral

O fluxo executa as seguintes etapas:

1. O usuário envia uma mensagem via Telegram.
2. O Node-RED captura e interpreta a mensagem.
3. Informações de contexto são armazenadas para manter a coerência da conversa.
4. A mensagem é enviada ao IBM Watson Assistant.
5. O Watson retorna uma resposta com base no conteúdo treinado.
6. O Node-RED formata essa resposta (com texto ou botões).
7. A mensagem final é enviada de volta ao usuário no Telegram.

---

## 🧩 Componentes do Projeto

### 🔹 Telegram Receiver
Nó responsável por escutar mensagens recebidas no Telegram.

- Envia a mensagem para:
  - `Function: Save context` (processamento e envio ao Watson).
  - `debug 1` (para monitoramento no Node-RED).

### 🔹 Function: Save context
Salva o `chatId` e o `type` no contexto do fluxo e prepara a mensagem para o Watson:

```javascript
context.flow.chatId = msg.payload.chatId;
context.flow.type = msg.payload.type;
msg.payload = msg.payload.content;
return msg;
```

### 🔹 IBM Watson Assistant
Envia a mensagem ao assistente virtual configurado, utilizando o Assistant ID e mantém o contexto de conversação.

### 🔹 Debug 2
Exibe a resposta bruta recebida do Watson antes da formatação final.

### 🔹 Function: Watson para Telegram – Botões
Verifica o tipo de resposta do Watson:
- Se `option`: renderiza botões inline no Telegram.
- Se `text`: envia mensagem comum.

Exemplo de formatação com botões:

```javascript
{
  text: "Escolha uma opção:",
  options: {
    reply_markup: {
      inline_keyboard: [
        [{ text: 'Suporte Técnico', callback_data: 'suporte' }],
        [{ text: 'Status do Pedido', callback_data: 'status' }]
      ]
    }
  }
}
```

### 🔹 Telegram Sender
Nó que envia a resposta final de volta ao usuário no Telegram.

---

## 🔘 Comando Especial `/start`

Há um fluxo específico para o comando `/start`, que envia uma saudação e opções ao usuário:

- Problema com produto
- Problema com a entrega
- Outro tipo de problema
- Avaliar
- Finalizar

---

## 💬 Intenções no Watson Assistant

O assistente está treinado com as seguintes intenções:

- `menu`: Boas-vindas e opções iniciais
- `suporte_tecnico`: Encaminhamento ao suporte técnico
- `status_pedido`: Consulta de status de entrega
- `encerrar`: Finalização do atendimento
