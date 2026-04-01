# Como Criar um Plano de Trabalho para Vibe Coding: Da Análise de Requisitos ao Modo Plan da IA

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Atualizado:** Março de 2026  
**Tempo de leitura:** ~10 minutos

-----

## Como a Maioria das Pessoas Começa o Vibe Coding

Tem uma ideia, abre o Cursor, diz "me faça um app de XX."

A IA começa a gerar, você começa a modificar, modifica e descobre que a direção está errada, derruba e recomeça. Gera de novo, modifica de novo, errado de novo.

Três dias depois, você tem um monte de funcionalidades pela metade, nenhuma completa, e também não sabe o que fazer a seguir.

**O problema não é a IA, é que você não tem um plano.**

-----

## Por Que Vibe Coders Precisam Especialmente de Planos?

Engenheiros tradicionais têm anos de treinamento, sabem como decompor problemas, estimar carga de trabalho, priorizar.

A vantagem dos Vibe Coders é a velocidade — mas velocidade também é uma armadilha. A IA faz você "fazer qualquer coisa rapidamente," então você facilmente corre muito rápido na direção errada.

O propósito do plano não é te desacelerar, é garantir que sua velocidade seja usada no lugar certo.

-----

## Passo 1: Escreva um Documento de Requisitos

Antes de abrir o Cursor, primeiro passe 30 minutos escrevendo um documento de requisitos.

O documento de requisitos é o primeiro documento do projeto inteiro de Vibe Coding — ele existe antes de você escrever qualquer linha de código. Este documento é a base de consenso entre você e a IA; toda vez que você iniciar uma nova tarefa depois, pode colá-lo para a IA, ela sabe o contexto completo do projeto, sem precisar re-explicar toda vez.

### Não Sabe Como Escrever? Deixe a IA Rascunhar

Se é a primeira vez que você faz um produto, pode não saber como transformar ideias vagas na sua cabeça em um documento completo. Isso é completamente normal — só deixe a IA ajudar a rascunhar.

Digamos que você comprou o template VibeFast e quer fazer um e-commerce vendendo sabonetes artesanais. Você tem uma ideia vaga na cabeça, mas não sabe como organizá-la em um documento.

Abra o Claude ou o ChatGPT, declare diretamente sua ideia:

```
Quero usar o template VibeFast (Remix + Cloudflare full-stack)
para fazer um e-commerce vendendo sabonetes artesanais.
Faço sabonetes eu mesmo, quero vender online, clientes podem pedir, pagar, receber notificações de envio.
O estoque não é grande, cada vez cerca de 20–30 itens.

Por favor me ajude a escrever um documento de requisitos, incluindo:
1. Quem são os usuários-alvo, quais são suas dores
2. Funcionalidades centrais do MVP (máximo cinco)
3. Stack técnico (baseado na arquitetura existente do VibeFast)
4. O que esta versão não vai fazer
5. Lista de tarefas de desenvolvimento, dividida em camadas base/central/polimento

Não escreva código ainda, apenas escreva o documento.
```

A IA pode gerar um rascunho assim:

```
# Documento de Requisitos do E-commerce de Sabonetes Artesanais

## Usuários-Alvo
- Quem são: Consumidores que gostam de sabonetes artesanais naturais, 25–45 anos, se importam com ingredientes e qualidade
- Suas dores: Não conseguem encontrar canais de compra de sabonetes artesanais com garantia de qualidade e ingredientes transparentes
- O que querem: Navegação simples, pedido seguro, saber o status do pedido

## Funcionalidades Centrais do MVP
1. Páginas de lista de produtos e detalhe do produto
2. Carrinho e checkout (pagamento Stripe)
3. Gerenciamento de pedidos (clientes podem verificar status do pedido)
4. Envio automático de e-mail de notificação após envio
5. Backend admin para gerenciar produtos e pedidos

## Stack Técnico (Baseado no VibeFast)
- Frontend/Backend: Arquitetura existente do VibeFast (Remix + Cloudflare Workers)
- Banco de dados: D1 (produtos, pedidos, usuários)
- Armazenamento de imagens: R2 (imagens de produtos)
- Pagamento: Stripe (já integrado no VibeFast)
- E-mail: Resend (já integrado no VibeFast)

## Esta Versão Não Vai Fazer
- Sistema de pontos de membro
- Funcionalidade de avaliação de produto
- Múltiplos idiomas
- App mobile

## Lista de Tarefas

### Camada 1 — Base
- [ ] Criar tabela products
- [ ] Criar tabelas orders e order_items
- [ ] Configurar R2 para imagens de produtos

### Camada 2 — Funcionalidades Centrais
- [ ] Páginas de lista e detalhe de produtos
- [ ] Lógica do carrinho
- [ ] Fluxo de checkout Stripe
- [ ] E-mail de confirmação de pedido
- [ ] Gerenciamento de produtos e pedidos no admin

### Camada 3 — Polimento
- [ ] Lazy loading de imagens de produtos
- [ ] Otimização da página de status do pedido
- [ ] Meta tags SEO
```

