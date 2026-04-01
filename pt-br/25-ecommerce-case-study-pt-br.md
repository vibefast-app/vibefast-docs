# Estudo de Caso: Construindo um E-Commerce com VibeFast

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Atualizado:** Março de 2026  
**Tempo de leitura:** ~10 minutos

-----

## O Ponto de Partida: Você Comprou o VibeFast e Quer Construir uma Loja

Este tutorial usa um cenário concreto para mostrar como é na prática: **ir de uma ideia de e-commerce a um produto funcionando com o VibeFast.**

O exemplo: um e-commerce de sabonetes artesanais — **SoapCo**. Fazer sabonetes e vender diretamente. Sem sistema complexo de estoque. Um catálogo pequeno — cerca de 20–30 SKUs a qualquer momento.

-----

## Como Isso Difere do Estudo de Caso SaaS

O tutorial anterior foi um SaaS — usuários pagam uma mensalidade para usar sua ferramenta.

E-commerce é diferente: **usuários compram produtos físicos, cada transação é única, e você precisa gerenciar produtos, pedidos e fulfillment.**

Isso muda a estrutura de dados e a lógica de negócios. Mas a base do VibeFast — auth, Stripe, Resend — funciona da mesma forma nos dois casos.

-----

## Passo 1: Clone e Coloque para Rodar

Igual ao caso SaaS — clone o repo, rode o setup seguindo a documentação de início rápido, e confirme que o app completo funciona localmente.

Você não precisa mudar nada neste passo. Você só está confirmando que o ponto de partida está sólido.

-----

## Passo 2: Escreva um Documento de Requisitos

```
Quero construir um e-commerce de sabonetes artesanais usando VibeFast.
A loja se chama SoapCo. Eu faço os sabonetes e vendo eu mesmo.
Catálogo pequeno — cerca de 20–30 SKUs.

Funcionalidades centrais (MVP):
1. Páginas de lista de produtos e detalhe (imagens, descrições de ingredientes, status de estoque)
2. Carrinho de compras (adicionar múltiplos produtos, ajustar quantidades)
3. Checkout e pagamento (Stripe, pagamento único)
4. E-mail automático de confirmação de pedido
5. Painel admin para gerenciar produtos e pedidos

Base técnica: VibeFast (Auth, Stripe e Resend já integrados)
Não nesta versão: pontos de fidelidade, avaliações de produtos, multi-idioma, códigos de desconto
```

-----

## Passo 3: Projete a Estrutura do Banco de Dados

O schema de e-commerce é ligeiramente mais complexo do que SaaS. Peça à IA para projetá-lo:

```
Não escreva nenhum código ainda.

Baseado nesses requisitos de e-commerce, projete o schema do banco de dados D1 para:
- products
- orders
- order_items

Quais colunas cada tabela precisa? Quais são os relacionamentos entre elas?
Imagens de produtos serão armazenadas no R2 — a tabela só precisa armazenar a URL da imagem.
```

A IA te dá um rascunho. Percorra:

- A tabela `products` tem `name`, `slug`, `description`, `price`, `stock`, `image_url`? Parece correto?
- O que `orders` precisa registrar? Deve armazenar um endereço de entrega?
- Quais status de pedido fazem sentido? `pending`, `paid`, `shipped`, `delivered`?

Uma vez confirmado, peça à IA para gerar o SQL e execute-o.

-----

## Passo 4: Imagens de Produtos

E-commerce precisa de imagens de produtos — a maior diferença em relação a uma construção SaaS.

A boa notícia: **o VibeFast inclui uma biblioteca de mídia completa** — upload de imagens, gerenciamento e exibição estão todos integrados. Você não precisa projetar esse fluxo você mesmo.

Ao criar ou editar um produto no painel admin, só use a biblioteca de mídia do VibeFast para selecionar ou fazer upload da imagem. Armazenar no R2, gerar uma URL, exibir no frontend — tudo tratado.

Tudo que você precisa fazer é adicionar uma coluna `image_url` à tabela `products` e conectá-la ao componente de biblioteca de mídia existente:

```
Adicione uma coluna image_url à tabela products.
Nas páginas de criar e editar produto no painel admin,
conecte o componente de biblioteca de mídia existente do VibeFast para deixar o usuário escolher uma imagem.
Salve a URL da imagem selecionada nesta coluna.
```

É isso. Sem precisar construir upload de imagem do zero.

-----

## Passo 5: Decisão de Design do Carrinho

O carrinho de compras é o núcleo de qualquer e-commerce. Duas abordagens:

**Opção A: Armazenar no navegador (localStorage/Cookie)**

- Usuários podem adicionar ao carrinho sem fazer login
- Simples — sem banco de dados necessário
- Desvantagem: carrinho é perdido se mudarem de dispositivo

**Opção B: Armazenar no banco de dados**

- Requer login para adicionar ao carrinho
- Sincroniza entre dispositivos
- Mais complexo de implementar

Para uma loja pequena como a SoapCo, a Opção A é suficiente. Diga à IA:

