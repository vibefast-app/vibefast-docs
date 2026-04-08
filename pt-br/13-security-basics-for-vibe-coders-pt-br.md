# Segurança Básica para Vibe Coders: Confirme Isso Antes de Colocar no Ar

[English](../en/13-security-basics-for-vibe-coders-en.md) · [繁中](../zh/13-security-basics-for-vibe-coders-zh.md) · [Español](../es/13-security-basics-for-vibe-coders-es.md) · [日本語](../jp/13-security-basics-for-vibe-coders-jp.md) · [Português (BR)](../pt-br/13-security-basics-for-vibe-coders-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Atualizado:** Março de 2026  
**Tempo de leitura:** ~10 minutos

-----

## Você Não Precisa Ser Especialista em Segurança, Mas Precisa Saber Isso

Muitos Vibe Coders só começam a pensar em segurança depois que o app vai ao ar. Isso é normal — quando você está usando IA para construir funcionalidades rapidamente, segurança é facilmente empurrada para a lista de "resolver depois."

Mas algumas coisas não são "resolver depois," são "confirmar obrigatoriamente antes de ir ao ar."

Este artigo não é sobre transformá-lo em um engenheiro de segurança, mas sobre esclarecer os pontos de verificação mais importantes em linguagem que você consegue entender.

-----

## 1. Chaves Secretas NUNCA Devem Aparecer no Código

Esse é o erro mais comum e mais grave.

```typescript
// ❌ Errado: chave secreta hardcoded no código
const token = jwt.sign(payload, "minha-super-chave-secreta-123")

// ✅ Correto: ler de variáveis de ambiente
const token = jwt.sign(payload, env.JWT_SECRET)
```

Por que isso é tão grave? Porque seu código geralmente vai para o GitHub. Enquanto o repo for público, ou se um dia acidentalmente for tornado público, qualquer pessoa pode ver sua chave secreta, então forjar a identidade de qualquer usuário e burlar toda a autenticação.

**Como o vibefast.app lida com isso:** `npm run setup` automaticamente gera o JWT secret e o escreve nas variáveis de ambiente do Cloudflare Workers. Você nunca vai ver o valor da chave secreta no código.

**O que você precisa confirmar:**

- Arquivo `.env` está adicionado ao `.gitignore`
- Todos os segredos (chave Stripe, chave Resend, JWT secret) são lidos de `env`, não hardcoded
- Antes de fazer push de código para o GitHub, pesquise no repo por strings como `sk_live_`, `re_`, `secret`

-----

## 2. Nunca Confie Diretamente na Entrada do Usuário

Lugares onde seu app recebe entrada do usuário — formulários, parâmetros de URL, requisições de API — cada um é um vetor de ataque em potencial.

### SQL Injection

Se você concatenar diretamente a entrada do usuário em queries SQL, atacantes podem injetar instruções SQL maliciosas:

```typescript
// ❌ Perigoso: concatenação direta de string
const query = `SELECT * FROM users WHERE email = '${userInput}'`
// Atacante insere: ' OR '1'='1
// Resultado: SELECT * FROM users WHERE email = '' OR '1'='1'
// Isso retorna todos os dados de usuários

// ✅ Seguro: usar queries parametrizadas
const result = await db.prepare(
  "SELECT * FROM users WHERE email = ?"
).bind(userInput).first()
```

D1 suporta queries parametrizadas, todas as operações de banco de dados do vibefast.app usam esse método. Se você escrever SQL você mesmo, sempre use placeholders `?`, nunca concatenação de strings.

### XSS (Cross-Site Scripting)

Se você renderizar diretamente a entrada do usuário como HTML, atacantes podem injetar scripts maliciosos:

```typescript
// ❌ Perigoso
<div dangerouslySetInnerHTML={{ __html: userInput }} />

// ✅ Seguro: deixe React/Remix fazer escape automático
<div>{userInput}</div>
```

Remix faz escape automático por padrão, enquanto você não usar `dangerouslySetInnerHTML`, está basicamente seguro.

-----

## 3. Endpoints de API Devem Verificar Identidade e Permissões

Todo endpoint de API que exige login deve verificar o JWT token. Isso parece óbvio, mas muitas pessoas esquecem durante o desenvolvimento rápido.

```typescript
// ❌ Esqueceu de verificar
export async function action({ request }: ActionFunctionArgs) {
  // Processa diretamente, não confirmou se o usuário está logado
  const data = await request.json()
  await db.prepare("DELETE FROM posts WHERE id = ?").bind(data.id).run()
}

// ✅ Verifique identidade primeiro
export async function action({ request, context }: ActionFunctionArgs) {
  const user = await requireAuth(request, context.env)
  // Confirme que está logado antes de continuar
  const data = await request.json()
  await db.prepare("DELETE FROM posts WHERE id = ?").bind(data.id).run()
}
```

Indo além: **não apenas verifique identidade, verifique também permissões**. O Usuário A não deve poder deletar os posts do Usuário B, mesmo que ambos estejam logados.

```typescript
// ✅ Verificar identidade + permissões
const post = await db.prepare(
  "SELECT * FROM posts WHERE id = ?"
).bind(postId).first()

if (post.authorId !== user.userId) {
  throw new Response("Forbidden", { status: 403 })
}
```

-----

## 4. Operações Sensíveis Precisam de Rate Limiting

Se sua API de login não tem rate limiting, atacantes podem usar programas para tentar automaticamente milhões de combinações de senha (força bruta).

O Cloudflare tem uma vantagem natural aqui — você pode usar o recurso de Rate Limiting do Cloudflare para bloquear frequências de requisição anormais antes que o tráfego entre no Workers, sem precisar implementar no código você mesmo.

Endpoints que devem pelo menos ser limitados:

- `/login`, `/signup` — prevenir força bruta
- `/api/send-email` — prevenir uso para enviar spam
- Qualquer operação que gere custos (pagamentos Stripe, uploads R2)

-----

## 5. HTTPS É Padrão, Não Opcional

Seu app deve usar HTTPS, não HTTP.

Boa notícia: **apps implantados no Cloudflare automaticamente habilitam HTTPS**, sem configuração extra necessária. O Cloudflare cuida da solicitação e renovação do certificado SSL, você não precisa se preocupar com isso.

Mas uma coisa a confirmar: se você tem um domínio personalizado, confirme no Cloudflare Dashboard que a configuração SSL/TLS está em Full ou Full (Strict), não use Flexible (no modo Flexible, o segmento do Cloudflare para sua origem ainda é HTTP, tem riscos de segurança).

-----

## 6. Mensagens de Erro Não Devem Vazar Informações Internas

Durante o desenvolvimento você pode estar acostumado a exibir mensagens de erro detalhadas por conveniência de debug:

```typescript
// ❌ Produção não deve fazer isso
catch (error) {
  return json({ error: error.message, stack: error.stack })
}
```

`error.stack` expõe sua estrutura de código, caminhos de arquivo, até nomes de funções, fornecendo informações úteis a atacantes.

```typescript
// ✅ Ambiente de produção
catch (error) {
  console.error(error) // Só registre erro completo nos logs
  return json({ error: "Algo deu errado" }, { status: 500 })
}
```

-----

## 7. Atualize Dependências Regularmente

Seu app depende de muitos pacotes de terceiros, esses pacotes ocasionalmente descobrem vulnerabilidades de segurança e lançam correções. Se você continuar usando versões antigas, estará exposto a vulnerabilidades conhecidas.

```bash
# Verificar quais pacotes têm problemas de segurança
npm audit

# Corrigir automaticamente problemas menores
npm audit fix
```

Não precisa fazer isso diariamente, mas pelo menos execute `npm audit` uma vez por mês, priorize vulnerabilidades de severidade alta/crítica.

-----

## Checklist de Segurança Pré-Lançamento

```
□ Todas as chaves secretas em variáveis de ambiente, não hardcoded
□ .env adicionado ao .gitignore
□ Todas as queries SQL usam abordagem parametrizada
□ Todas as APIs que exigem login verificam JWT
□ Endpoints de API sensíveis têm rate limiting (ou use Cloudflare Rate Limiting)
□ Mensagens de erro de produção não expõem stack traces
□ Rodou npm audit, sem vulnerabilidades altas/críticas
□ Cloudflare SSL/TLS definido como Full ou Full (Strict)
```

-----

## Resumo

Segurança não é "quando tiver tempo," mas também não precisa ser perfeita de uma vez.

Para a maioria dos apps indie, fazer bem os sete pontos mencionados neste artigo já te torna mais seguro do que 80% dos pequenos apps no mercado. Atacantes geralmente procuram os alvos mais fáceis — enquanto você não for o mais fraco, pode evitar a maioria dos riscos.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Voltar ao índice do curso](../README-pt-br.md)

Pronto para começar com um template completo Cloudflare full-stack?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, preço sobe para $199 em 1º de junho de 2026.
