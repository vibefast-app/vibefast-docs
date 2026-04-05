# Analytics e Tráfego: Entendendo o Que Seus Usuários Estão Fazendo

[English](../en/23-analytics-and-user-tracking-en.md) · [繁中](../zh/23-analytics-and-user-tracking-zh.md) · [Español](../es/23-analytics-and-user-tracking-es.md) · [日本語](../jp/23-analytics-and-user-tracking-jp.md) · [Português (BR)](../pt-br/23-analytics-and-user-tracking-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Atualizado:** Março de 2026  
**Tempo de leitura:** ~6 minutos

-----

## Você Precisa de Dados para Tomar Boas Decisões

"De onde vêm meus usuários?" "Qual página tem mais visitas?" "Por que o tráfego caiu de repente na terça-feira?"

Sem dados, você está adivinhando. Acerte e foi sorte. Erre e você desperdiçou tempo otimizando a coisa errada.

A boa notícia: se seu app roda no Cloudflare, você já tem dados básicos de tráfego — e não precisou instalar nada para tê-los.

-----

## Cloudflare Analytics: Gratuito, Zero Configuração, Já Rodando

No momento em que seu app é implantado no Cloudflare Workers, o Cloudflare começa automaticamente a registrar todos os dados de tráfego.

Vá ao [Cloudflare Dashboard](https://dash.cloudflare.com), clique no seu Worker e selecione a aba "Analytics". Você vai ver:

- **Volume de requisições**: quantas requisições chegam por dia e por hora
- **Distribuição de códigos de status**: a divisão entre 200s, 404s, 500s — identifique anomalias de relance
- **Tempo de resposta**: velocidade média de resposta, se está ficando mais lento
- **Tráfego por região**: de quais países e regiões seus usuários estão vindo

Esses dados são:

- Completamente gratuitos
- Não exigem nenhuma mudança no seu código
- Amigáveis à privacidade — sem rastreamento pessoal, sem necessidade de banner de consentimento de cookies
- Em tempo real — sem atrasos de várias horas como algumas outras ferramentas

Para um app novo, isso é tudo que você precisa.

-----

## O Que o Cloudflare Analytics Pode Te Dizer?

### Alguém está aparecendo?

A pergunta mais básica. O gráfico de volume de requisições diárias mostra imediatamente sua tendência de tráfego — se está crescendo, quando caiu, se um post que você publicou realmente gerou tráfego.

### Algo está quebrado?

A distribuição de códigos de status é um indicador prático de saúde. Um pico repentino em erros 500 significa que algo está errado no backend. Muitos 404s significa que alguns links morreram. Verifique este gráfico após cada deploy para confirmar que você não introduziu novos problemas.

### De onde vêm seus usuários?

A distribuição regional mostra onde está sua principal audiência, o que te ajuda a decidir em quais mercados focar para promoção.

-----

## Cloudflare Web Analytics: Rastreie Visualizações de Página Frontend

O Cloudflare Workers Analytics registra todas as requisições HTTP, incluindo chamadas de API. Se você quer rastrear visualizações de páginas frontend especificamente, use o **Cloudflare Web Analytics** — também gratuito, também sem cookies necessários.

Se seu domínio já foi adicionado ao Cloudflare, o Web Analytics é habilitado automaticamente sem nenhuma configuração necessária e sem script para adicionar ao seu código.

Vá ao Cloudflare Dashboard → selecione seu domínio → Web Analytics, e você vai ver:

- Visualizações de página por página
- Contagem de visitantes e tempo na página
- Fontes de tráfego (direto, busca, redes sociais)
- Tipos de dispositivo (mobile vs desktop)

-----

## Quais Métricas Acompanhar Primeiro

Dados demais é seu próprio problema — você acaba em "paralisia analítica," olhando para números sem saber o que fazer com eles.

**Quando você lança pela primeira vez, acompanhe apenas três números:**

1. **Volume de requisições diárias** — alguém está vindo? A tendência está subindo ou descendo?
1. **Taxa de erros** — algum pico em erros 500? O último deploy introduziu novos problemas?
1. **Tráfego por região** — de onde vêm principalmente seus usuários? Corresponde ao seu mercado-alvo?

Quando tiver tráfego estável, comece a se aprofundar nos dados em nível de página.

-----

## Analytics Integrado do VibeFast: Direto no Dashboard

Se você está usando o VibeFast, analytics de tráfego já está integrado.

Faça login no painel admin do VibeFast e o Dashboard da homepage mostra gráficos de tráfego diretamente — sem precisar abrir o Cloudflare Dashboard, sem configuração necessária. Os dados de tráfego do seu app estão bem na sua frente.

Para um app web em estágio inicial, isso é mais do que suficiente. Você pode:

- Ver de relance se a contagem de visitantes de hoje está acima ou abaixo de ontem
- Identificar quais páginas estão recebendo mais tráfego
- Confirmar se uma promoção ou post realmente trouxe visitantes para seu site

-----

## Se Você Precisar de Análises Mais Profundas Depois

Os dados integrados do Cloudflare são suficientes para começar. À medida que seu app cresce e você quer rastrear comportamentos de usuário mais granulares — em quais botões as pessoas clicam, onde elas desistem em um fluxo de compra — é quando ferramentas de terceiros se tornam dignas de consideração.

Mas isso é um problema para depois. Construa o app primeiro. Consiga o tráfego primeiro. Então pense em analytics mais profundo.

-----

## Resumo

O objetivo do analytics não é coletar dados — é tomar melhores decisões com dados.

A ordem de início:

1. **Cloudflare Workers Analytics**: gratuito, zero configuração, disponível no momento do deploy — comece com volume de requisições e taxa de erros
1. **Cloudflare Web Analytics**: habilitado automaticamente para qualquer domínio no Cloudflare — visualizações de página e fontes de tráfego sem configuração
1. **Dashboard VibeFast**: se você está usando o VibeFast, gráficos de tráfego estão direto na homepage do admin

Decisões baseadas em dados não são só para grandes empresas. Saber o que seus usuários estão realmente fazendo supera adivinhar toda vez.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Voltar ao índice do curso](../README-pt-br.md)

Pronto para começar com um template completo Cloudflare full-stack?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, preço sobe para $199 em 1º de junho de 2026.
