# Como Discutir Projetos com IA: Pense Primeiro, Code Depois

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Atualizado:** Março de 2026  
**Tempo de leitura:** ~8 minutos

-----

## A Maioria das Pessoas Usa IA do Jeito Errado

Você encontra um problema, abre o Cursor imediatamente e diz: "Corrija esse bug para mim" ou "Adicione essa funcionalidade para mim."

A IA gera código, você cola, executa, parece funcionar. Então descobre que quebrou outra coisa, pede à IA para corrigir, quebra mais uma coisa...

Meia hora depois, você está em círculos, código cada vez mais bagunçado, você cada vez mais frustrado.

**O problema não é que a IA não é boa o suficiente, é que você pulou o passo mais importante: discutir primeiro, implementar depois.**

-----

## Por Que Discutir Primeiro?

A IA é muito boa em execução — você diz o que fazer, ela faz, e faz rápido.

Mas "executar rapidamente na direção errada" desperdiça mais tempo do que "pensar devagar e depois fazer."

Benefícios de discutir primeiro:

- **Confirmar qual é o seu problema real** — você acha que é o problema A, após a discussão descobre que é na verdade o problema B, resolver B é dez vezes mais rápido do que resolver A
- **Deixar a IA sugerir opções que você não pensou** — a IA viu toneladas de cenários similares, ela sabe quais abordagens existem e seus trade-offs
- **Evitar construir para depois derrubar** — decisões arquiteturais feitas erradas custam caro de mudar, a discussão custa quase nada

-----

## Princípio Central: Primeiro Diga "Não Programe Ainda"

Quando você quer discutir um problema, **diga à IA desde o início que você só quer discutir, não quer que ela escreva código ainda**:

```
Quero discutir um problema com você, não escreva código ainda,
aguarde até chegarmos a um consenso antes de implementar.
```

Essa frase é importante. O comportamento padrão da IA é "dar soluções," ela vai tender a gerar código diretamente. Você diz explicitamente "não programe ainda," ela vai realmente entrar no modo de discussão, te dando análise, opções, sugestões, em vez de só jogar código em você.

-----

## Três Estágios de Discussão

### Estágio 1: Declare Claramente Seu Problema

Descreva o problema completamente, não diga apenas os sintomas, diga o contexto:

```
O que estou fazendo: [qual é seu projeto, onde você está agora]
Qual problema estou enfrentando: [descrição específica, não apenas "não funciona"]
O que já tentei: [quais tentativas você fez]
O que quero alcançar: [qual você espera que seja o resultado final]
```

**Exemplo — descrição incompleta do problema:**

```
Meu login está quebrado, conserte.
```

**Exemplo — descrição completa do problema:**

```
Não programe ainda.

Estou fazendo um app de assinatura construído com VibeFast.
Problema: Após os usuários fazerem login, atualizar a página os desloga.
Já tentei: Confirmei que o JWT token gera corretamente, o cookie também está definido.
Objetivo: O estado de login deve persistir após atualização.

O que você acha que pode ser a causa? Quais são as soluções possíveis?
```

A segunda descrição permite que a IA dê um diagnóstico significativo, em vez de adivinhar uma direção e mudar coisas aleatoriamente.

-----

### Estágio 2: Deixe a IA Sugerir Opções

Não pergunte apenas "como fazer isso," pergunte "quais são as abordagens, quais são seus trade-offs":

```
# Pergunta não muito boa
"Como implementar funcionalidade de notificação de usuário?"

# Pergunta melhor
"Quero adicionar funcionalidade de notificação de usuário, não programe ainda.
Quais são as abordagens de implementação? Qual é a complexidade e trade-offs de cada uma?
Meu app atualmente usa arquitetura VibeFast (Remix + Cloudflare Workers + D1),
escala ainda é pequena, só eu mantendo."
```

A IA pode te dar três opções:

1. Versão simples: armazenar registros de notificação diretamente no DB, frontend fazendo polling
2. Versão intermediária: usar Cloudflare Queues para notificações assíncronas
3. Versão complexa: push em tempo real com WebSocket

Com essa lista, você pode tomar decisões baseadas na sua situação — você não precisa do mais complexo, precisa do que se encaixa no seu estágio atual.

-----

### Estágio 3: Confirmar Consenso Antes de Implementar

Após a discussão, antes de começar a implementação, primeiro confirme que ambos os lados têm entendimento compartilhado da mesma coisa:

```
Ok, decidi pela abordagem 1 (DB armazena notificações + polling).
Antes de escrever código, vamos confirmar nosso consenso:

1. Criar nova tabela notifications, campos são id, user_id, message, is_read, created_at
2. Adicionar endpoint GET /api/notifications, retorna notificações não lidas do usuário atual
3. Frontend faz polling a cada 30 segundos, mostra ponto vermelho quando há novas notificações
4. Após ler notificações, chamar PATCH /api/notifications/:id para marcar como lido

Esse entendimento está correto? Tem algo que não considerei?
```