Após receber o rascunho, **leia linha por linha, não aceite apressadamente**:

- Não entende algo, pergunte: "O que é a tabela order_items? Por que separada de orders?"
- Não gosta de algo, diga: "Não preciso de sistema de membro, clientes fazem checkout sem cadastro, mude para guest checkout"
- Falta algo, adicione: "Quero adicionar funcionalidade de gerenciamento de estoque, cada produto deve ter limite de quantidade"
- Incerto sobre algo, discuta: "As notificações de envio devem usar e-mail ou SMS? Quais são os prós e contras de cada um?"

Vá e volte algumas rodadas, até você olhar para este documento e sentir "sim, é isso que quero construir" — só então está completo.

**Este documento é seu, não da IA.** A IA ajuda a rascunhar, mas cada decisão precisa do seu entendimento e aprovação.

-----

## Passo 2: Divida Requisitos em Tarefas Específicas

Após o documento de requisitos estar escrito, divida cada funcionalidade em tarefas específicas de desenvolvimento.

**Tarefas não específicas o suficiente:**

```
- Fazer páginas de produto
- Fazer checkout
```

**Tarefas específicas o suficiente:**

```
- Criar tabela products (name, description, price, stock, image_url)
- Criar tabelas orders e order_items
- Implementar endpoint GET /api/products (lista de produtos)
- Implementar endpoint GET /api/products/:slug (detalhes do produto)
- Implementar endpoint POST /api/checkout (criar sessão Stripe)
- Criar UI da página de lista de produtos (/shop)
- Criar UI da página de detalhe do produto (/shop/:slug)
```

Quanto mais específicas as tarefas, mais fácil estimar "quanto tempo isso vai levar," e também mais fácil dizer à IA o que fazer.

-----

## Passo 3: Priorize

Divida as tarefas em três camadas:

**Camada 1 — Base (faça primeiro)**  
Sem essas, outras funcionalidades não podem rodar:

- Schema do banco de dados
- Sistema de autenticação
- Estrutura básica do projeto

**Camada 2 — Funcionalidades Centrais (faça depois)**  
As principais funcionalidades do seu MVP:

- Fluxo de pagamento
- Páginas principais do usuário
- Backend admin

**Camada 3 — Polimento (faça por último)**  
Torna o produto melhor, mas não é essencial:

- Animações e micro-interações
- Otimização de SEO
- Design de páginas de erro

Sempre comece pela Camada 1. Muitas pessoas começam pela Camada 3 (ajustando cores de botões, adicionando animações), resultado é a base ainda não construída.

-----

## Passo 4: Use o Modo Plan da IA

Agora você tem o documento de requisitos e a lista de tarefas, pode começar a usar a IA.

Mas não diga diretamente à IA para começar — primeiro use o **modo Plan**.

### O Que É o Modo Plan?

O Cursor tem uma funcionalidade chamada **Plan** (selecione "Plan" no modo Chat). No modo Plan, você descreve o que quer fazer, a IA vai:

1. Analisar seus requisitos
2. Listar o que planeja fazer, quais arquivos modificar
3. **Aguardar sua confirmação antes de executar**

A diferença de dizer diretamente à IA para começar é: você pode ver o "plano" da IA antes que ela faça qualquer coisa, confirmar se a direção está certa.

### Como Usar o Modo Plan

No painel Chat do Cursor, mude para o modo **Plan**, insira:

```
Quero implementar as páginas de lista e detalhe de produtos do e-commerce de sabonetes artesanais.

Contexto:
- Este é um app de e-commerce baseado em VibeFast (Remix + Cloudflare Workers + D1)
- D1 já tem tabela products, campos: id, name, slug, description, price, stock, image_url
- Imagens de produtos armazenadas no R2

O que fazer:
1. GET /api/products — retornar todos os produtos em estoque (stock > 0)
2. GET /api/products/:slug — retornar detalhes de um único produto
3. Criar página de lista de produtos /shop, exibir imagens, nomes, preços
4. Criar página de detalhe do produto /shop/:slug, exibir informações completas e botão adicionar ao carrinho

Por favor liste primeiro seu plano e quais arquivos você vai modificar, não comece ainda.
```

