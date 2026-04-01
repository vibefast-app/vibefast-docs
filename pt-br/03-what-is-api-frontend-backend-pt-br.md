# O Que É uma API? Comunicação Entre Frontend e Backend em Linguagem Simples

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Atualizado:** Março de 2026  
**Tempo de leitura:** ~7 minutos

-----

## Uma Analogia do Seu Dia a Dia

Você vai a um restaurante.

Você não entra na cozinha e cozinha você mesmo. Você diz ao garçom o que quer. O garçom leva seu pedido para a cozinha, a cozinha prepara, e o garçom traz a comida de volta para você.

**O garçom é a API.**

Uma API é a ponte de comunicação entre o frontend (você) e o backend (cozinha). O frontend não precisa saber como o backend funciona, só precisa saber como falar com a API.

-----

## O Que Exatamente É uma API?

**API (Application Programming Interface)** é um conjunto de regras que define como dois sistemas se comunicam.

No contexto de apps web, normalmente significa:

- **Frontend** (página no navegador) envia uma requisição
- **API do backend** (servidor) recebe a requisição, processa e retorna resultados

Uma requisição de API típica se parece com isso:

```
Frontend pergunta: "Me dê os dados do usuário com userId = 123"
→ GET /api/users/123

Backend responde: "Aqui estão os dados"
→ { "id": 123, "name": "Danko", "email": "danko@example.com" }
```

-----

## Métodos HTTP: O Que Você Está Dizendo à API?

Requisições de API têm quatro ações comuns, correspondendo a diferentes métodos HTTP:

|Método          |Propósito     |Analogia               |
|----------------|--------------|-----------------------|
|`GET`           |Ler dados     |"Me mostre o cardápio" |
|`POST`          |Criar dados   |"Quero pedir esse prato"|
|`PUT` / `PATCH` |Atualizar dados|"Mude meu pedido para outro prato"|
|`DELETE`        |Deletar dados |"Cancele esse prato"   |

-----

## Respostas da API: O Que o Backend Está Dizendo?

Respostas do backend geralmente contêm duas partes:

**Código de Status HTTP** — um número que informa o resultado:

|Código|Significado                    |
|------|-------------------------------|
|`200` |Sucesso                        |
|`201` |Criado com sucesso             |
|`400` |Sua requisição tem problemas (Bad Request)|
|`401` |Você não está logado (Unauthorized)|
|`403` |Você não tem permissão (Forbidden)|
|`404` |Não encontrado                 |
|`500` |Erro do servidor               |

**Conteúdo da Resposta** — geralmente em formato JSON:

```json
{
  "success": true,
  "data": {
    "id": 123,
    "name": "Danko"
  }
}
```

-----

## Arquitetura de API do VibeFast

O VibeFast usa **Service Binding** para permitir que frontend e backend se comuniquem diretamente dentro da rede interna do Cloudflare, não pela internet pública.

```
Navegador
  ↓ requisição HTTP
Remix Frontend Worker
  ↓ Service Binding (interno, zero CORS)
Workers API
  ↓
Banco de Dados D1
```

Isso significa:

- A API do backend não tem URL pública, acesso externo impossível
- Não há necessidade de configuração de CORS entre frontend e backend
- Cada chamada de API economiza uma ida e volta na rede, velocidade maior

-----

## APIs de Terceiros: Seu App Também Chama APIs de Outros

Seu app não tem apenas sua própria API, ele também chama APIs de terceiros:

- **Stripe API**: "Crie um pagamento para mim"
- **Resend API**: "Envie um e-mail para este usuário"
- **Cloudflare API**: `npm run deploy` por trás dos panos chama a API do Cloudflare

Essas APIs de terceiros exigem chaves de API para usar — é por isso que o artigo anterior dizia que chaves de API devem ser mantidas em segurança em variáveis de ambiente.

-----

## Projetando Endpoints de API com IA

Esse é um cenário prático de Vibe Coding. Você pode dizer diretamente à IA:

> "Crie um endpoint de API no Cloudflare Workers, GET /api/posts, retorne todos os artigos publicados, ordenados por data de criação decrescente, 10 por página."

A IA vai gerar código como este:

```typescript
// apps/api/src/routes/posts.ts
app.get("/api/posts", async (c) => {
  const page = Number(c.req.query("page") || 1)
  const limit = 10
  const offset = (page - 1) * limit

  const posts = await c.env.DB.prepare(`
    SELECT id, title, slug, created_at 
    FROM posts 
    WHERE status = 'published'
    ORDER BY created_at DESC
    LIMIT ? OFFSET ?
  `).bind(limit, offset).all()

  return c.json({ success: true, data: posts.results })
})
```

Você não precisa escrever isso você mesmo, mas entender o que faz ajuda a julgar melhor se o output da IA está correto.

-----

## Resumo

APIs são as regras de comunicação entre frontend e backend. Lembre-se de três coisas:

1. Frontend usa métodos HTTP (GET/POST/PUT/DELETE) para dizer à API o que fazer
2. Backend usa códigos de status (200/404/500) para dizer ao frontend o resultado
3. Dados são geralmente passados em formato JSON

-----

👉 [Próximo: O Que É Domain e DNS? O Que Fazer Após Comprar um Domínio](./12-domain-and-dns-setup-guide-pt-br.md)

Dúvidas? Me encontre no X em [@dankopeng](https://x.com/dankopeng).  
👉 **[vibefast.app](https://vibefast.app)** — Early bird $99, preço sobe para $199 em 1º de junho de 2026.
