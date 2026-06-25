# Começando com Vibe Coding: Configurando Seu Ambiente de Desenvolvimento do Zero

[English](../en/02-how-to-setup-vibecoding-environment-en.md) · [繁中](../zh/02-how-to-setup-vibecoding-environment-zh.md) · [Español](../es/02-how-to-setup-vibecoding-environment-es.md) · [日本語](../jp/02-how-to-setup-vibecoding-environment-jp.md) · [Português (BR)](../pt-br/02-how-to-setup-vibecoding-environment-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Atualizado:** Março de 2026  
**Tempo de leitura:** ~10 minutos

-----

## Siga Este Guia e Seu Ambiente Estará Pronto

Muitas pessoas travam nessa etapa — não porque seja difícil demais, mas porque não sabem o que fazer nem em que ordem.

Este guia percorre cada pré-requisito, um passo de cada vez. Ao final, você terá um ambiente de Vibe Coding completo configurado, com o vibefast.app já implantado e rodando no Cloudflare.

O que você precisa instalar: Node.js, Git, Cursor. O que você precisa fazer: clonar o repositório, rodar `npm install`, rodar `npm run setup`. É isso.

-----

## Passo 1: Instalar o Node.js

Node.js é o runtime que permite ao seu computador executar JavaScript. Sem ele, `npm` e `npx` não funcionam.

Acesse [nodejs.org](https://nodejs.org) e baixe a **versão LTS** (Long Term Support).

Após instalar, abra seu terminal e verifique:

```bash
node --version
# Você deve ver algo como v22.0.0

npm --version
# Você deve ver algo como 10.0.0
```

Se aparecerem números de versão, a instalação foi bem-sucedida.

-----

## Uma Nota Rápida Sobre o Terminal

Se você não está familiarizado com o terminal (a janela preta), não se intimide.

**Mac:** Pressione `Cmd + Space`, pesquise "Terminal" e abra.  
**Windows:** Pesquise "PowerShell" ou instale o [Windows Terminal](https://aka.ms/terminal).

O terminal é apenas um lugar onde você se comunica com seu computador usando comandos de texto. Você digita um comando, o computador executa. Cerca de metade do fluxo de trabalho do Vibe Coding acontece aqui.

-----

## O Que São npm e npx? (Versão de 30 Segundos)

**npm (Node Package Manager)** é a ferramenta de gerenciamento de pacotes do JavaScript. Desenvolvedores ao redor do mundo publicam suas ferramentas e bibliotecas no registro npm, e você pode baixá-las e usá-las gratuitamente.

```bash
npm install stripe   # baixa o pacote Stripe para seu projeto
npm run dev          # executa o script "dev" definido no package.json
```

**npx** executa uma ferramenta diretamente sem instalá-la primeiro:

```bash
npx wrangler deploy  # executa o wrangler diretamente, sem npm install
```

Isso é tudo que você precisa saber por agora. Se encontrar outros comandos npm, peça à IA para explicá-los.

-----

## Passo 2: Instalar o Git

Git é uma ferramenta de controle de versão que permite clonar repositórios e rastrear mudanças no seu código.

**Mac:** Digite isso no terminal:

```bash
git --version
```

Se o Git não estiver instalado, o macOS vai automaticamente solicitar que você instale as Xcode Command Line Tools. Siga as instruções.

**Windows:** Acesse [git-scm.com](https://git-scm.com) e baixe o instalador.

Verifique após instalar:

```bash
git --version
# Você deve ver algo como git version 2.40.0
```

-----

## Passo 3: Criar uma Conta no Cloudflare

Acesse [cloudflare.com](https://cloudflare.com) e cadastre-se para uma conta gratuita.

O vibefast.app faz deploy no Cloudflare, então você precisará de uma conta para completar a configuração. Os limites do plano gratuito são mais do que suficientes para um novo app.

### Ativar o R2 (Requer Cartão de Crédito)

O vibefast.app usa o Cloudflare R2 para armazenar arquivos e imagens. **O R2 está desativado por padrão** e precisa ser ativado manualmente — o Cloudflare vai pedir um cartão de crédito quando você fizer isso.

Passos:

1. Faça login no Cloudflare Dashboard
2. Clique em **R2** no menu lateral esquerdo
3. Clique em "Purchase R2 Plan" ou "Enable R2" e siga as instruções para inserir seu cartão de crédito

**Você não será cobrado imediatamente.** O R2 inclui uma cota gratuita mensal (10 GB de armazenamento, 1 milhão de operações de leitura). Você só é cobrado se ultrapassar esses limites. O cartão de crédito serve para verificação de identidade e para cobranças de uso além da cota gratuita. Para um app novo, você ficará bem dentro desses limites.

-----

## Passo 4: Clonar o vibefast.app

Após comprar o vibefast.app, você receberá um convite de colaborador para o repositório privado. Aceite o convite e execute:

```bash
git clone https://github.com/vibefast-app/vibefast.git meu-app
cd meu-app
```

`git clone` copia o repositório inteiro para seu computador. `cd meu-app` te move para dentro dessa pasta.

-----

## Passo 5: npm install

```bash
npm install
```

Isso lê o `package.json` do projeto e baixa todos os pacotes necessários para uma pasta `node_modules`.

A primeira execução pode levar alguns minutos. Aguarde a mensagem de conclusão.

**Por que isso é necessário?**  
A pasta `node_modules` é grande (centenas de MB) e não é armazenada no GitHub. Toda vez que você clona um novo projeto, precisa executar `npm install` para baixar os pacotes localmente.

-----

## Passo 6: npm run setup

```bash
npm run setup
```

Esse é o passo mais mágico de todo o processo.

Combinado com o clone e o install, você executou apenas três comandos:

```bash
git clone https://github.com/vibefast-app/vibefast.git meu-app
cd meu-app && npm install
npm run setup
```

O `npm run setup` cuida de tudo automaticamente:

1. Guia você pelo login na sua conta Cloudflare
1. Cria o banco de dados D1 e executa o SQL de bootstrap
1. Gera automaticamente um JWT secret e o escreve nas variáveis de ambiente do Cloudflare
1. Faz deploy simultaneamente do frontend e do backend Workers para produção

Quando terminar, seu app está rodando em mais de 300 nós de edge do Cloudflare ao redor do mundo. Do clone ao deploy global — três comandos, alguns minutos. Isso é Vibe Coding no Cloudflare.

O terminal vai exibir a URL do seu app. Abra no navegador para confirmar que está no ar.

-----

## Passo 7: Instalar o Cursor

O Cursor é atualmente o editor de código com IA mais popular e a ferramenta central para Vibe Coding.

**O Cursor é um software pago a $20/mês.** Após se cadastrar, você recebe 7 dias de avaliação gratuita com funcionalidade completa — sem necessidade de cartão de crédito. Decida se vai assinar após o período de avaliação.

Acesse [cursor.com](https://cursor.com), baixe e instale, então cadastre-se com seu e-mail para iniciar os 7 dias de avaliação.

Após instalar, abra seu projeto no Cursor:

```bash
cursor .
```

Ou vá em Arquivo → Abrir Pasta dentro do Cursor e selecione sua pasta `meu-app`.

Uma vez aberto, pressione `Cmd+L` (Mac) ou `Ctrl+L` (Windows) para abrir o painel de chat de IA. Tente digitar:

```
Qual é a estrutura deste projeto? Você pode explicar as principais pastas e arquivos?
```

O Cursor vai analisar todo o seu projeto e explicar o que cada parte faz.

-----

## Desenvolvimento Local

Uma vez que seu ambiente esteja configurado, seu fluxo diário de desenvolvimento é:

```bash
npm run dev
```

Isso inicia um servidor de desenvolvimento local. Abra `http://localhost:5173` para ver seu app rodando localmente.

**Faça mudanças localmente → confirme que tudo funciona → `npm run deploy` para enviar para produção.**

Esse é o ciclo básico de desenvolvimento.

-----

## O Que Você Tem Agora

Após completar esses sete passos, você tem:

- ✅ Node.js e npm instalados
- ✅ Git instalado
- ✅ Uma conta no Cloudflare
- ✅ vibefast.app implantado em produção e acessível mundialmente
- ✅ Cursor instalado e pronto para Vibe Coding

Continue para o próximo tutorial e comece a construir as funcionalidades que você quer.

-----

## E Se Você Não Conseguir Ler o Código?

Uma vez que seu ambiente esteja configurado, você vai começar a ver muito código desconhecido — anotações de tipo TypeScript, async/await, sintaxe JSX…

**Você não precisa aprender tudo isso antes de começar.** Esse é o ponto central do Vibe Coding.

Quando encontrar código que não entende, pergunte diretamente ao Cursor:

```
Explique o que este código faz em linguagem simples.
```

Quando ver sintaxe que não faz sentido:

```
O que significa async/await? Por que é usado aqui?
```

Quando receber uma mensagem de erro vermelha, copie o texto completo e cole para a IA:

```
Estou recebendo este erro: [cole o erro]
Aqui está meu código: [cole o código]
Explique o que está causando isso e como corrigi-lo.
```

Você não precisa terminar de ler todos os tutoriais antes de começar a construir. Aprenda fazendo — pergunte quando travar. Essa é a maneira mais rápida de avançar.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Voltar ao índice do curso](../README-pt-br.md)

Pronto para começar com um template completo Cloudflare full-stack?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, preço sobe para $199 em 1º de agosto de 2026.
