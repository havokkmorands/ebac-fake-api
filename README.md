# EBAC Fake API — Next.js (Ebac Sports, Ebac Jobs, Eplay & Efood)

API didática construída com **Next.js (Pages Router)**. Fornece endpoints para 4 contextos de aula:

- **Módulo 30 – EBAC Sports**
- **Módulo 34 – EBAC Jobs**
- **Módulos 36 a 38 – Eplay e Efood**

> Após o deploy na **Vercel**, **atualize as URLs base de mídia** nos arquivos de *mocks* para apontar para o domínio do seu projeto, e consuma os endpoints `/api/**` desse mesmo domínio.

---

## Requisitos

- Node.js 18+ e npm/pnpm/yarn
- Conta na [Vercel](https://vercel.com/)

---

## Estrutura (relevante)

```
src/
├─ pages/
│  └─ api/
│     ├─ ebac_sports.ts
│     ├─ vagas.ts
│     ├─ vagas/[id].ts
│     ├─ candidaturas.ts
│     ├─ contatos.ts
│     ├─ eplay/
│     │  ├─ acao.ts
│     │  ├─ luta.ts
│     │  ├─ rpg.ts
│     │  ├─ simulacao.ts
│     │  ├─ esportes.ts
│     │  ├─ promocoes.ts
│     │  ├─ em-breve.ts
│     │  ├─ destaque.ts
│     │  └─ jogos/[id].ts
│     └─ efood/
│        ├─ restaurantes.ts
│        ├─ restaurantes/[id].ts
│        └─ checkout.ts
└─ mocks/
   ├─ jogos.ts           # dados e seleções do Eplay (inclui mediaPath)
   ├─ restaurantes.ts    # dados do Efood (inclui mediaPath)
   ├─ vagas.ts           # dados do Jobs
   └─ candidatura.ts     # modelo para POST de candidatura
```

---

## 1) Configurar as URLs base (mídia) nos mocks

Os arquivos abaixo têm a constante `mediaPath` usada para **imagens estáticas** servidas a partir de `/public`:

- `src/mocks/jogos.ts` → `const mediaPath = 'https://SEU-PROJETO.vercel.app/eplay/'`
- `src/mocks/restaurantes.ts` → `const mediaPath = 'https://SEU-PROJETO.vercel.app/efood/'`

> **Altere `https://SEU-PROJETO.vercel.app` para o seu domínio da Vercel** (ex.: `https://meu-projeto.vercel.app`).  
> Em desenvolvimento local, as imagens também funcionarão via `http://localhost:3000/eplay/...` e `http://localhost:3000/efood/...`.

**Atenção:** isso **não** é a base dos *endpoints* de API, e sim das **imagens**. Os *endpoints* são os caminhos sob `/api/**` no mesmo domínio.

---

## 2) Rodar localmente

```bash
npm install
npm run dev
# http://localhost:3000
# Endpoints em http://localhost:3000/api/***
```

---

## 3) Deploy na Vercel

1. Suba o repositório para GitHub/GitLab/Bitbucket.
2. Importe na Vercel como projeto **Next.js** (Pages Router).
3. Faça o **Deploy**.
4. **Atualize `mediaPath`** em `src/mocks/jogos.ts` e `src/mocks/restaurantes.ts` para o seu domínio de produção.
5. Valide acessando, por exemplo:
   - `https://SEU-PROJETO.vercel.app/api/ebac_sports`
   - `https://SEU-PROJETO.vercel.app/api/eplay/acao`
   - `https://SEU-PROJETO.vercel.app/api/efood/restaurantes`

---

## 4) Endpoints Disponíveis

### 4.1 EBAC Sports (Módulo 30)

| Método | Rota                   | Descrição                           |
|------:|-------------------------|-------------------------------------|
| GET   | `/api/ebac_sports`      | Lista produtos esportivos (id, nome, imagem, preco). |

**Exemplo de uso (curl):**
```bash
curl https://SEU-PROJETO.vercel.app/api/ebac_sports
```

---

### 4.2 EBAC Jobs (Módulo 34)

| Método | Rota               | Descrição                                                                 | Body (se aplicável)                |
|------:|---------------------|---------------------------------------------------------------------------|------------------------------------|
| GET   | `/api/vagas`        | Lista todas as vagas.                                                     | —                                  |
| GET   | `/api/vagas/<built-in function id>`   | **Atenção:** `<built-in function id>` é **índice** do array (0-based), não o campo `id`.    | —                                  |
| POST  | `/api/candidaturas` | Registra uma candidatura.                                                 | `{"vagaId": 2, "email": "a@b.com"}` |
| GET   | `/api/contatos`     | Lista contatos                     (`{"data": [...]}`).                 | —                                  |
| POST  | `/api/contatos`     | Insere contato                      (`{"contato": {...}}`).           | `{"contato": { "name": "...", "email":"...", "phone": 119... }}` |
| PUT   | `/api/contatos`     | Atualiza contato pelo `id`           (`{"contato": {...}}`).          | `{"contato": { "id": 1, ... }}` |
| DELETE| `/api/contatos`     | Remove contato pelo `id`.                                                 | `{"id": 1}`                      |

**Observações:**  
- `src/models/Vaga.ts` define o shape de vaga (titulo, localizacao, nivel, modalidade, salarioMin/Max, requisitos).  
- `src/mocks/vagas.ts` é a fonte dos dados.  
- `src/mocks/candidatura.ts` demonstra o payload de candidatura.

**Exemplos (curl):**
```bash
curl https://SEU-PROJETO.vercel.app/api/vagas
curl https://SEU-PROJETO.vercel.app/api/vagas/0
curl -X POST https://SEU-PROJETO.vercel.app/api/candidaturas -H "Content-Type: application/json" -d '{"vagaId":2,"email":"dev@ebac.com"}'
```

---

### 4.3 Eplay (Módulos 36–38)

As rotas retornam subconjuntos da lista `jogos` definida em `src/mocks/jogos.ts`:

| Método | Rota                      | Descrição                                       |
|------:|----------------------------|-------------------------------------------------|
| GET   | `/api/eplay/acao`         | Jogos de categoria **Ação**.                    |
| GET   | `/api/eplay/luta`         | Jogos de categoria **Luta**.                    |
| GET   | `/api/eplay/rpg`          | Jogos de categoria **RPG**.                     |
| GET   | `/api/eplay/simulacao`    | Jogos de categoria **Simulação**.               |
| GET   | `/api/eplay/esportes`     | Jogos de categoria **Esportes**.                |
| GET   | `/api/eplay/promocoes`    | Jogos **em promoção**.                          |
| GET   | `/api/eplay/em-breve`     | Jogos **em breve**.                             |
| GET   | `/api/eplay/destaque`     | **Um** jogo em destaque (seleção aleatória).    |
| GET   | `/api/eplay/jogos/{id}` | **Atenção:** `<built-in function id>` é **índice** do array (0-based). |

#### Checkout (Eplay)

| Método | Rota                   | Descrição                                  |
|------:|-------------------------|--------------------------------------------|
| GET   | `/api/eplay/checkout`   | Retorna **schema** de payload esperado.    |
| POST  | `/api/eplay/checkout`   | Valida payload e retorna `{"orderId": "...""}`. |

**Modelo de payload (resumo):**
```json
{
  "products": [{"id": 1, "price": 0}],
  "billing": {"name": "string", "email": "string", "document": "string"},
  "delivery": {"email": "string"},
  "payment": {
    "card": {
      "owner": {"name": "string", "document": "string"},
      "name": "string",
      "number": "string",
      "expires": {"month": 12, "year": 1234},
      "code": 123
    },
    "installments": 1
  }
}
```

> **Dica**: o arquivo `src/pages/api/eplay/checkout.ts` contém toda a validação de campos.

---

### 4.4 Efood (Módulos 36–38)

| Método | Rota                               | Descrição                                 |
|------:|-------------------------------------|-------------------------------------------|
| GET   | `/api/efood/restaurantes`           | Lista restaurantes.                        |
| GET   | `/api/efood/restaurantes/{id}`    | Retorna restaurante por **id** (match por `id`). |
| GET   | `/api/efood/checkout`               | Retorna **schema** de payload esperado.    |
| POST  | `/api/efood/checkout`               | Valida payload e retorna `{"orderId": "...""}`. |

**Modelo de payload para POST /checkout (resumo):**
```json
{
  "products": [{"id": 1, "price": 0}],
  "delivery": {
    "receiver": "string",
    "address": {
      "description": "string",
      "city": "string",
      "zipCode": "string",
      "number": 12,
      "complement": "string"
    }
  },
  "payment": {
    "card": {
      "owner": {"name": "string", "document": "string"},
      "name": "string",
      "number": "string",
      "expires": {"month": 12, "year": 1234},
      "code": 123
    },
    "installments": 1
  }
}
```

> **Atenção:** Diferente de `eplay/jogos/[id]`, aqui o `<built-in function id>` é o **id do restaurante**, não o índice.

---

## 5) Módulos do Curso (resumo)

- **Módulo 30 — Ebac Sports**  
  Consumo de catálogo simples para loja esportiva: `GET /api/ebac_sports`.

- **Módulo 34 — Ebac Jobs**  
  Listagem de vagas e fluxo básico de contato/candidatura: `GET /api/vagas`, `GET /api/vagas/{idx}`, `POST /api/candidaturas`, `GET|POST|PUT|DELETE /api/contatos`.

- **Módulos 36 a 38 — Eplay & Efood**  
  Catálogo de jogos por categoria, destaque, promoções, detalhe por índice e **checkout**;  
  Catálogo de restaurantes, detalhe por **id** e **checkout**: ver seção 4.3 e 4.4.

---

## 6) Observações e Dicas

- **CORS:** as rotas usam o pacote `cors` permitindo `GET`, `POST`, etc. Se consumir de outro domínio, adicione regras conforme necessário.
- **Índices vs IDs:** `eplay/jogos/{id}` e `vagas/{id}` usam **índice** do array; já `efood/restaurantes/{id}` usa **id real**.
- **Imagens:** estão em `public/eplay/**`, `public/efood/**`, `public/ebac_sports/**`. O `mediaPath` deve apontar para o domínio correto.
- **Erros comuns:**
  - `404` após deploy → confira a **URL** e a **estrutura** em `src/pages/api/**`.
  - Imagens quebradas → ajuste `mediaPath` no mock correspondente.
  - Validação de checkout falhando → compare seu body com o **schema** retornado via `GET /checkout`.

---

## 7) Exemplos rápidos

```bash
# EBAC Sports
curl https://SEU-PROJETO.vercel.app/api/ebac_sports

# Jobs
curl https://SEU-PROJETO.vercel.app/api/vagas
curl https://SEU-PROJETO.vercel.app/api/vagas/0
curl -X POST https://SEU-PROJETO.vercel.app/api/candidaturas -H "Content-Type: application/json" -d '{"vagaId":2,"email":"dev@ebac.com"}'

# Eplay
curl https://SEU-PROJETO.vercel.app/api/eplay/acao
curl https://SEU-PROJETO.vercel.app/api/eplay/jogos/0
curl -X GET https://SEU-PROJETO.vercel.app/api/eplay/checkout
# Envie um payload com os campos do schema para POST /checkout

# Efood
curl https://SEU-PROJETO.vercel.app/api/efood/restaurantes
curl https://SEU-PROJETO.vercel.app/api/efood/restaurantes/1
curl -X GET https://SEU-PROJETO.vercel.app/api/efood/checkout
```

---