Esse passo parece redundante, mas é muito valioso. Você articula o plano com suas próprias palavras, a IA pode apontar o que você perdeu — como "você esqueceu de considerar paginação quando usuários têm muitas notificações" — descobrir antes da implementação é muito mais fácil do que mudar depois.

Após confirmar que não há problemas, então diga:

```
Ótimo, agora comece a implementar. Comece pela migration do banco de dados.
```

-----

## Um Exemplo Completo de Discussão

Abaixo está um trecho de uma conversa completa em um cenário real, demonstrando como esse processo realmente parece.

-----

**Você:**

```
Não programe ainda.

Estou usando VibeFast para fazer um app vendendo produtos digitais.
Problema: Quero que compradores possam baixar repetidamente os arquivos que compraram,
mas não quero que pessoas que não pagaram acessem diretamente os links de download.

Atualmente os arquivos R2 são públicos, qualquer pessoa que saiba a URL pode baixar.

Quais métodos podem proteger esses links de download?
```

**IA:**

```
Ok, vamos discutir. Esse problema tem algumas soluções comuns:

Abordagem 1: Signed URL (links pré-assinados)
Quando usuários solicitam download, o backend primeiro verifica se eles têm registro de compra,
depois gera uma Signed URL do R2 com prazo (ex: válida por 15 minutos),
retorna essa URL para o frontend. Frontend usa essa URL diretamente para baixar.
Prós: Simples, R2 suporta nativamente. Contras: URL pode ser compartilhada (válida por 15 minutos).

Abordagem 2: Proxy de download pelo backend
Requisição de download primeiro chega à sua API Workers, API verifica identidade,
Workers lê diretamente o stream do arquivo do R2, retorna ao usuário.
Prós: Controle completo, pode rastrear contagem de downloads, limitar vezes de download.
Contras: Workers deve transmitir o arquivo inteiro, usa tempo de CPU.

Para sua situação, a abordagem 1 geralmente é suficiente. Você quer limitar as vezes de download?
```

**Você:**

```
Não preciso limitar vezes, mas quero rastrear contagem de downloads.
Abordagem 1 + registrar downloads no DB, isso funcionaria?
```

**IA:**

```
Sim, essa combinação é muito razoável. O fluxo seria:

1. Usuário clica em download → chega em POST /api/downloads/:productId
2. API verifica que o usuário comprou esse produto
3. Registra na tabela download_logs do DB (user_id, product_id, timestamp)
4. Gera Signed URL do R2 válida por 15 minutos
5. Retorna URL, frontend redireciona para download

Precisará criar nova tabela download_logs. Esse entendimento está correto?
```

**Você:**

```
Sim, funciona. Agora comece a implementar, comece pela tabela.
```

-----

Essa conversa levou cerca de cinco minutos, mas antes de começar a codar você já sabe:

- Quais abordagens existem
- Qual você escolheu e por quê
- Qual é o escopo da implementação (uma tabela + um endpoint de API)

A implementação subsequente tem objetivos claros, a IA não vai desviar, e você não vai descobrir no meio do caminho que a direção estava errada.

-----

## Quando Discutir, Quando Codar Diretamente?

Nem tudo precisa de discussão primeiro.

**Bom para discutir primeiro:**

- Decisões arquiteturais (essa funcionalidade deve ir no frontend ou backend?)
- Problemas com múltiplas abordagens (de que formas posso implementar X?)
- Você não tem certeza de onde está a raiz do problema (por que Y quebrou?)
- Mudanças com escopo de impacto maior

**Pode codar diretamente:**

- Ajustes simples e claros de UI (mudar a cor deste botão para laranja)
- Você já sabe claramente como fazer, só precisa que a IA ajude a escrever
- Adicionar uma nova página com a mesma lógica de uma página existente

Princípio geral: **quando incerto, discuta primeiro; quando certo, code diretamente.**

-----

## Resumo

Discutir primeiro, implementar depois — um dos hábitos mais valiosos a desenvolver no Vibe Coding.

Lembre-se desse processo:

1. **Diga "não programe ainda"** — deixe a IA entrar no modo de discussão
2. **Declare claramente o contexto** — o que você está fazendo, o que está enfrentando, o que tentou, o que quer alcançar
3. **Peça opções e trade-offs** — "quais são as abordagens, quais são seus trade-offs?"
4. **Confirme o consenso** — articule o plano com suas próprias palavras, deixe a IA confirmar ou complementar
5. **Então comece a implementar** — com objetivos claros, a IA pode fazer o que você realmente quer

-----

Dúvidas? Me encontre no X em [@dankopeng](https://x.com/dankopeng).  
👉 **[vibefast.app](https://vibefast.app)** — Early bird $99, preço sobe para $199 em 1º de junho de 2026.
