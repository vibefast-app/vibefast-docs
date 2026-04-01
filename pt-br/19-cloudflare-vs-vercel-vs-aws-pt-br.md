# Por Que Escolhi o Cloudflare em Vez do Vercel e da AWS

[English](../en/19-cloudflare-vs-vercel-vs-aws-en.md) · [繁中](../zh/19-cloudflare-vs-vercel-vs-aws-zh.md) · [Español](../es/19-cloudflare-vs-vercel-vs-aws-es.md) · [日本語](../jp/19-cloudflare-vs-vercel-vs-aws-jp.md) · [Português (BR)](../pt-br/19-cloudflare-vs-vercel-vs-aws-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Atualizado:** Março de 2026  
**Tempo de leitura:** ~8 minutos

-----

## Esta Não É uma Comparação Neutra

Há muitos artigos "Vercel vs AWS vs Cloudflare: A Comparação Completa" por aí. Todos terminam com "depende do seu caso de uso."

Este não é esse tipo de artigo.

Sou um solopreneur gastando meu próprio dinheiro e tempo construindo produtos. Esta é minha decisão real: **escolhi o Cloudflare, e acho que a maioria dos Vibe Coders deveria também.**

Se você quer uma análise equilibrada e neutra, este provavelmente não é para você. Se você quer saber o que alguém que está realmente usando essas plataformas pensa — continue lendo.

-----

## O Problema do Vercel Não É a Tecnologia — É o Modelo de Negócio

A experiência de desenvolvedor do Vercel é genuinamente boa. Não vou argumentar o contrário. Deploys automáticos no `git push`, URLs de preview geradas automaticamente — são detalhes atenciosos bem executados.

Mas o modelo de negócio do Vercel é construído em uma suposição: **quanto mais bem-sucedido seu app, mais você paga.**

Largura de banda de egresso custa dinheiro. Tempo de execução de funções serverless custa dinheiro. Uma vez que você excede o plano gratuito, sua conta escala com seu tráfego.

Isso não é inerentemente errado — o Vercel é uma empresa, precisa de receita. Mas para indie hackers, significa que sua curva de custos está diretamente ligada ao crescimento de usuários. O período em que seu app está começando a decolar é muitas vezes o mesmo período em que você ainda não tem receita suficiente para cobri-lo. É exatamente quando a conta começa a explodir.

O problema maior é: **o Vercel não tem banco de dados e não tem armazenamento de arquivos.**

Você precisa encontrar Supabase para seu banco de dados, AWS S3 para armazenamento de imagens, Resend para e-mail. Cada serviço que você adiciona significa outra conta, outra fatura, mais uma coisa que pode quebrar. Você acha que está usando uma plataforma — na verdade está gerenciando uma rede distribuída de serviços.

-----

## O Problema da AWS Não É o Custo — É a Complexidade

Os custos da AWS na verdade não são tão loucos como algumas pessoas afirmam. Comparado ao que o Vercel cobra em escala, a AWS é muitas vezes mais barata na prática.

Mas a AWS tem um problema diferente: **foi projetada para equipes de engenharia, não para solopreneurs.**

VPCs, Security Groups, IAM Roles, Load Balancers, Auto Scaling Groups — só para colocar um app web básico no ar exige entender uma dúzia de conceitos e configurar dezenas de opções. Qualquer configuração errada pode significar vulnerabilidades de segurança, interrupções de serviço, ou cobranças inexplicáveis.

A filosofia de design da AWS é flexibilidade máxima. Você pode controlar cada detalhe. Mas a maioria dos apps indie não precisa dessa flexibilidade — o que precisam é de "configure uma vez e nunca pense mais nisso."

A IA pode gerar configs Terraform e templates CloudFormation para você. Mas você ainda precisa entender o que essas coisas estão fazendo, caso contrário quando algo quebrar você não tem ideia de onde começar.

-----

## Qual Problema o Cloudflare Realmente Resolve?

Não escolhi o Cloudflare porque é o mais barato ou porque é o mais rápido. Escolhi porque resolve o problema que mais me importa: **me deixar colocar toda minha energia no produto, não na infraestrutura.**

**Uma conta. Um painel. Uma fatura.**

Workers roda seu código, D1 armazena seus dados, R2 armazena seus arquivos — tudo dentro do Cloudflare. Sem alternar entre quatro ou cinco plataformas, sem gerenciar quatro ou cinco conjuntos de chaves de API. Quando algo quebra, há apenas um lugar para procurar.

**Zero taxas de egresso.**

Se um milhão de usuários baixar imagens do R2, o custo de egresso é zero. Quanto mais bem-sucedido seu app se torna, mais óbvia essa vantagem fica. As taxas de egresso do Vercel e da AWS acumulam rápido com alto tráfego.

**Deploy global desde o primeiro dia.**

O Cloudflare tem 300+ nós de edge. Seu código automaticamente roda mais próximo de cada usuário. Um usuário em São Paulo se conecta a um nó próximo. Sem configuração de CDN, sem setup multi-região — simplesmente funciona.

**Plano gratuito generoso.**

Workers recebe 100.000 requisições por dia gratuitamente. D1 recebe 5 milhões de leituras por mês gratuitamente. R2 recebe 10GB de armazenamento gratuitamente. Para um novo app, você pode nunca atingir os limites. Faça upgrade quando tiver os usuários e a receita para justificar.

-----

## Limitações do Cloudflare — Você Também Deve Saber Disso

Não estou escrevendo um anúncio, então aqui está para o que o Cloudflare não é bom:

**Workers tem limites de tempo de CPU.** Plano gratuito: 10ms de tempo de CPU por requisição. Plano pago: até 30 segundos. Transcodificação de vídeo, processamento pesado de dados, inferência de modelos de IA — essas coisas não pertencem ao Workers.

**D1 é SQLite, não PostgreSQL.** Se você precisa de recursos complexos de banco de dados — joins sofisticados, stored procedures, busca full-text — o D1 pode não ser suficiente. A maioria dos apps indie nunca precisa disso, mas se sua lógica de negócios for incomumente complexa, pense bem.

**O ecossistema é relativamente novo.** O D1 ainda está amadurecendo. A funcionalidade central é estável e pronta para produção, mas se seu app tem requisitos muito específicos, verifique se são suportados antes de comprometer.

-----

## Uma Pergunta para Te Ajudar a Decidir

Você não precisa de uma árvore de decisão. Uma pergunta é suficiente:

**Você está construindo sozinho, ou com uma equipe?**

Construindo solo: escolha o Cloudflare. Seu tempo é seu recurso mais valioso. Qualquer escolha que reduza o tempo que você gasta gerenciando infraestrutura é a escolha certa.

Construindo com uma equipe: depende do seu stack. Se a equipe já está profunda em Next.js, o Vercel é ótimo. Se você tem requisitos complexos de backend, o Cloudflare ou a AWS ambos funcionam — resume-se ao background técnico da equipe.

-----

## A Conclusão

O Vercel é bom, mas sua estrutura de custos e o ecossistema de serviços fragmentado tornam-no cansativo de gerenciar como desenvolvedor indie.

A AWS é poderosa, mas sua complexidade significa que a maioria dos indie hackers gasta muito tempo em configuração e pouco tempo construindo.

O Cloudflare não é perfeito, mas me dá o que realmente quero: **um lugar para gerenciar tudo, custos previsíveis e deploy global desde o primeiro dia.**

Essa é minha escolha. Sua situação pode ser diferente. Mas se você está construindo seu primeiro produto, trabalhando solo, e não quer gastar tempo gerenciando infraestrutura — minha recomendação é: comece com o Cloudflare.

-----

**Danko Peng**  
[@dankopeng](https://x.com/dankopeng) · [vibefast.app](https://vibefast.app)

-----

👉 [Voltar ao índice do curso](../README-pt-br.md)

Pronto para começar com um template completo Cloudflare full-stack?  
👉 **[vibefast.app](https://vibefast.app)** — Early bird $99, preço sobe para $199 em 1º de junho de 2026.
