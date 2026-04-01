# O Que São Variáveis de Ambiente? Por Que Você Não Pode Colocar Chaves de API no Código

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Atualizado:** Março de 2026  
**Tempo de leitura:** ~6 minutos

-----

## Começando com um Desastre Real

Em 2022, um desenvolvedor fez push de código contendo uma chave de API da AWS para o GitHub.

Em quatro minutos, programas automatizados varreram e encontraram a chave.

Oito horas depois, ele recebeu uma conta de $50.000 da AWS.

Isso não é um caso isolado. Milhares de chaves de API são acidentalmente expostas no GitHub diariamente, varridas e abusadas por ferramentas automatizadas.

-----

## O Que São Variáveis de Ambiente?

Seu app precisa de algumas "informações secretas" para funcionar:

- Chave de API do Stripe (para pagamentos)
- Chave de API do Resend (para enviar e-mails)
- JWT secret (para assinar tokens de login)
- Strings de conexão do banco de dados

Essas não podem ser escritas no código, porque o código é enviado para o GitHub.

**Variáveis de ambiente** armazenam essas informações secretas fora do código — no sistema operacional ou no "ambiente" da plataforma de deploy. Seu código lê esses valores em tempo de execução, mas os valores em si não existem no código.

-----

## Desenvolvimento Local: Arquivos .env

Durante o desenvolvimento local, você normalmente cria um arquivo `.env`:

```
STRIPE_SECRET_KEY=sk_live_xxxxx
RESEND_API_KEY=re_xxxxx
JWT_SECRET=minha-super-chave-secreta
```

Depois lê no código assim:

```typescript
// Node.js / Remix
const stripeKey = process.env.STRIPE_SECRET_KEY

// Cloudflare Workers
const stripeKey = env.STRIPE_SECRET_KEY
```

**Crítico: arquivos `.env` NUNCA devem ser enviados para o GitHub.**

Confirme que seu `.gitignore` inclui esta linha:

```
.env
.env.local
.env.production
```

-----

## Ambiente de Produção: Gerenciamento de Secrets da Plataforma

`.env` local funciona apenas no seu computador. Ao fazer deploy para produção, você precisa definir variáveis de ambiente na plataforma.

**Abordagem Cloudflare Workers:**

```bash
# Definir secret com Wrangler CLI
npx wrangler secret put JWT_SECRET
# Após inserir o valor, o Cloudflare criptografa e armazena, não vai aparecer em nenhum log
```

Ou defina manualmente no Cloudflare Dashboard em Workers → Settings → Variables.

-----

## Erros Comuns

### Erro 1: Push do .env para o GitHub

```bash
# Remédio: remover do histórico do git
git rm --cached .env
git commit -m "remove .env do rastreamento"
```

Mas se já foi feito push, assuma que a chave vazou, vá imediatamente à plataforma correspondente para revogar e gerar uma nova chave.

### Erro 2: Chave hardcoded no código

```typescript
// ❌ Isso expõe sua chave
const stripe = new Stripe("sk_live_xxxxx")

// ✅ Abordagem correta
const stripe = new Stripe(env.STRIPE_SECRET_KEY)
```

### Erro 3: console.log dos valores do .env

```typescript
// ❌ Logs podem ser gravados
console.log("Chave Stripe:", env.STRIPE_SECRET_KEY)

// ✅ Só confirme a existência, não imprima o valor
console.log("Chave Stripe existe:", !!env.STRIPE_SECRET_KEY)
```

-----

## Resumo

O conceito central das variáveis de ambiente é simples: **segredos não pertencem ao código, pertencem ao ambiente.**

Três princípios para lembrar:

1. Adicione `.env` ao `.gitignore`
2. Defina segredos de produção na plataforma, não no código
3. Se vazar acidentalmente, revogue e regenere imediatamente, sem exceções

-----

👉 [Próximo: O Que É uma API? Comunicação Entre Frontend e Backend em Linguagem Simples](./03-what-is-api-frontend-backend-pt-br.md)

Dúvidas? Me encontre no X em [@dankopeng](https://x.com/dankopeng).  
👉 **[vibefast.app](https://vibefast.app)** — Early bird $99, preço sobe para $199 em 1º de junho de 2026.
