# Não Entre em Pânico: Usando IA para Ler Mensagens de Erro e Resolver Problemas

[English](../en/18-debug-and-errors-en.md) · [繁中](../zh/18-debug-and-errors-zh.md) · [Español](../es/18-debug-and-errors-es.md) · [日本語](../jp/18-debug-and-errors-jp.md) · [Português (BR)](../pt-br/18-debug-and-errors-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Atualizado:** Março de 2026  
**Tempo de leitura:** ~8 minutos

-----

## Quando Você Ver uma Mensagem de Erro Vermelha — Não Feche

A reação típica de iniciantes a um erro é: entrar em pânico, fechar o terminal, tentar de novo, mesmo resultado.

Mas mensagens de erro não são seu inimigo. **São sua pista mais importante.** Elas te dizem exatamente o que deu errado e por quê. Aprenda a trabalhar com elas e você resolverá problemas dez vezes mais rápido.

Mais importante: **você não precisa entender cada erro por conta própria. Só cole na IA.**

-----

## Três Tipos de Erros

### Erros de Sintaxe

O código foi escrito incorretamente — um colchete faltando, aspas não fechadas, uma palavra-chave com typo. São os mais fáceis de corrigir porque o Cursor geralmente destaca a linha do problema com um sublinhado vermelho imediatamente.

```
SyntaxError: Unexpected token '}'
```

Só diga à IA: "Há um erro de sintaxe neste código. Encontre e corrija."

### Erros de Runtime

A sintaxe está correta, mas o código quebra quando roda. Exemplos comuns:

```
TypeError: Cannot read properties of undefined (reading 'name')
```

Geralmente significa que você esperava que uma variável tivesse um valor, mas ela é `undefined` — por exemplo, uma query de banco de dados não retornou nada e você tentou acessar uma propriedade no resultado.

```
ReferenceError: env is not defined
```

Você usou uma variável que não foi declarada ou passada para o escopo atual.

### Erros de Lógica

O tipo mais difícil de encontrar. O código roda sem gerar um erro, mas o resultado está errado — preço de produto calculado incorretamente, estoque não deduzido corretamente, usuário não redirecionado para a página certa após login.

Esses exigem rastrear a execução do seu programa para encontrar onde a lógica deu errado.

-----

## Primeiro Checkpoint: Valide o Código da IA com npm run build

Após a IA escrever um trecho de código, **não corra para rodar `npm run dev` ou fazer deploy. Rode `npm run build` primeiro.**

```bash
npm run build
```

`npm run build` faz duas coisas:

1. **Verificação de tipos TypeScript** — detecta erros de tipo, variáveis indefinidas, chamadas de função incorretas
1. **Compila o código** — confirma que o projeto inteiro pode ser empacotado com sucesso

Geralmente é mais rápido do que iniciar o servidor de dev, e captura muitos problemas antes de você sequer rodar o app.

**Só siga em frente quando o build passar:**

```bash
✓ Built in 2.3s   ← isso é o que você quer ver
```

Se o build falhar, o terminal te diz exatamente qual arquivo e linha tem o problema:

```
src/routes/checkout.ts:23:18 - error TS2532: Object is possibly 'undefined'.
```

Isso é muito mais rápido do que esperar para descobrir erros em tempo de execução. Cole o erro para a IA, deixe ela corrigir o código, rode o build de novo para confirmar que passa, então continue.

**Torne isso um hábito: IA escreve código → `npm run build` → passa → `npm run dev` para testar a funcionalidade.**

-----

## O Processo Padrão Quando Você Encontra um Erro

### Passo 1: Copie a Mensagem de Erro Completa

Não tire apenas um screenshot ou anote "houve um erro." Copie o texto completo do erro, incluindo:

- O tipo de erro (TypeError, SyntaxError, etc.)
- A descrição do erro
- O arquivo e número de linha onde ocorreu (o stack trace)

```
TypeError: Cannot read properties of undefined (reading 'price')
    at handleCheckout (api/src/routes/checkout.ts:23:18)
    at Object.<anonymous> (api/src/index.ts:45:5)
```

Isso te diz: na linha 23 de `checkout.ts`, algo é `undefined` e você tentou ler sua propriedade `price`.

### Passo 2: Cole para a IA com Contexto

```
Estou construindo uma funcionalidade de checkout no Cloudflare Workers e encontrei este erro:

TypeError: Cannot read properties of undefined (reading 'price')
    at handleCheckout (api/src/routes/checkout.ts:23:18)

Aqui está meu código:
[cole o código relevante]

O que estou tentando fazer: após o usuário clicar em "Comprar", o backend busca um produto do D1,
então cria uma Stripe Checkout Session.

Explique o que esse erro significa, o que provavelmente está causando, e como corrigi-lo.
```

### Passo 3: Entenda a Explicação Antes de Pedir a Correção

Após a IA explicar o problema, certifique-se de entender o que deu errado antes de pedir para ela corrigir o código. Não diga apenas "corrija" — pergunte "por que isso aconteceu" primeiro. Dessa forma, quando você ver algo similar no futuro, saberá onde procurar.

-----

## console.log: A Ferramenta de Debug Mais Simples

Quando você não tem certeza do valor de uma variável, a abordagem mais direta é imprimi-la com `console.log`:

```typescript
export async function handleCheckout(request: Request, env: Env) {
  const { productId } = await request.json()
  
  const product = await env.DB.prepare(
    'SELECT * FROM products WHERE id = ?'
  ).bind(productId).first()
  
  // Adicione esta linha para ver o que product realmente contém
  console.log('product:', product)
  
  // Se product for null ou undefined, a próxima linha vai gerar um erro
  const price = product.price
}
```

### Visualizando console.log no Cloudflare Workers

Os logs do Cloudflare Workers não aparecem nas ferramentas de desenvolvedor do navegador. Você precisa visualizá-los pelo Cloudflare:

**Método 1: Tempo real (melhor durante o desenvolvimento)**

```bash
npx wrangler tail
```

Após rodar isso, todos os logs do seu Worker aparecem diretamente no seu terminal em tempo real.

**Método 2: Dashboard**  
Cloudflare Dashboard → Workers & Pages → seu Worker → Logs

-----

## Técnicas Avançadas de Debug com IA

### Peça à IA para Adicionar Logs de Debug

```
Esta função não está produzindo o resultado correto. Adicione instruções console.log
nas etapas-chave para eu poder rastrear o valor de cada variável:

[cole seu código]
```

### Peça à IA para Explicar um Stack Trace

Um stack trace mostra a cadeia de chamadas de função que levou ao erro. É difícil de ler no início — só cole para a IA:

```
Explique o que este stack trace está me dizendo. Onde o erro se originou?

[cole o stack trace completo]
```

### Peça à IA para Explicar Código que Você Não Entende

Quando você encontrar código que não faz sentido, não pule:

```
Explique o que este código faz em linguagem simples.
Particularmente não entendo estas linhas:

[cole o código]
```

-----

## Os Erros Mais Comuns do Cloudflare Workers

### `env is not defined` ou `env.DB is not defined`

Você usou `env` em algum lugar, mas a função não recebe `env` como parâmetro.

```typescript
// ❌ Esta função não tem acesso a env
async function getProduct(productId: string) {
  const product = await env.DB.prepare(...) // de onde vem env?
}

// ✅ Passe env explicitamente
async function getProduct(productId: string, env: Env) {
  const product = await env.DB.prepare(...)
}
```

### `Cannot read properties of null`

Uma query de banco de dados não encontrou registro correspondente e retornou `null`, mas você acessou uma propriedade nela diretamente.

```typescript
const product = await env.DB.prepare('SELECT * FROM products WHERE id = ?')
  .bind(productId).first()

// ❌ Se product for null, isso gera um erro
const price = product.price

// ✅ Verifique primeiro
if (!product) {
  return Response.json({ error: 'Produto não encontrado' }, { status: 404 })
}
const price = product.price
```

### Deploy Bem-Sucedido Mas Funcionalidade Não Está Funcionando

Primeiro confirme que você está vendo a versão mais recente — o Cloudflare tem cache, e às vezes as mudanças levam alguns segundos para propagar após o deploy.

Use curl para verificar se sua API está retornando o que você espera:

```bash
curl -s https://seu-app.com/api/products | jq
```

Se isso parecer correto, rode `wrangler tail` e procure por mensagens de erro nos logs.

-----

## A Abordagem Mais Direta: Tire um Screenshot e Mostre para a IA

Às vezes descrever o problema em texto é tedioso — "algo na página está na posição errada," "o botão não responde quando clicado," "o erro aparece em um lugar estranho."

**Só tire um screenshot e cole diretamente no chat de IA do Cursor.**

Screenshots podem mostrar:

- A tela de erro como aparece no navegador
- Output de erro do terminal (mais completo do que texto copiado — cores e formatação são preservadas)
- Resultados de UI que não correspondem ao esperado
- Logs de erro no Cloudflare Dashboard

Diga à IA:

```
Aqui está meu problema — screenshot abaixo:
[cole o screenshot]

O que eu esperava: [descreva]
O que estou realmente vendo: [descreva]
Me ajude a descobrir o que está errado.
```

Um screenshot é mais preciso do que uma descrição em texto. A IA pode ver exatamente o que você está vendo sem depender da sua tradução.

Especialmente útil para:

- **Problemas de layout**: um screenshot supera descrever "algo foi para o lado errado"
- **Erros de terminal**: screenshot preserva destaque de cores e o stack trace completo melhor do que texto simples
- **Logs do Cloudflare**: screenshot do painel de logs inteiro para que a IA possa ver timestamps e o erro completo no contexto

-----

## A Mentalidade Correta

Encontrar um erro não significa que você falhou. Significa que você está aprendendo.

Todo erro tem uma causa. Toda causa pode ser encontrada. Você não precisa descobrir todos os detalhes sozinho — a IA é seu parceiro de debug. Cole o erro e o código, e ela pode ajudar a rastrear a maioria dos problemas.

**O fluxo correto quando você encontra um erro: copie o erro completo → cole para a IA → entenda a causa → corrija → continue.**

Não entre em pânico. Não feche o terminal. Não tente a mesma coisa esperando um resultado diferente.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Voltar ao índice do curso](../README-pt-br.md)

Pronto para começar com um template completo Cloudflare full-stack?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, preço sobe para $199 em 1º de junho de 2026.