A IA vai responder com um plano assim:

```
Meu plano:

1. Adicionar apps/api/src/routes/products.ts
   - GET /products: buscar todos os produtos com stock > 0
   - GET /products/:slug: buscar produto único

2. Adicionar apps/web/app/routes/shop._index.tsx
   - loader chama API de produtos
   - Exibir grade de produtos, cada card tem imagem, nome, preço

3. Adicionar apps/web/app/routes/shop.$slug.tsx
   - loader chama API products/:slug
   - Exibir informações completas do produto
   - Botão adicionar ao carrinho (UI primeiro, lógica do carrinho no próximo passo)

4. Adicionar apps/web/app/components/ProductCard.tsx
   - Componente de card de produto reutilizável

Confirmar que o plano está ok?
```

Após revisar e confirmar que não há problemas, então diga "ok, comece a implementar."

Mas "confirmar que não há problemas" não é dar uma olhada casual e concordar — esta etapa, como o documento de requisitos, precisa de atenção séria.

### O Mindset Central do Modo Plan: Planos São para Revisar, Não para Aprovar

Muitas pessoas veem a IA listar um plano e habitualmente dizem "bom, vamos começar" — porque o plano parece razoável e você está ansioso para ver progresso.

Mas planos são listados para você **revisar**, não para você **carimbar aprovação**.

Várias vezes disse à IA para ir direto, ela modificou arquivos que eu não esperava, ou usou uma arquitetura que eu não gostava, descobri depois de terminar. O modo Plan te permite descobrir esses problemas antes da IA começar — mas a premissa é que você realmente leia com cuidado, realmente faça perguntas.

Trate a revisão de planos como uma sessão de trabalho independente, não se apresse. Esses dez minutos valem a pena.

-----

## Um Ritmo de Trabalho Completo

Juntando tudo acima, seu ritmo de trabalho é aproximadamente assim:

**Ao iniciar novo projeto (uma vez)**

1. Escreva o documento de requisitos (30 minutos)
2. Divida em tarefas específicas (30 minutos)
3. Priorize Camadas 1/2/3 (10 minutos)

**Ao começar o trabalho cada dia**

1. Olhe a lista de tarefas, escolha 1–3 tarefas para fazer hoje
2. Para partes incertas, primeiro discuta com "não programe ainda" (consulte artigo anterior)
3. Após confirmar a direção, use o modo Plan para a IA listar o plano
4. Confirme o plano, comece a implementar
5. Teste localmente, se não houver problemas então commit + push

**Revisão semanal**

1. Quais tarefas foram completadas?
2. Quais travaram? Por quê?
3. Algum lugar precisando ajuste de prioridade?

-----

## Template de Documento de Requisitos

Você pode copiar diretamente este template para começar:

```markdown
# Documento de Requisitos do [Nome do Projeto]

## Usuários-Alvo
- Quem são:
- Suas dores:
- O que querem:

## Funcionalidades Centrais (MVP, máximo cinco)
1. 
2. 
3. 
4. 
5. 

## Stack Técnico
- Frontend:
- Backend:
- Banco de dados:
- Pagamento:
- E-mail:

## Esta Versão Não Vai Fazer
- 
- 
- 

## Lista de Tarefas

### Camada 1 — Base
- [ ] 
- [ ] 

### Camada 2 — Funcionalidades Centrais
- [ ] 
- [ ] 

### Camada 3 — Polimento
- [ ] 
- [ ] 
```

Gastar 30 minutos preenchendo este template vai economizar muito tempo de "descobrir direção errada no meio do caminho" depois.

-----

## Resumo

A vantagem de velocidade do Vibe Coding precisa de planos para garantir a direção correta:

1. **Escreva o documento de requisitos** — quem são os usuários, o que fazer, o que não fazer
2. **Divida em tarefas específicas** — mais específico melhor, conveniente para dizer à IA o que fazer
3. **Priorize Camadas 1/2/3** — sempre construa a base primeiro
4. **Use o modo Plan** — faça a IA listar o plano primeiro, você confirma e então implementa
5. **Escolha 1–3 tarefas diariamente** — passos pequenos rápidos, todo dia tem coisas completadas

Planos não são restrições, eles garantem que sua velocidade seja usada no lugar certo.

-----

Dúvidas? Me encontre no X em [@dankopeng](https://x.com/dankopeng).  
👉 **[vibefast.app](https://vibefast.app)** — Early bird $99, preço sobe para $199 em 1º de junho de 2026.