```
Armazene o carrinho em um cookie. Login não necessário.
Formato: um array JSON — [{productId, quantity}, ...]
Login ou detalhes de entrega só são necessários no checkout.

Não escreva nenhum código ainda — há algum problema potencial com este design?
```

Discuta primeiro, confirme a direção, depois implemente.

-----

## Passo 6: Stripe Checkout

O VibeFast já tem o Stripe integrado. O checkout de e-commerce é ligeiramente diferente de uma assinatura SaaS — é um pagamento único e o carrinho pode ter múltiplos produtos.

Diga à IA:

```
Implemente o fluxo de checkout:

1. Usuário clica em "Finalizar Compra" — conteúdo do carrinho é enviado ao backend
2. Backend valida que cada produto tem estoque suficiente
3. Criar uma Stripe Checkout Session com todos os itens do carrinho
4. Em pagamento bem-sucedido:
   - Criar registros em orders e order_items
   - Deduzir estoque de cada produto
   - Enviar e-mail de confirmação de pedido via Resend
5. Tratar resultado do pagamento via webhook

Liste o plano e quais arquivos serão afetados antes de escrever qualquer código.
```

-----

## Passo 7: Painel Admin

E-commerce precisa de uma área admin simples para gerenciar produtos e pedidos.

O VibeFast já tem a estrutura do painel admin no lugar. O que você precisa adicionar:

- Gerenciamento de produtos (criar, editar, publicar/despublicar, definir níveis de estoque)
- Lista de pedidos (ver todos os pedidos e seus status)
- Atualizar status do pedido (enviado, entregue)

Todas são operações CRUD padrão. Peça à IA para implementá-las uma por uma.

-----

## Passo 8: Branding e Design

Substitua a UI padrão do VibeFast pela identidade visual da SoapCo.

Visuais importam muito em e-commerce — especialmente fotos de produtos e qualidade geral da apresentação. Vale a pena gastar tempo extra aqui:

```
Reestilize o site para combinar com uma marca de sabonetes artesanais naturais:
- Cores primárias: branco quente (stone-50) combinado com verde escuro (green-800)
- Sensação tipográfica: elegante, natural
- Cards de produto: foco na imagem, texto mínimo
- Tom geral: artesanal, natural, qualidade — evite over-design
```

-----

## Da Ideia ao Lançamento: A Linha do Tempo

```
Dia 1:       Clone, setup, explorar a estrutura do projeto
Dia 2:       Design do schema do banco de dados, campo de URL de imagem
Dias 3–5:    Páginas de produto (lista + detalhe)
Dias 6–7:    Lógica do carrinho
Dias 8–9:    Checkout Stripe + webhook + e-mail de pedido
Dia 10:      Painel admin (produtos + pedidos)
Dias 11–12:  Branding, polimento visual, ajustes de detalhes
Dia 13:      Testar todo o fluxo de ponta a ponta, corrigir bugs
Dia 14:      Deploy, preparar para lançar
```

Duas semanas do zero ao ar. Se você tivesse que construir auth e Stripe do zero, essas duas sozinhas levariam mais de uma semana. O VibeFast pula tudo isso — as duas semanas inteiras vão para a loja em si.

-----

## Detalhes Fáceis de Perder

**Deduza o estoque após o pagamento ser confirmado — não quando o item é adicionado ao carrinho.**  
Adicionar ao carrinho é apenas intenção. Se o pagamento falhar ou o usuário abandonar o checkout, o estoque nunca deveria ter sido deduzido. Só deduza dentro do handler `checkout.session.completed` do webhook Stripe.

**O e-mail de confirmação de pedido precisa de informações suficientes.**  
Após pagar, os usuários precisam ver: número do pedido, lista de itens, valor total, prazo estimado de entrega e informações de contato. Este e-mail é o principal sinal de confiança após uma compra.

**Teste o fluxo completo de compra — não só a UI.**  
Use o cartão de teste do Stripe para percorrer o fluxo completo: adicionar ao carrinho → checkout → pagamento → confirmar que o e-mail chega → verificar que o pedido aparece no admin. Cada etapa precisa estar certa.

-----

## Lições Principais

**E-commerce é construído em confiança, não em funcionalidades.**  
Fotos claras de produtos, descrições detalhadas, fluxo de pagamento suave, e-mails de confirmação rápidos — isso importa muito mais do que ter um recurso de código de desconto.

**Comece com um catálogo pequeno e valide o fluxo.**  
No primeiro dia, coloque apenas 3–5 produtos. Confirme que a experiência completa de compra funciona sem problemas. Expanda o catálogo quando tiver confiança.

**Atendimento ao cliente importa mais do que funcionalidades.**  
Após seus primeiros clientes comprarem, entre em contato e pergunte como foi a experiência. Feedback real de clientes reais vale mais do que qualquer dado de analytics.

-----

Quer construir seu próprio produto dessa forma?  
👉 **[vibefast.app](https://vibefast.app)** — Early bird $99, preço sobe para $199 em 1º de junho de 2026.
