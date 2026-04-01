# Git e GitHub: Controle de Versão Essencial para Vibe Coders

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Atualizado:** Março de 2026  
**Tempo de leitura:** ~12 minutos

-----

## Por Que Você Precisa do Git?

Você construiu rapidamente uma funcionalidade com IA e fez o deploy.

No dia seguinte, descobre que essa funcionalidade quebrou outra coisa. Você quer voltar para a versão de ontem, mas modificou dezenas de arquivos, não sabe o que mudou e não lembra como estava antes.

**O Git resolve esse problema.**

Git é uma **ferramenta de controle de versão** — ela registra cada estado de "salvamento", permitindo que você retorne a qualquer ponto no tempo. Roda no seu próprio computador, funciona sem internet.

**GitHub** é uma plataforma na nuvem que permite fazer backup do histórico de versões do Git online. Seu código tem backup na nuvem, sem medo de perder ao trocar de computador, e outros podem ver seu projeto.

Resumindo: **Git é a ferramenta, GitHub é o armazém.**

-----

## Conceitos Centrais do GitHub

### Repositório (Repo)

Um **repositório** é onde o GitHub armazena todo o código e histórico de um projeto. Cada projeto corresponde a um repo.

Seu repo pode ser:

- **Público**: Qualquer pessoa pode ver, adequado para projetos open source ou conteúdo educacional
- **Privado**: Só você e pessoas convidadas podem ver, adequado para código-fonte de produto comercial

O repo vibefast-docs é público, qualquer um pode ver o conteúdo dos tutoriais. O código-fonte do template VibeFast está em outro repo privado, somente clientes pagantes podem acessar — assim é como muitos templates comerciais são entregues.

### Clone

**Clone** copia um repo do GitHub para seu computador local:

```bash
git clone https://github.com/vibefast-app/vibefast-docs.git
```

Após clonar, você tem o código completo e histórico localmente.

### Fork

**Fork** copia o repo de outra pessoa para sua própria conta do GitHub, permitindo que você modifique livremente sua versão sem afetar o repo original. Esse é um método comum de colaboração open source.

-----

## Passo 1: Instalar Git e Definir Identidade

### Verificar se o Git está instalado

macOS geralmente tem Git integrado:

```bash
git --version
# Se exibir um número de versão, está instalado, ex: git version 2.39.3
```

Se não estiver, instale com Homebrew:

```bash
brew install git
```

### Defina sua identidade

Git registra quem fez cada commit. Diga ao Git quem você é (só precisa fazer uma vez):

```bash
git config --global user.name "Danko Peng"
git config --global user.email "danko@example.com"
```

O e-mail deve corresponder à sua conta do GitHub para que o GitHub possa vincular commits à sua conta.

### Criar conta no GitHub

