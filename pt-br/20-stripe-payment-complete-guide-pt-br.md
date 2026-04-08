# Pagamentos com Stripe: Do Sandbox ao Ambiente de Produção

[English](../en/20-stripe-payment-complete-guide-en.md) · [繁中](../zh/20-stripe-payment-complete-guide-zh.md) · [Español](../es/20-stripe-payment-complete-guide-es.md) · [日本語](../jp/20-stripe-payment-complete-guide-jp.md) · [Português (BR)](../pt-br/20-stripe-payment-complete-guide-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Atualizado:** Março de 2026  
**Tempo de leitura:** ~10 minutos

-----

## Pagamentos São a Funcionalidade Mais Importante do Seu App

Quando usuários pagam, significa que seu produto é genuinamente valioso.

O Stripe é o serviço de pagamento online mais amplamente usado hoje — documentação clara, API bem projetada e suporte a praticamente todos os principais cartões de crédito e métodos de pagamento. Este guia percorre o fluxo completo, desde criar uma conta até sua primeira transação real.

-----

## Passo 1: Criar uma Conta no Stripe

Acesse [stripe.com](https://stripe.com) e cadastre-se.

Após inserir suas informações básicas, o Stripe vai exigir **verificação de identidade (KYC)**, incluindo:

- Informações legais pessoais ou empresariais
- Uma conta bancária (para receber pagamentos)
- Documentos de identidade

Esse processo geralmente leva 1–3 dias úteis. Até a verificação ser concluída, você só pode usar o modo de teste — nenhum dinheiro real pode ser processado.

**Nota para usuários em países não suportados:** O Stripe não está disponível em todos os países. Se o Stripe não estiver disponível onde você está, você tem duas opções:

**Opção A: Use um processador de pagamento local**  
Procure provedores de pagamento que suportem sua região. A desvantagem é normalmente suporte limitado para cartões internacionais, o que dificulta vender para clientes no exterior.

**Opção B: Registre uma empresa nos EUA e solicite o Stripe**  
Incorporar uma LLC nos EUA (através de serviços como Stripe Atlas ou Firstbase) permite que você solicite o Stripe. O custo único é aproximadamente $500, com taxas estaduais anuais em torno de $300. Este é um caminho comum para indie hackers que querem aceitar pagamentos globais e vem com uma conta bancária nos EUA também.

Se você só atende clientes locais, a Opção A é mais simples. Se está construindo para um mercado global, a Opção B vale a pena considerar.

-----

## Passo 2: Entender Modo de Teste e Modo Produção

O Stripe tem dois ambientes completamente separados:

**Modo de Teste (Sandbox)**

- Chaves de API começam com `sk_test_`
- Todas as transações são simuladas — nenhum dinheiro real se move
- Use os números de cartão de teste do Stripe para percorrer os fluxos

**Modo Produção (Live)**

- Chaves de API começam com `sk_live_`
- Transações reais, dinheiro real

**Sempre desenvolva e teste no modo de teste primeiro. Só mude para o modo produção após confirmar que tudo está funcionando.**

Números de cartão de teste do Stripe:

```
Número do cartão: 4242 4242 4242 4242
Validade: qualquer data futura (ex: 12/30)
CVV: qualquer três dígitos (ex: 123)
```

Este cartão sempre tem sucesso no modo de teste. Para testar um pagamento recusado:

```
Número do cartão: 4000 0000 0000 0002
→ Este cartão é sempre recusado
```

-----

## Passo 3: Obtenha Suas Chaves de API

No Stripe Dashboard → Developers → API keys, você encontrará duas chaves:

- **Publishable key** (`pk_test_...`): seguro para usar no frontend para inicializar o Stripe.js
- **Secret key** (`sk_test_...`): somente backend — nunca exponha isso em código frontend ou no GitHub

Adicione ambas às suas variáveis de ambiente:

```
STRIPE_PUBLISHABLE_KEY=pk_test_xxxxx
STRIPE_SECRET_KEY=sk_test_xxxxx
```

-----

## Passo 4: Entender Como o Stripe Checkout Funciona

O Stripe oferece duas abordagens para aceitar pagamentos:

### Stripe Checkout (Recomendado para Vibe Coders)

O Stripe fornece uma página de pagamento pré-construída. Você cria uma Checkout Session, redireciona o usuário para a página hospedada do Stripe, e o Stripe redireciona de volta para seu app quando o pagamento é concluído.

```
Usuário clica em "Comprar"
  ↓
Seu backend cria uma Checkout Session
  ↓
Usuário é redirecionado para a página de pagamento do Stripe
  ↓
Usuário insere dados do cartão (na página do Stripe — você nunca os vê)
  ↓
Pagamento bem-sucedido → Stripe redireciona para sua URL de sucesso
Pagamento falha → Stripe redireciona para sua URL de cancelamento
```

A vantagem: você não constrói um formulário de pagamento, e o Stripe cuida de toda a segurança.

### Stripe Elements (Avançado)

Incorpora o formulário de pagamento do Stripe diretamente dentro da sua própria página — aparência totalmente personalizável, mas requer mais configuração. O Checkout é suficiente para a maioria dos apps indie começando.

-----

## Passo 5: Implemente a Checkout Session

Diga à IA:

```
Crie um endpoint POST /api/checkout no Cloudflare Workers.

Requisitos:
- Aceite productId e userId
- Crie uma Stripe Checkout Session
- Leia informações do produto da tabela products do D1
- Em caso de sucesso, redirecione para /success?session_id={CHECKOUT_SESSION_ID}
- Em caso de cancelamento, redirecione para /shop

Variável de ambiente: env.STRIPE_SECRET_KEY
```

A IA vai gerar algo assim:

```typescript
import Stripe from 'stripe'

export async function handleCheckout(request: Request, env: Env) {
  const { productId, userId } = await request.json()

  const product = await env.DB.prepare(
    'SELECT * FROM products WHERE id = ?'
  ).bind(productId).first()

  if (!product) {
    return Response.json({ error: 'Produto não encontrado' }, { status: 404 })
  }

  const stripe = new Stripe(env.STRIPE_SECRET_KEY)

  const session = await stripe.checkout.sessions.create({
    payment_method_types: ['card'],
    line_items: [{
      price_data: {
        currency: 'brl',
        product_data: { name: product.name },
        unit_amount: product.price, // em centavos — R$10,00 = 1000
      },
      quantity: 1,
    }],
    mode: 'payment',
    success_url: `${env.APP_URL}/success?session_id={CHECKOUT_SESSION_ID}`,
    cancel_url: `${env.APP_URL}/shop`,
    metadata: { userId, productId },
  })

  return Response.json({ url: session.url })
}
```

-----

## Passo 6: Configure Webhooks (O Passo Mais Importante)

Muitas pessoas pulam isso e então se perguntam por que seu app não responde após um pagamento bem-sucedido — é porque o backend nunca ficou sabendo que o pagamento aconteceu.

**Webhooks** são notificações que o Stripe envia para seu servidor quando eventos ocorrem (pagamento concluído, reembolso emitido, assinatura atualizada).

### Adicione um Endpoint de Webhook no Stripe Dashboard

Stripe Dashboard → Developers → Webhooks → Add endpoint

Insira a URL do seu webhook: `https://seu-app.com/api/webhook/stripe`

Selecione os eventos a ouvir — no mínimo:

- `checkout.session.completed` (pagamento concluído)
- `payment_intent.payment_failed` (pagamento recusado)

### Implemente o Handler do Webhook

```typescript
export async function handleStripeWebhook(request: Request, env: Env) {
  const signature = request.headers.get('stripe-signature')
  const body = await request.text()

  const stripe = new Stripe(env.STRIPE_SECRET_KEY)

  let event
  try {
    // Verifique que esta requisição realmente veio do Stripe, não é falsificada
    event = stripe.webhooks.constructEvent(
      body,
      signature,
      env.STRIPE_WEBHOOK_SECRET
    )
  } catch (err) {
    return new Response('Verificação de assinatura do webhook falhou', { status: 400 })
  }

  if (event.type === 'checkout.session.completed') {
    const session = event.data.object
    const { userId, productId } = session.metadata

    // Registre o pedido no banco de dados
    await env.DB.prepare(`
      INSERT INTO orders (user_id, product_id, amount, status, stripe_session_id)
      VALUES (?, ?, ?, 'paid', ?)
    `).bind(userId, productId, session.amount_total, session.id).run()

    // Envie e-mail de confirmação via Resend
    // ...
  }

  return new Response('OK', { status: 200 })
}
```

Após configurar o webhook no Stripe Dashboard, você receberá um **Webhook Secret**. Adicione-o às suas variáveis de ambiente:

```
STRIPE_WEBHOOK_SECRET=whsec_xxxxx
```

-----

## Passo 7: Teste o Fluxo Completo no Modo Sandbox

O modo de teste do Stripe (sandbox) permite que você teste o fluxo completo de pagamento sem mover nenhum dinheiro real.

**Abordagem recomendada: faça deploy para o Cloudflare produção e teste com chaves sandbox.**

O motivo é direto: Stripe Webhooks precisam de uma URL HTTPS publicamente acessível. O Cloudflare Workers te dá uma no momento que você faz deploy — muito mais simples do que configurar um túnel local.

### Fluxo de Teste

1. **Confirme que suas variáveis de ambiente usam chaves de teste**

   No Cloudflare Dashboard → Workers → seu API Worker → Settings → Variables, verifique:
   
   ```
   STRIPE_SECRET_KEY = sk_test_xxxxx
   STRIPE_WEBHOOK_SECRET = whsec_xxxxx (webhook secret do modo de teste)
   ```

1. **Adicione um webhook de teste no Stripe Dashboard**

   Stripe Dashboard → Developers → Webhooks → Add endpoint
   
   Insira sua URL de produção: `https://seu-app.workers.dev/api/webhook/stripe`
   
   Copie o Webhook Secret que o Stripe te dá e atualize no Cloudflare.

1. **Percorra o fluxo completo de pagamento com um cartão de teste**

   Abra sua URL de produção, clique em "Comprar", e na página de pagamento do Stripe insira:
   
   ```
   Número do cartão: 4242 4242 4242 4242
   Validade: qualquer data futura (ex: 12/30)
   CVV: qualquer três dígitos (ex: 123)
   ```

1. **Verifique que cada etapa foi concluída corretamente**
   - Stripe Dashboard → Payments: a transação de teste aparece
   - Stripe Dashboard → Webhooks: webhook entregue com sucesso (resposta 200)
   - Banco de dados do seu app: registro de pedido foi criado
   - Usuário recebeu um e-mail de confirmação

Só passe para o modo produção depois que cada etapa estiver verificada.

### Teste o Caminho de Falha Também

```
Número do cartão: 4000 0000 0000 0002
→ Sempre recusado — verifique se seu app lida corretamente com falhas de pagamento
```

-----

## Passo 8: Mude para o Modo Produção

Uma vez que o teste sandbox passar, mude para o modo produção:

1. Alterne para **Live Mode** no canto superior direito do Stripe Dashboard
1. Obtenha sua chave de API de produção (`sk_live_...`)
1. Adicione um novo endpoint de webhook no Live Mode (mesma URL, mas registrada no Live Mode)
1. Obtenha o Webhook Secret de produção (`whsec_...`)
1. Atualize as variáveis de ambiente do seu Cloudflare Workers:
   
   ```bash
   npx wrangler secret put STRIPE_SECRET_KEY
   # Insira sk_live_xxxxx
   
   npx wrangler secret put STRIPE_WEBHOOK_SECRET
   # Insira o whsec_xxxxx de produção
   ```

Após mudar, faça um pagamento real com um valor pequeno (ex: R$5) para confirmar que tudo funciona de ponta a ponta.

-----

## Usando o vibefast.app — Pule Tudo Isso

Se você está usando o vibefast.app, a boa notícia é: **todo o fluxo Stripe já está construído.**

O vibefast.app inclui:

- Lógica de criação de Checkout Session
- Handler de webhook (com verificação de assinatura e tratamento de idempotência)
- Criação automática de registro de pedido em pagamento bem-sucedido
- E-mail de confirmação de pagamento via Resend

Você não precisa implementar nenhum código acima. Você só precisa fazer uma coisa:

**Defina suas variáveis de ambiente no Cloudflare Dashboard**

Vá para Cloudflare Dashboard → seu API Worker → Settings → Variables:

```
STRIPE_SECRET_KEY = sk_test_xxxxx (teste) ou sk_live_xxxxx (produção)
STRIPE_WEBHOOK_SECRET = whsec_xxxxx
STRIPE_CURRENCY = brl (ou usd, eur, etc.)
STRIPE_PRICE_CENTS = 9900 (em centavos — 9900 = R$99,00)
```

Não precisa criar um produto no Stripe Dashboard primeiro. O vibefast.app gera o preço dinamicamente ao criar a Checkout Session.

Defina as variáveis, faça deploy, e os pagamentos estão no ar.

Percorra o fluxo sandbox (Passo 7) para confirmar que tudo funciona, então substitua com suas chaves de produção.

-----

## Erros Comuns

**Verificação de assinatura do webhook falha**  
A causa mais comum é ler o body como JSON parseado antes de passá-lo para o handler do webhook. O handler deve ler o **texto bruto do body** — se você chamar `request.json()` primeiro, a verificação de assinatura vai falhar.

**Erro na unidade de valor**  
O Stripe usa centavos, não reais ou dólares. R$10,00 deve ser passado como `1000`, não `10`.

**Não tratar retentativas de webhook**  
Se o Stripe não receber uma resposta 200, ele tenta novamente por até 3 dias. Torne seu handler de webhook idempotente — processar o mesmo evento duas vezes não deve ter efeitos colaterais. Use `stripe_session_id` como chave única no seu banco de dados para prevenir pedidos duplicados.

-----

## Resumo

O fluxo completo de pagamento Stripe:

1. Crie uma conta e complete a verificação de identidade
1. Desenvolva e teste usando o modo sandbox (chaves de teste)
1. Use Checkout Session — deixe o Stripe gerenciar a página de pagamento
1. Configure webhooks para que seu backend saiba quando os pagamentos são concluídos
1. Faça deploy para o Cloudflare, percorra o fluxo completo com cartões de teste
1. Confirme que cada etapa funciona, então mude para chaves de produção

Com o vibefast.app: defina quatro variáveis de ambiente e os pagamentos estão no ar imediatamente.

O passo mais frequentemente ignorado é o passo 4 — sem webhooks, seu app nunca fica sabendo quem pagou.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Voltar ao índice do curso](../README-pt-br.md)

Pronto para começar com um template completo Cloudflare full-stack?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, preço sobe para $199 em 1º de junho de 2026.
