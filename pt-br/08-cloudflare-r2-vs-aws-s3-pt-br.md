# R2 vs S3: Por Que Não Uso AWS para Armazenar Imagens

[English](../en/08-cloudflare-r2-vs-aws-s3-en.md) · [繁中](../zh/08-cloudflare-r2-vs-aws-s3-zh.md) · [Español](../es/08-cloudflare-r2-vs-aws-s3-es.md) · [日本語](../jp/08-cloudflare-r2-vs-aws-s3-jp.md) · [Português (BR)](../pt-br/08-cloudflare-r2-vs-aws-s3-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Atualizado:** Março de 2026  
**Tempo de leitura:** ~6 minutos

-----

## Seu App Precisa Armazenar Imagens?

Avatares de usuários, screenshots de produtos, imagens de capa de blog — qualquer app com upload de mídia precisa de um lugar para armazenar esses arquivos.

Bancos de dados não são adequados para armazenar imagens (bancos de dados são para dados de texto estruturado), você precisa de um serviço dedicado de "armazenamento de objetos."

A escolha mais comum é **AWS S3**. Mas se seu app roda no Cloudflare, existe uma opção melhor: **Cloudflare R2**.

-----

## O Custo Oculto do S3: Taxas de Egresso

O preço do AWS S3 tem uma armadilha em que muitas pessoas caem: **taxas de egresso**.

- Armazenar imagens no S3: taxa de armazenamento (~$0,023/GB/mês)
- Usuários baixam imagens: **taxa de egresso (~$0,09/GB)**

Parece pouco? Vamos calcular:

Digamos que seu app tem 1.000 usuários, cada um baixa em média 10 imagens diariamente, 500KB cada:

```
Egresso diário = 1.000 × 10 × 0,5MB = 5.000MB = 5GB
Custo de egresso diário = 5GB × $0,09 = $0,45
Custo de egresso mensal = $0,45 × 30 = $13,50
```

Quanto mais bem-sucedido seu app, maior a conta. Algumas empresas só descobrem que as taxas de egresso do S3 dominam os custos depois que o app viraliza.

-----

## A Lógica de Preços do R2

A estratégia de preços do Cloudflare R2 é completamente diferente:

|Item de Custo|AWS S3       |Cloudflare R2      |
|-------------|-------------|-------------------|
|Armazenamento|$0,023/GB/mês|$0,015/GB/mês      |
|Egresso      |$0,09/GB     |**$0 — Completamente gratuito**|
|Plano gratuito|Limitado    |10GB + 1M operações/mês|

Zero taxas de egresso tornam a vantagem do R2 muito clara para apps de alto tráfego. Quanto mais suas imagens são baixadas, maior a diferença de custo em relação ao S3.

-----

## Diferenças Técnicas Entre R2 e S3

|            |AWS S3   |Cloudflare R2         |
|------------|---------|----------------------|
|Compatibilidade de API|API S3 nativa|Compatível com API S3|
|Distribuição global|Configuração manual multi-região|Distribuição global automática|
|Integração com Workers|Chamadas entre redes|Direct Binding, sem internet pública|
|Configuração CORS|Configuração manual|Trate via Workers, pode ser zero CORS|

**Compatibilidade com a API S3** é importante — a maioria dos pacotes que suportam S3 (como `@aws-sdk/client-s3`) pode operar diretamente o R2, só troque a URL do endpoint. Isso torna os custos de migração muito baixos.

-----

## Usando R2 no Cloudflare Workers

Padrão básico de upload e leitura:

```typescript
// Fazer upload de imagem para o R2
await env.R2.put(`images/${filename}`, fileBuffer, {
  httpMetadata: { contentType: file.type }
})

// Retornar arquivo R2 através do Workers
const object = await env.R2.get(`images/${filename}`)
if (!object) return new Response("Not found", { status: 404 })

return new Response(object.body, {
  headers: { "Content-Type": object.httpMetadata?.contentType || "application/octet-stream" }
})
```

Fazer proxy do acesso ao R2 através do Workers (em vez de expor URLs públicas diretamente) permite adicionar qualquer lógica no Workers: verificar permissões do usuário, rastrear contagens de download, adicionar controle de cache, etc. Com o vibefast.app, a configuração de binding do R2 já está feita, basta usar `env.R2` diretamente no Workers.

-----

## Quando Considerar o S3?

O R2 é melhor para apps no ecossistema Cloudflare em quase todos os aspectos, mas algumas situações ainda podem precisar do S3:

- Você precisa dos recursos avançados do S3 (como Intelligent Tiering, armazenamento frio Glacier)
- Seu app não está no Cloudflare, as vantagens do R2 não são tão óbvias
- Você precisa de integração profunda com serviços existentes dependentes do S3

Para a maioria dos apps indie rodando no Cloudflare, R2 é a escolha mais natural.

-----

## Resumo

Se seu app roda no Cloudflare, usar R2 para armazenar imagens e arquivos de mídia é mais econômico do que S3:

- Zero taxas de egresso, mais tráfego = mais economia
- Direct Binding com Workers, sem internet pública
- Plano gratuito suficiente para a maioria dos apps começando
- Compatível com API S3, baixos custos de migração

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Voltar ao índice do curso](../README-pt-br.md)

Pronto para começar com um template completo Cloudflare full-stack?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, preço sobe para $199 em 1º de junho de 2026.
