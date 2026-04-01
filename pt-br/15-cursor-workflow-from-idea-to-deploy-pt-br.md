# Como Uso o Cursor para Levar uma Funcionalidade da Ideia ao Deploy

[English](../en/15-cursor-workflow-from-idea-to-deploy-en.md) · [繁中](../zh/15-cursor-workflow-from-idea-to-deploy-zh.md) · [Español](../es/15-cursor-workflow-from-idea-to-deploy-es.md) · [日本語](../jp/15-cursor-workflow-from-idea-to-deploy-jp.md) · [Português (BR)](../pt-br/15-cursor-workflow-from-idea-to-deploy-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Atualizado:** Março de 2026  
**Tempo de leitura:** ~10 minutos

-----

## Um Processo Real de Desenvolvimento de Funcionalidade

Vou usar um exemplo concreto para demonstrar: **adicionar a funcionalidade "alerta de estoque baixo" em um e-commerce de sabonetes artesanais** — nas páginas de detalhe do produto, quando o estoque for 3 ou menos, exibir "Apenas X restantes" em texto vermelho.

Da ideia ao deploy, o processo todo levou cerca de 20 minutos.

-----

## Passo 1: Definir os Requisitos Claramente (5 minutos)

O maior problema que muitas pessoas têm ao usar ferramentas de IA: os requisitos não são definidos claramente o suficiente, os resultados da IA ficam desviando, depois de ir e vir várias vezes é mais cansativo do que escrever você mesmo.

Antes de começar, pense em três coisas:

**O que quero:** Na página de detalhe do produto, quando estoque <= 3, exibir "Apenas X restantes" acima do botão adicionar ao carrinho em texto vermelho pequeno.

**Onde mudar:** Arquivo de rota da página de detalhe do produto (`/shop/:slug`)

**Lógica:** Julgue pelo campo `stock` retornado pela API, exiba apenas quando <= 3, quando for 0 mostre "Esgotado" e desative o botão.

-----

## Passo 2: Diga ao Cursor (3 minutos)

Abra o Cursor, pressione `Cmd+L` para abrir o chat, insira:

> "Na página de detalhe do produto (`/shop/:slug`), quando o campo `stock` do produto for <= 3, exiba 'Apenas X restantes' em texto vermelho pequeno acima do botão adicionar ao carrinho. Quando o estoque for 0, exiba 'Esgotado' e defina o botão como desabilitado. Use Tailwind CSS."

O Cursor vê toda a estrutura do projeto, sabe onde está esse arquivo, o código gerado funciona diretamente.

-----

## Passo 3: Revise o Código Gerado pela IA (2 minutos)

O Cursor gerou algo assim:

```typescript
// Lógica de status de estoque
const isOutOfStock = product.stock === 0
const isLowStock = product.stock > 0 && product.stock <= 3
```

```tsx
{/* Alerta de estoque */}
{isLowStock && (
  <p className="text-sm text-red-500 mb-2">
    Apenas {product.stock} restantes
  </p>
)}
{isOutOfStock && (
  <p className="text-sm text-red-500 mb-2">Esgotado</p>
)}

{/* Botão adicionar ao carrinho */}
<button
  disabled={isOutOfStock}
  className={`w-full py-3 rounded-lg font-semibold ${
    isOutOfStock
      ? 'bg-zinc-300 text-zinc-500 cursor-not-allowed'
      : 'bg-orange-500 text-white hover:bg-orange-400'
  }`}
>
  {isOutOfStock ? 'Esgotado' : 'Adicionar ao Carrinho'}
</button>
```

Parece correto, lógica clara, sem problemas óbvios.

-----

## Passo 4: Testar Localmente (5 minutos)

```bash
npm run dev
```

Abra a página de detalhe do produto, confirme:

- Estoque > 3: exibição normal do botão, sem alerta
- Estoque <= 3: "Apenas X restantes" em vermelho aparece
- Estoque = 0: exibe "Esgotado", botão cinza e não clicável

Às vezes os estilos gerados pela IA diferem ligeiramente das expectativas. Eu digo diretamente ao Cursor: "O cinza do botão esgotado está claro demais, mude para zinc-400," ele modifica diretamente.

-----

## Passo 5: Deploy (2 minutos)

```bash
npm run deploy
```

Aguarde o deploy completar, abra a URL de produção para confirmar que a funcionalidade está no ar.

Processo completo: **ideia → no ar, 20 minutos.**

-----

## Hábitos Que Tornam as Ferramentas de IA Mais Eficazes

Da minha experiência de mais de um ano fazendo vibe coding, alguns hábitos que melhoram a qualidade do output da IA:

**Diga "em qual arquivo"**  
Não diga apenas "adicione um alerta de estoque para mim," diga "adicione na rota da página de detalhe do produto, o arquivo é `shop.$slug.tsx`." O Cursor pode ver o projeto inteiro, mas dar a ele um ponto de partida claro torna o output mais preciso.

**Explique "por que fazer assim"**  
"Português calcula a 300 palavras por minuto" é melhor do que "calcular tempo de leitura." Informações de contexto deixam a IA fazer escolhas mais apropriadas.

**Mude uma coisa de cada vez**  
Não diga de uma vez "adicione tempo de leitura, adicione botão de compartilhar, mude o estilo do título." Divida em três requisições, confirme que cada uma está ok antes de continuar. Mudar múltiplas coisas simultaneamente, quando problemas ocorrem é difícil identificar onde.

**Pergunte sobre código que não entende**  
"O que esse `Math.ceil` está fazendo?" Pergunte diretamente no Cursor, ele vai explicar. Você não precisa entender completamente cada linha, mas deve ter entendimento básico da lógica-chave, caso contrário quando problemas ocorrerem depois você não vai saber onde procurar.

**Teste localmente antes de fazer deploy**  
Execute `npm run dev` localmente uma vez, confirme que não há problemas antes de `npm run deploy`. Descobrir problemas depois de fazer deploy em produção, rollback é mais trabalhoso.

-----

## Que Funcionalidades Se Encaixam Nesse Fluxo?

**Muito adequado:**

- Adicionar e modificar componentes de UI
- Novos endpoints de API
- Queries de banco de dados
- Lógica de validação de formulário
- Ajustes de estilo

**Precisa de mais cautela:**

- Lógica envolvendo pagamentos (webhooks Stripe, cálculos de valor)
- Modificações relacionadas à autenticação
- Mudanças de banco de dados que afetam estrutura de dados existente

Para o último, o código gerado pela IA pode ser um ponto de partida, mas revise com mais cuidado, teste manualmente cada caso extremo quando necessário.

-----

## Resumo

Fluxo central do Vibe Coding:

```
1. Pense nos requisitos (o que você quer, onde, como calcular)
2. Dê instruções claras à IA (diga o caminho do arquivo, diga a lógica de negócios)
3. Revise o output da IA, julgue se está correto
4. Teste localmente
5. Faça deploy
```

Ponto mais importante: **Você é o diretor, a IA é o executor.** Em cada passo você está julgando a direção, a IA está implementando os detalhes.

-----

**Danko Peng**  
[@dankopeng](https://x.com/dankopeng) · [vibefast.app](https://vibefast.app)

-----

👉 [Voltar ao índice do curso](../README-pt-br.md)

Pronto para começar com um template completo Cloudflare full-stack?  
👉 **[vibefast.app](https://vibefast.app)** — Early bird $99, preço sobe para $199 em 1º de junho de 2026.
