# O Que É JWT? A Tecnologia Que Você Usa Todo Dia Explicada em Linguagem Simples

[English](../en/04-what-is-jwt-authentication-en.md) · [繁中](../zh/04-what-is-jwt-authentication-zh.md) · [Español](../es/04-what-is-jwt-authentication-es.md) · [日本語](../jp/04-what-is-jwt-authentication-jp.md) · [Português (BR)](../pt-br/04-what-is-jwt-authentication-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Atualizado:** Março de 2026  
**Tempo de leitura:** ~8 minutos

-----

## Começando com um Cenário Real

Você abre um app, digita usuário e senha para fazer login.

Depois clica para outra página, e outra, e o sistema sempre sabe quem você é sem pedir para você fazer login novamente.

Parece natural, mas há um problema por trás disso:

**HTTP é um protocolo "sem estado".**

Cada requisição ao servidor é completamente nova — ele não se lembra automaticamente de "quem enviou a última requisição." Após você fazer login, quando clica para a próxima página, como o servidor sabe que ainda é você?

Esse é o problema que o JWT resolve.

-----

## O Que É JWT?

**JWT (JSON Web Token)** é uma forma de o servidor "lembrar quem você é."

Pense nele como um **passe digital**:

1. Você faz login, o servidor confirma que suas credenciais estão corretas
2. O servidor te dá um passe (JWT token)
3. Cada requisição subsequente, você traz esse passe
4. O servidor vê o passe, sabe quem você é e quais permissões você tem

O processo inteiro não exige que o servidor lembre de nada — toda informação está no passe.

-----

## Como É um JWT?

JWT é uma string muito longa, assim:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjEyMywiZW1haWwiOiJ1c2VyQGV4YW1wbGUuY29tIiwicm9sZSI6InVzZXIiLCJleHAiOjE3MDk0NTEyMDB9.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

Parece complexo, mas na verdade são três partes separadas por `.`:

```
[Header].[Payload].[Signature]
```

### Header

Descreve qual algoritmo esse token usa para criptografia:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### Payload

Contém os dados reais — quem você é, quais permissões você tem, quando esse token expira:

```json
{
  "userId": 123,
  "email": "user@example.com",
  "role": "user",
  "exp": 1709451200
}
```

Essa parte **não é criptografada**, apenas codificada em Base64. Qualquer pessoa com o token pode decodificar e ver esse conteúdo — então nunca coloque senhas ou dados sensíveis aqui.

### Signature

Essa é a parte crucial. O servidor usa uma **chave secreta** que só ele conhece para assinar as duas primeiras partes:

```
HMACSHA256(
  base64(header) + "." + base64(payload),
  chave_secreta
)
```

O propósito da assinatura: **evitar falsificação**.

Se alguém pegar seu token e tentar mudar `role: "user"` para `role: "admin"` no payload, a assinatura não vai bater. O servidor verifica e sabe que o token foi adulterado, rejeitando-o imediatamente.

-----

## Como JWT É Diferente das Sessions Tradicionais?

Outro método comum de autenticação é a **Session**:

|          |Session                |JWT               |
|----------|-----------------------|------------------|
|Servidor precisa armazenar estado|✅ Sim (armazena dados da session)|❌ Não (info no token)|
|Adequado para sistemas distribuídos|❌ Complexo (múltiplos servidores precisam compartilhar session)|✅ Funciona naturalmente (qualquer servidor pode verificar)|
|Token pode ser revogado|✅ Fácil (só deletar a session)|⚠️ Complexo (precisa de mecanismo extra)|
|Compatível com edge computing|❌ Difícil|✅ Perfeito|

**JWT é particularmente adequado para edge computing (Cloudflare Workers)** porque Workers são sem estado — cada requisição pode rodar em um nó de edge diferente, não pode compartilhar memória de session. JWT carrega toda a informação no token, qualquer nó que receba uma requisição pode verificá-la independentemente sem consultar um banco de dados.

Por isso o VibeFast escolhe JWT em vez de Session.

-----

## Pontos de Segurança do JWT

### A Chave Secreta Deve Ser Forte e Confidencial

A segurança do JWT depende inteiramente da chave secreta. Se alguém obtiver sua chave secreta, pode forjar o token de qualquer pessoa, personificar qualquer usuário, incluindo admin.

A chave secreta deve ser lida de variáveis de ambiente, nunca hardcoded. Gere um segredo forte aleatório com esse comando:

```bash
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

Armazene o valor gerado nas variáveis de ambiente do Cloudflare Workers, nunca deixe aparecer em nenhum arquivo de código.

### O Token Deve Ter Tempo de Expiração

Passes não devem ser válidos para sempre. O campo `exp` do JWT define o tempo de expiração, o servidor rejeita tokens expirados.

Prática comum é validade do access token de 15 minutos a 7 dias, ajustada conforme necessidades de segurança. Após expirar, os usuários precisam fazer login novamente.

### O Token Deve Ser Armazenado com Segurança

Onde você armazena o token importa:

|Local de Armazenamento|Risco           |
|----------------------|----------------|
|localStorage          |⚠️ Vulnerável a ataques XSS|
|sessionStorage        |⚠️ Mesmo que acima|
|HttpOnly Cookie       |✅ Mais seguro, JS não consegue ler|
|Memória (React state) |✅ Seguro mas desaparece ao atualizar a página|

O VibeFast usa HttpOnly Cookie para armazenar tokens, atualmente a prática segura mais comum.

### O Payload Não Deve Conter Dados Sensíveis

O payload do JWT é codificado em Base64, não criptografado. Qualquer pessoa com o token pode decodificar e ver o conteúdo em segundos.

Por isso o payload contém apenas informações de identidade (userId, role, email), nunca coloque senhas, números de cartão de crédito, ou qualquer dado que não possa ser público.

-----

## Um Fluxo Completo de JWT

Percorra o fluxo inteiro em linguagem simples:

```
1. Usuário digita usuário/senha → enviado ao backend

2. Backend verifica no banco de dados, confirma que a senha está correta

3. Backend usa a chave secreta para gerar o JWT token:
   payload = { userId: 123, role: "user", exp: amanhã }
   token = sign(payload, chave_secreta)

4. Coloca o token em HttpOnly Cookie, retorna ao navegador

5. Cada requisição subsequente, o navegador inclui automaticamente o Cookie

6. Backend recebe a requisição:
   - Extrai o token
   - Verifica a assinatura com a chave secreta
   - Confirma que não está expirado
   - Extrai userId do payload, sabe quem é

7. Processa a requisição, retorna resultado
```

Todo esse fluxo já está implementado no VibeFast, você não precisa escrever você mesmo.

-----

## Perguntas Comuns

**P: E se o JWT token for roubado?**

Essa é a pergunta mais frequente sobre JWT. Uma vez que um token é emitido, é difícil revogá-lo antes de expirar (a menos que você mantenha uma lista negra, mas isso perde a vantagem sem estado do JWT).

Solução: encurte a validade do token (ex: 1 hora), combine com mecanismo de refresh token para renovação automática. O VibeFast atualmente usa validade de 7 dias, adequada para a maioria dos apps web.

**P: Devo obrigatoriamente usar JWT?**

Não. Session + Cookie é outra solução madura, completamente viável em ambientes de servidor tradicionais. Mas em ambientes de edge computing do Cloudflare Workers, JWT é uma escolha mais natural.

**P: Qual é a relação entre JWT e OAuth?**

OAuth é um framework de autorização (deixar apps de terceiros acessarem recursos em seu nome, como "Entrar com Google"). JWT é um formato de token comumente usado em fluxos OAuth, mas JWT pode ser usado independentemente sem OAuth.

-----

## Resumo

JWT é um passe digital que permite que sistemas HTTP sem estado identifiquem usuários. É particularmente adequado para ambientes de edge computing do Cloudflare Workers porque:

- Não há necessidade de o servidor armazenar estado de session
- Qualquer nó de edge pode verificar independentemente
- Mecanismo de assinatura previne falsificação

Três pontos de segurança ao usar JWT: mantenha a chave secreta confidencial, defina tempo de expiração, não coloque dados sensíveis no payload.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Voltar ao índice do curso](../README-pt-br.md)

Pronto para começar com um template completo Cloudflare full-stack?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, preço sobe para $199 em 1º de junho de 2026.
