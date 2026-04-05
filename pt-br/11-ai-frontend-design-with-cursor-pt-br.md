# Criando UI Bonita com IA: Guia Completo de Prompts no Cursor

[English](../en/11-ai-frontend-design-with-cursor-en.md) · [繁中](../zh/11-ai-frontend-design-with-cursor-zh.md) · [Español](../es/11-ai-frontend-design-with-cursor-es.md) · [日本語](../jp/11-ai-frontend-design-with-cursor-jp.md) · [Português (BR)](../pt-br/11-ai-frontend-design-with-cursor-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Atualizado:** Março de 2026  
**Tempo de leitura:** ~12 minutos

-----

## Você Não Precisa Aprender Design — Precisa Descrever o Que Quer

Os resultados de frontend com IA que a maioria das pessoas obtém parecem com: "funciona, mas é feio."

Não é porque a IA não sabe fazer design. É porque as instruções não foram específicas o suficiente. "Me faça uma homepage bonita" e "Me faça uma homepage de SaaS com fundo escuro, botão CTA laranja e IBM Plex Mono para títulos" produzem resultados completamente diferentes.

Este tutorial ensina a dar o segundo tipo de instrução.

-----

## Alguns Conceitos para Conhecer Primeiro

Você não precisa estudar esses em profundidade — só o suficiente para entender o que a IA está falando.

### O Que São React e JSX?

**React** é o framework de frontend mais amplamente usado hoje. Suas páginas são construídas a partir de "componentes" individuais — cada componente é responsável por uma seção da UI.

**JSX** é a sintaxe que o React usa, que permite escrever estrutura semelhante a HTML diretamente dentro do JavaScript:

```tsx
// Isso é um componente React
export default function ProductCard({ name, price }) {
  return (
    <div className="p-4 border rounded-lg">
      <h2>{name}</h2>
      <p>R${price}</p>
    </div>
  )
}
```

As chaves `{}` contêm expressões JavaScript. Todo o resto parece HTML.

### O Que É Remix?

**Remix** é um framework full-stack construído sobre React — o framework de frontend que o VibeFast usa. Conceitos-chave:

- **loader**: uma função que busca dados do backend quando uma página carrega
- **action**: uma função que lida com ações do usuário quando um formulário é enviado
- **route**: cada arquivo mapeia para um caminho de URL — `app/routes/shop.tsx` corresponde a `/shop`

Você não precisa entender completamente esses. Quando encontrar algo confuso, peça à IA para explicar.

### O Que É Tailwind CSS?

**Tailwind** é o framework CSS que o VibeFast usa. Em vez de escrever CSS, você aplica estilo diretamente usando nomes de classe no seu HTML:

```tsx
// Sem Tailwind
<button style={{ backgroundColor: 'orange', padding: '12px 24px', borderRadius: '8px' }}>
  Comprar
</button>

// Com Tailwind
<button className="bg-orange-500 px-6 py-3 rounded-lg text-white font-semibold">
  Comprar
</button>
```

Os nomes de classe do Tailwind são intuitivos: `bg-orange-500` é fundo laranja, `px-6` é padding horizontal, `rounded-lg` são cantos arredondados. Você não precisa memorizá-los — a IA os conhece todos. Só descreva o efeito que quer.

### Termos de UI Comuns Que Você Vai Ouvir

- **Hero**: a grande seção do topo de uma página, geralmente com título, subtítulo e botão CTA
- **CTA (Call to Action)**: um botão que leva os usuários a fazer algo — "Compre Agora," "Iniciar Avaliação Gratuita"
- **Card**: um bloco de conteúdo com borda ou sombra
- **Grid**: itens organizados em colunas — como uma seção de recursos em 3 colunas
- **Responsive**: adapta corretamente a telas de celular e desktop

-----

## Como Descrever Seu Estilo de Design para a IA

Essa é a habilidade mais importante. Ao dar instruções de design, cubra essas dimensões:

### 1. Estilo Geral

```
"Tema escuro"
"Minimalista branco, muito espaço em branco"
"Estética tech com efeitos de brilho"
"Sensação quente e pessoal — adequado para uma marca individual"
```

### 2. Cores

Não diga "azul" — seja específico com nomes de cores Tailwind ou valores hex:

```
# Vago demais
"Use um tema azul"

# Específico o suficiente
"Cor primária: orange-500 (#f97316), fundo: zinc-950, texto: zinc-100"
```

Famílias de cores Tailwind comuns: `slate`, `zinc`, `neutral` (cinzas), `orange`, `blue`, `violet`, `emerald`. Cada uma tem tons de 50 a 950.

### 3. Tipografia

```
"Títulos em IBM Plex Mono (monoespaçada, sensação técnica)"
"Corpo do texto em Inter (moderno, legível)"
"Títulos em Bricolage Grotesque (sans-serif distinto)"
```

Se não tiver certeza, só diga "sugira uma combinação de fontes adequada para um produto SaaS" e a IA recomendará algo.

### 4. Detalhes dos Elementos

```
"Botões com forma de pílula (rounded-full)"
"Cards com borda sutil (border border-zinc-800)"
"Animação de hover (hover:scale-105 transition)"
"Título com acento sublinhado laranja"
```

-----

## Passo a Passo: Construindo uma Seção Hero

Vou usar o design da homepage do próprio VibeFast como exemplo para demonstrar o fluxo completo de prompts.

### Rodada 1: Estabeleça a Estrutura Básica

No Cursor, pressione `Cmd+L` e digite:

```
Crie uma seção Hero em apps/web/app/routes/_index.tsx.

Especificações de design:
- Fundo escuro: bg-zinc-950
- Layout centralizado
- Pequeno badge no topo com texto "Edge-native · Sem Next.js · Sem Vercel", borda laranja
- Título principal: grande, branco, fonte IBM Plex Mono, texto "O Template de App Web Nativo no Cloudflare"
- Subtítulo: cinza, menor, texto "Da ideia à produção em 40 minutos."
- Dois botões lado a lado: botão primário laranja "Comprar Agora — $99", botão secundário transparente com borda "Ver Demo"
- Seção inteira centralizada verticalmente, altura mínima 100vh
```

A IA gera uma estrutura JSX completa.

### Rodada 2: Refine os Detalhes

Depois de ver o output, você vai notar coisas a ajustar. Continue no Cursor:

```
Ajuste algumas coisas:
1. Tamanho da fonte do título está pequeno demais — mude para text-5xl md:text-7xl
2. Aumente o espaço entre os dois botões — mude gap-4 para gap-6
3. Adicione efeito hover ao botão primário: hover:bg-orange-400 transition-colors duration-200
4. Adicione um brilho radial laranja muito sutil no fundo do hero
```

### Rodada 3: Adicione Detalhes Decorativos

```
Adicione um padrão de grade de pontos ao fundo do hero.
Use uma cor zinc-800 muito fraca.
O objetivo é dar profundidade sem distrair.
Similar ao estilo de fundo do linear.app.
```

Três rodadas depois, você tem uma seção Hero completa com qualidade de design quase profissional.

-----

## Use Screenshots para Mostrar Sites de Referência à IA

Essa é a técnica que melhora mais drasticamente a qualidade do output da IA.

Encontre um design de site que você gosta. Tire um screenshot. Arraste diretamente para o painel de chat do Cursor. Então diga:

```
Referencie o estilo de design neste screenshot e crie um componente de card de preço similar.
Requisitos técnicos:
- Tailwind CSS
- Formato Remix JSX
- Mantenha meu tema escuro zinc-950 existente
- Substitua o conteúdo de texto com a precificação do VibeFast: $99 early bird, inclui repo privado e atualizações vitalícias
```

O Cursor tem entendimento visual — ele pode ler o design do seu screenshot e reimplementá-lo no seu stack técnico.

**Bons sites para tirar screenshots de referência:**

- [linear.app](https://linear.app) — escuro, minimalista, forte estética tech
- [vercel.com](https://vercel.com) — estilo SaaS moderno
- [stripe.com](https://stripe.com) — limpo, alta confiança
- [tailwindui.com](https://tailwindui.com) — referências de componentes diretamente

-----

## Problemas Comuns e Como Resolvê-los

### Problema 1: IA Produziu um Design Que Quebra no Celular

**Sintoma:** Fica ótimo no desktop, mas o texto é grande demais ou o layout desmorona no celular.

**Solução:**

```
O design atual tem problemas no celular. Adicione ajustes responsivos:
- Título: text-3xl no celular, text-5xl no tablet, text-7xl no desktop
- Dois botões: empilhe verticalmente no celular (flex-col), lado a lado no desktop (sm:flex-row)
- Padding horizontal: px-4 no celular, px-8 no desktop
```

### Problema 2: O Design Parece Genérico — Como Todo Outro Site Gerado por IA

**Sintoma:** Azul e branco, cards arredondados, sombras — indistinguível de mil outros apps.

**Solução:** Dê instruções de personalidade mais específicas:

```
O design atual é genérico demais. Faça esses ajustes para dar mais caráter:
1. Substitua todo o azul por laranja (orange-500)
2. Adicione tachado em "Sem Next.js" no título principal
3. Adicione um bloco de código estilo terminal mostrando os três comandos de instalação
4. Mude a fonte para IBM Plex Mono para sensação de engenharia
```

### Problema 3: Você Queria Uma Pequena Mudança Mas a IA Mudou Demais

**Sintoma:** Você disse "mude a cor do botão para laranja" e a IA reescreveu metade do componente.

**Solução:** Seja mais preciso:

```
Mude apenas esta coisa e deixe todo o resto intocado:
Mude bg-blue-500 para bg-orange-500 no elemento <button>.
Não modifique mais nada.
```

-----

## Técnicas Pequenas Que Melhoram Imediatamente a Qualidade do Design

Essas são coisas que aprendi construindo a homepage do VibeFast. Cada uma é pequena, mas combinadas fazem uma diferença perceptível.

**Adicione animações de transição**

```tsx
// Escala sutil no hover
className="hover:scale-105 transition-transform duration-200"

// Transição de cor no hover
className="hover:bg-orange-400 transition-colors duration-200"
```

**Texto com gradiente**

```tsx
className="bg-gradient-to-r from-white to-zinc-400 bg-clip-text text-transparent"
```

**Bordas de card refinadas**

```tsx
// Mais refinado que uma borda branca simples
className="border border-zinc-800 hover:border-zinc-600 transition-colors"
```

**Efeito de brilho em fundos escuros**

```tsx
// Brilho radial laranja
<div className="absolute inset-0 bg-[radial-gradient(ellipse_at_center,rgba(249,115,22,0.15),transparent_70%)] pointer-events-none" />
```

-----

## Template Completo de Prompt

Copie este template, substitua o conteúdo entre colchetes e use diretamente:

```
Crie um [nome do componente] em [caminho do arquivo].

Estilo de design:
- Tema: [escuro / claro]
- Cor primária: [ex: orange-500]
- Fundo: [ex: zinc-950]
- Tipografia: [ex: IBM Plex Mono para títulos, Inter para corpo]

Conteúdo:
- [Descreva o que este componente deve exibir]

Requisitos técnicos:
- Tailwind CSS
- Formato Remix JSX
- Inclua animações de hover
- Deve ser responsivo (exibição correta em celular e desktop)

Estilo de referência: [se tiver um site de referência, descreva ou anexe um screenshot]
```

-----

## Resumo

Obter um output de frontend bonito da IA não é sobre se a IA sabe fazer design — é sobre **se você consegue descrever claramente o que quer.**

O fluxo de trabalho:

1. **Decida uma direção de estilo** — escuro/claro, cor primária, tipografia
1. **Dê o primeiro prompt** — estrutura e especificações de design juntas
1. **Itere nos detalhes** — ajuste uma coisa de cada vez
1. **Use screenshots para acelerar** — mostre sites que você gosta para a IA
1. **Adicione os toques finais** — animações, brilhos, gradientes — pequenos detalhes fazem grande diferença

Design nunca está "pronto" — só "bom o suficiente para lançar." Chegue a 80%, publique, depois itere.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Voltar ao índice do curso](../README-pt-br.md)

Pronto para começar com um template completo Cloudflare full-stack?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, preço sobe para $199 em 1º de junho de 2026.