Acesse [github.com](https://github.com) e cadastre-se, conta gratuita é suficiente.

-----

## Passo 2: Criar Novo Repo no GitHub

1. Faça login no GitHub, clique em **+** no canto superior direito → **New repository**
2. Preencha **Repository name** (ex: `meu-app`, apenas inglês, números, hífens)
3. Escolha **Public** ou **Private**
4. **Não** marque "Add a README file" — porque você já tem um projeto local, deixe o local ter precedência
5. Clique em **Create repository**

O GitHub cria um repo vazio e exibe os comandos para executar em seguida — você vai usá-los em breve.

-----

## Passo 3: git init e Conectar ao GitHub

Você tem uma pasta de projeto localmente, um repo vazio no GitHub, agora conecte-os.

### Cenário 1: Projeto local existe, enviar para o GitHub

```bash
# Entre na pasta do seu projeto
cd meu-app

# Inicializar Git (fazer esta pasta ser rastreada pelo Git)
git init

# Crie o .gitignore primeiro, exclua arquivos que não devem ser rastreados (explicado abaixo)
echo ".env" >> .gitignore
echo "node_modules/" >> .gitignore

# Primeira vez: adicione todos os arquivos e faça commit
git add .
git commit -m "initial commit"

# Conecte ao repo do GitHub (substitua com seu usuário e nome do repo)
git remote add origin https://github.com/SEU_USUARIO/meu-app.git

# Primeiro push para o GitHub
git push -u origin main
```

`git remote add origin` diz ao Git local: "Meu backup na nuvem está nessa URL do GitHub, chame-o de origin."

`-u origin main` só é necessário no primeiro push, depois basta `git push`.

### Cenário 2: Projeto clonado do GitHub

Se você clonou do GitHub (como clonar o VibeFast), o Git já conectou origin automaticamente, só use:

```bash
git add .
git commit -m "minha primeira mudança"
git push
```

### Confirmar que a conexão foi bem-sucedida

```bash
git remote -v
```

A saída deve parecer com:

```
origin  https://github.com/SEU_USUARIO/meu-app.git (fetch)
origin  https://github.com/SEU_USUARIO/meu-app.git (push)
```

-----

## Passo 4: Fluxo Diário de Trabalho

Após a configuração, o desenvolvimento diário são esses três comandos:

### git add .

```bash
git add .
```

Diz ao Git: "Prepare todos os arquivos que modifiquei para salvar."

`.` significa "todas as mudanças no diretório atual." Para adicionar apenas arquivos específicos:

```bash
git add apps/web/app/routes/blog.tsx
```

### git commit -m "atualizar funcionalidades de pedido"

```bash
git commit -m "atualizar funcionalidades de pedido"
```

A ação real de salvar. `-m` seguido de descrição do que mudou.

Boas mensagens de commit permitem que você, três meses depois, saiba imediatamente o que foi feito:

```bash
# ✅ Claro
git commit -m "adicionar exibição de tempo de leitura aos posts do blog"
git commit -m "corrigir status de pedido não atualizando após webhook Stripe"
git commit -m "remover imports não utilizados em api/users.ts"

# ❌ Sem significado
git commit -m "update"
git commit -m "fix bug"
git commit -m "asdfgh"
```

### git push

```bash
git push
```

Sincroniza commits locais para o GitHub. Seu código tem backup na nuvem.

-----

## Ver Histórico

```bash
git log --oneline
```

A saída parece com:

```
a3f2c1d adicionar notificação de e-mail para novos pedidos
9b8e4a2 corrigir status de pedido não atualizando após webhook
3d7f1c5 adicionar tempo de leitura aos posts do blog
1a2b3c4 initial commit
```

Cada linha é um commit, a string na frente é o ID do commit. Para retornar a uma versão:

```bash
git checkout 9b8e4a2
```

-----

## Importante: O Que Foi Enviado ao GitHub Tem Histórico

Isso é o que muitos não sabem, e a forma mais fácil de causar problemas sérios.

**Deletar um arquivo e fazer push não significa que o arquivo desapareceu.**

O Git é projetado para preservar todo o histórico. O que você deletou ainda pode ser encontrado no histórico de commits. Qualquer pessoa que clone seu repo e olhe commits antigos pode ver o conteúdo anterior daquele arquivo.

E o GitHub tem programas automatizados varrendo novos pushes 24/7, especificamente procurando por chaves de API vazadas. Desde o momento que você fez push, pode já ter sido varrido.

### Cenário Perigoso Mais Comum

Você acidentalmente fez commit do `.env`:

```bash
git add .   # Acidentalmente incluiu .env
git commit -m "adicionar integração stripe"
git push    # Enviado para o GitHub
```

Depois percebeu, imediatamente deleta e faz push:

```bash
git rm .env
git commit -m "remover .env"
git push
```

**Isso não funciona.**

O commit atual não tem `.env`, mas o commit anterior ainda tem. Qualquer pessoa olhando o histórico de commits pode ver sua chave Stripe, JWT secret, todos os segredos.

### Passos Corretos de Correção

**Passo 1 (mais importante): Vá imediatamente à plataforma correspondente para revogar essa chave e gerar uma nova.** Independentemente de como você limpe o histórico do git depois, faça isso primeiro. A chave já vazou, deve ser considerada inválida.

**Passo 2: Remover completamente do histórico.** Use `git filter-repo`:

```bash
# Instalar
pip install git-filter-repo

# Remover .env de todo o histórico de commits
git filter-repo --path .env --invert-paths

# Force push para o GitHub (sobrescrever histórico)
git push --force
```

Mas o melhor método é **nunca deixar entrar no git em primeiro lugar**.

-----

## Prevenção: .gitignore

`.gitignore` diz ao Git quais arquivos nunca rastrear:

```
# .gitignore
.env
.env.local
.env.production
node_modules/
.wrangler/
dist/
```

**Sequência crítica: Após `git init`, antes do primeiro `git add .`, crie `.gitignore` primeiro.**

Se você fizer `git add .` primeiro e depois criar `.gitignore`, o `.env` pode já estar rastreado, adicionar ao `.gitignore` depois não vai ajudar. A ordem importa.

O `.gitignore` do VibeFast já está configurado, você não precisa adicionar manualmente. Lembre-se desse passo quando criar novos projetos você mesmo.

-----

## Olhe Antes de Fazer Push

```bash
# Ver arquivos preparados para commit
git status

# Ver mudanças específicas de conteúdo
git diff --staged
```

Desenvolva o hábito de dar uma olhada no `git status` antes de fazer push, confirme que nenhum arquivo indesejado se infiltrou.

-----

## Resumo

**Fluxo completo do zero:**

```bash
# 1. Criar novo repo no site do GitHub

# 2. Inicializar localmente
cd meu-app
git init
echo ".env" >> .gitignore    # Crie .gitignore primeiro
echo "node_modules/" >> .gitignore
git add .
git commit -m "initial commit"

# 3. Conectar ao GitHub e fazer push
git remote add origin https://github.com/SEU_USUARIO/meu-app.git
git push -u origin main

# 4. Diariamente depois
git add .
git commit -m "descreva claramente o que você mudou"
git push
```

**Consciência mais importante: GitHub tem histórico completo, deletar não significa desaparecer.** Uma vez que uma chave de API foi enviada, mesmo que imediatamente deletada, essa chave deve ser considerada vazada — revogue e regenere imediatamente, sem exceções.

Prevenção é sempre mais fácil que remédio: configure `.gitignore` antes do primeiro commit, dê uma olhada no `git status` antes de fazer push.

-----

Dúvidas? Me encontre no X em [@dankopeng](https://x.com/dankopeng).  
👉 **[vibefast.app](https://vibefast.app)** — Early bird $99, preço sobe para $199 em 1º de junho de 2026.
