# Como Testar Seu App: Deixe a IA Gerar Comandos curl para Cada Funcionalidade

[English](../en/21-ai-testing-guide-en.md) · [繁中](../zh/21-ai-testing-guide-zh.md) · [Español](../es/21-ai-testing-guide-es.md) · [日本語](../jp/21-ai-testing-guide-jp.md) · [Português (BR)](../pt-br/21-ai-testing-guide-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Atualizado:** Março de 2026  
**Tempo de leitura:** ~7 minutos

-----

## Após o Deploy, Como Você Sabe Que Realmente Funciona?

Muitas pessoas fazem deploy, abrem um navegador, clicam por aí e assumem que está tudo certo porque nada quebrou visivelmente.

Mas o navegador só testa o que você pode ver. Sua API está retornando os dados certos? O frontend realmente retorna um 200? Um login com falha retorna o código de erro correto? Nada disso é visível clicando por aí.

Meu hábito: **toda vez que termino uma funcionalidade, testo imediatamente com curl.** Seja em desenvolvimento local ou em um deploy de produção, curl funciona nos dois ambientes e os resultados são imediatos.

-----

## O Que É curl?

curl é uma ferramenta de linha de comando para enviar requisições HTTP. Vem pré-instalado no Mac e Linux, e está disponível no Windows também.

Sem pacotes npm para instalar. Sem código para escrever. Uma linha no terminal testa qualquer URL — tanto páginas frontend quanto APIs backend.

**E você não precisa memorizar a sintaxe do curl. Só peça à IA para gerar os comandos.**

-----

## Passo 1: Peça à IA para Gerar Seus Comandos curl

Após terminar qualquer funcionalidade, diga à IA diretamente:

```
Acabei de fazer deploy de um e-commerce de sabonetes artesanais para https://soapco.com.

Gere comandos curl para testar estas funcionalidades:
1. A homepage retorna com sucesso
2. API de listagem de produtos (GET /api/products)
3. Detalhe de produto único (GET /api/products/sabonete-lavanda)
4. API de login (POST /api/auth/login) com email: test@example.com, senha: test123
5. Usando o token do login, testar a API protegida de lista de pedidos (GET /api/orders)
6. Acessar a lista de pedidos sem token — confirmar que está bloqueado (deve retornar 401)
```

A IA gera um conjunto completo de comandos curl. Copie e cole no seu terminal e execute.

**Este é o mindset central para testes de Vibe Coder: você descreve o que testar, a IA escreve como testar.**

-----

## curl Funciona em Páginas Frontend Também

Muitas pessoas assumem que curl é só para APIs backend. Funciona igualmente bem em páginas frontend:

```
Gere comandos curl para testar estas páginas frontend:
1. Homepage https://soapco.com/ retorna 200
2. Página de listagem de produtos /shop retorna com sucesso
3. Uma página inexistente /xyz retorna 404 (não 200)
4. O dashboard /admin, quando acessado sem login, redireciona para login (deve retornar 302 ou 401)
```

A IA gera algo assim:

```bash
# Testar homepage
curl -s -o /dev/null -w "%{http_code}" https://soapco.com/
# Esperado: 200

# Testar página inexistente
curl -s -o /dev/null -w "%{http_code}" https://soapco.com/xyz
# Esperado: 404

# Testar que página admin está bloqueada sem login
curl -s -o /dev/null -w "%{http_code}" https://soapco.com/admin
# Esperado: 302 ou 401, nunca 200
```

`-o /dev/null` descarta o corpo HTML para não poluir seu terminal. `-w "%{http_code}"` imprime apenas o código de status — para testes frontend, o código de status é geralmente tudo que você precisa.

-----

## Quando Você Precisa Ver a Resposta Completa

Às vezes você precisa ver o corpo completo da resposta da API. Diga à IA:

```
Gere um comando curl para acessar GET https://soapco.com/api/products
e exibir a resposta JSON formatada para fácil leitura.
```

A IA gera:

```bash
curl -s https://soapco.com/api/products | jq
```

`jq` é uma ferramenta de formatação JSON. Instale no Mac com `brew install jq`, ou só pergunte à IA "como instalo jq no meu sistema."

-----

## Quando Você Precisa Ver o Código de Status

Não tem certeza se uma API está retornando 200, 401 ou 500? Diga à IA:

```
Gere um comando curl para acessar este endpoint e exibir
tanto o código de status HTTP quanto o corpo da resposta:
POST https://soapco.com/api/auth/login
body: {"email": "test@example.com", "password": "senhaerrada"}
```

A IA gera:

```bash
curl -i -X POST https://soapco.com/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "test@example.com", "password": "senhaerrada"}'
```

`-i` inclui o código de status HTTP e os headers de resposta na saída.

-----

## Testar Localmente Funciona da Mesma Forma

Não precisa esperar um deploy — teste localmente assim que estiver rodando:

```
Gere comandos curl para testar a API de produtos localmente.
O servidor local roda em http://localhost:8787
```

A IA substitui pela URL do localhost. Todo o resto é idêntico.

**Teste localmente → confirme que funciona → faça deploy → teste de novo com a URL de produção.**

Ambos os passos precisam passar antes de você poder chamar de pronto.

-----

## Um Bom Hábito: Peça à IA para Reunir Seus Testes em um Script

Assim que você tiver múltiplas funcionalidades para testar, peça à IA para consolidá-las:

```
Reúna estes testes em um script test.sh.
Cada teste deve imprimir "PASS" ou "FAIL",
e o script deve mostrar uma contagem final de testes aprovados e reprovados.

Testes:
1. Homepage retorna 200
2. API de listagem de produtos retorna 200
3. Produto inexistente retorna 404
4. API de pedidos sem token retorna 401

URL base: https://soapco.com
```

Então execute após cada deploy:

```bash
bash test.sh
```

Alguns segundos para confirmar que todas as suas funcionalidades principais ainda estão funcionando — sem precisar rodar comandos um por um.

-----

## Resumo

curl é a ferramenta de teste mais prática para Vibe Coders — funciona tanto para frontend quanto para backend. O fluxo central são dois passos:

1. **Descreva o que você quer testar em linguagem simples** — páginas, APIs, casos de sucesso, casos de falha
1. **A IA gera os comandos curl, você copia, cola e executa** — verifique se os resultados correspondem às expectativas

Teste após cada funcionalidade. Teste localmente, depois teste de novo após fazer deploy. Quanto mais cedo você detecta um problema, mais fácil é corrigi-lo.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Voltar ao índice do curso](../README-pt-br.md)

Pronto para começar com um template completo Cloudflare full-stack?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, preço sobe para $199 em 1º de junho de 2026.
