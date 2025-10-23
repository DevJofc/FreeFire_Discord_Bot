# 🧠 Documentação Técnica — Bot de Envio Automático Free Fire 🎮

## 📘 Visão Geral

Este bot foi desenvolvido para **automatizar o envio de presentes no Free Fire**, utilizando o **estoque de contas da empresa** e a **API pública do FreeFireAPI**.
Ele foi projetado para uso administrativo em **Discord**, oferecendo comandos seguros e dinâmicos que permitem:

* Consultar o **estoque de contas disponíveis**
* Verificar **saldo de diamantes** automaticamente
* Enviar **itens (como Passe Booyah ou Caixa Token)** para jogadores
* **Atualizar o estoque** em tempo real conforme os envios são realizados

---

## 🧩 Tecnologias Utilizadas

| Tecnologia                                                                         | Função                                                                   |
| ---------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| **Node.js (v18+)**                                                                 | Ambiente principal de execução                                           |
| **discord.js (v14)**                                                               | Framework para criação e gerenciamento de comandos no Discord            |
| **axios**                                                                          | Comunicação HTTP com a API do Free Fire                                  |
| **fs-extra**                                                                       | Leitura e escrita de arquivos JSON (estoque)                             |
| **FreeFireAPI ([https://freefireapi.vercel.app](https://freefireapi.vercel.app))** | API pública usada para envio de presentes e consulta de dados das contas |

---

## ⚙️ Estrutura de Pastas

```
📂 bot-freefire/
├── 📁 commands/
│   ├── enviar.js          # Comando principal de envio de presentes
│   └── estoque.js         # Comando para visualizar o estoque
│
├── 📁 utils/
│   └── api.js             # Funções auxiliares para integrar com a FreeFireAPI
│
├── 📁 data/
│   └── estoque.json       # Lista de contas disponíveis para uso
│
├── config.json            # Configurações gerais (chave da API, etc.)
├── index.js               # Inicialização do bot e carregamento dos comandos
└── package.json
```

---

## ⚡ Configuração Inicial

### 1. Instalação

```bash
npm install discord.js axios fs-extra
```

### 2. Configuração da API

Crie um arquivo `config.json`:

```json
{
  "apiKey": "SUA_CHAVE_DA_API_AQUI",
  "tokenDiscord": "SEU_TOKEN_DO_BOT"
}
```

### 3. Estrutura do Estoque

Crie `data/estoque.json`:

```json
[
  {
    "uid": "12345678",
    "accessToken": "token_de_acesso_aqui"
  },
  {
    "uid": "87654321",
    "accessToken": "outro_token_aqui"
  }
]
```

---

## 🧱 Módulos Principais

### 🔹 `utils/api.js`

Contém funções auxiliares para comunicação com a **FreeFireAPI**:

#### `checkGameWallet(token)`

> Retorna informações da conta (ex: saldo de diamantes)

* **Parâmetro:** `token` (string)
* **Retorno:** `{ data: { gems: number } }`

#### `enviarPresente(token, giftId, quantity, playerId, message?)`

> Envia um item (passe, caixa, etc.) para um jogador

* **Parâmetros:**

  * `token`: token de acesso da conta
  * `giftId`: ID do item
  * `quantity`: quantidade (geralmente 1)
  * `playerId`: ID do jogador que receberá
  * `message`: mensagem opcional
* **Retorno:** `{ sucesso: true }` ou `{ sucesso: false, erro: "mensagem" }`

---

### 🔹 `commands/estoque.js`

Exibe todas as contas disponíveis e seus respectivos **saldos de diamantes**.

* Usa `checkGameWallet()` para cada conta
* Mostra resultado formatado em um **Embed do Discord**

**Exemplo de uso:**

```
/estoque
```

🖼️ **Retorno:**

```
📋 Estoque de Contas
🆔 12345678 — 1250 💎
🆔 87654321 — 350 💎
```

---

### 🔹 `commands/enviar.js`

Gerencia o envio de itens diretamente pelo Discord.

#### Funcionamento:

1. Verifica o tipo de item (Passe Booyah ou Caixa Token)
2. Percorre o estoque e encontra uma conta com **diamantes suficientes**
3. Envia o item com a função `enviarPresente()`
4. Atualiza ou remove a conta do `estoque.json`
5. Responde ao usuário com o status da operação

#### Regras:

* **1 Passe Booyah = 250 diamantes**
* **1 Caixa Token = 40 diamantes**
* Caso o saldo da conta seja insuficiente, ela é **descartada**
* O estoque é **reescrito automaticamente**

**Exemplo de uso:**

```
/enviar tipo:passe id:12345678
```

🟢 **Resposta:**

```
✅ Passe Booyah enviado com sucesso para 12345678 usando conta 98765432
```

🔴 **Erro:**

```
❌ Nenhuma conta com diamantes suficientes para enviar Passe Booyah.
```

---

## 🧠 Lógica de Estoque

Cada conta no arquivo `estoque.json` representa uma conta válida para envio.
O sistema consulta automaticamente o saldo da conta antes de enviar qualquer item.

**Fluxo de verificação:**

```
1️⃣ Ler estoque.json
2️⃣ Para cada conta:
     ↳ Consultar saldo via checkGameWallet()
     ↳ Se saldo >= custo do item → usar essa conta
3️⃣ Enviar item via enviarPresente()
4️⃣ Atualizar estoque
```

---

## 🔒 Segurança

* O **token de cada conta** deve ser mantido **confidencial**.
* O bot **não armazena logs sensíveis** (apenas UID e status).
* É recomendado usar o bot **em canal restrito** para evitar abusos.
* A chave da API é usada apenas no backend e **nunca exibida publicamente**.

---

## 🧰 Manutenção e Atualizações

| Ação                      | Descrição                                                 |
| ------------------------- | --------------------------------------------------------- |
| 🔁 Atualizar Estoque      | Edite `estoque.json` manualmente adicionando novas contas |
| 🧹 Limpar Contas Mortas   | Remova contas com saldo 0 ou tokens expirados             |
| 🔧 Adicionar novos itens  | Basta editar o objeto `giftConfig` dentro de `enviar.js`  |
| 🚀 Atualizar dependências | `npm update`                                              |

---

## 🧾 Exemplos de Expansão

* **Adicionar limites diários de envio por conta**
* **Criar painel web para monitorar estoque e histórico de envios**
* **Registrar log de transações em MongoDB**
* **Integrar sistema de recompensas para administradores**

---

## 👤 Autor

**Desenvolvido por:** DevJ
**Função:** Programador e gestor técnico
**Versão atual:** `1.0.0`
**Data:** Outubro de 2025
