# R2 vs S3: Por qué no uso AWS para almacenar imágenes

[English](../en/08-cloudflare-r2-vs-aws-s3-en.md) · [繁中](../zh/08-cloudflare-r2-vs-aws-s3-zh.md) · [Español](../es/08-cloudflare-r2-vs-aws-s3-es.md) · [日本語](../jp/08-cloudflare-r2-vs-aws-s3-jp.md) · [Português (BR)](../pt-br/08-cloudflare-r2-vs-aws-s3-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~6 minutos

-----

## ¿Tu app necesita almacenar imágenes?

Avatares de usuarios, capturas de productos, imágenes de portada de blog — cualquier app con subida de archivos multimedia necesita un lugar donde almacenarlos.

Las bases de datos no son adecuadas para almacenar imágenes (las bases de datos son para datos de texto estructurado), necesitas un servicio dedicado de "almacenamiento de objetos."

La opción más común es **AWS S3**. Pero si tu app corre en Cloudflare, hay una mejor opción: **Cloudflare R2**.

-----

## El coste oculto de S3: costes de transferencia de salida

El precio de AWS S3 tiene una trampa en la que mucha gente cae: **costes de egress (transferencia de salida)**.

- Almacenar imágenes en S3: coste de almacenamiento (~$0.023/GB/mes)
- Los usuarios descargan imágenes: **coste de egress (~$0.09/GB)**

¿No parece mucho? Calculemos:

Digamos que tu app tiene 1.000 usuarios, cada uno descarga un promedio de 10 imágenes al día, 500KB cada una:

```
Egress diario = 1.000 × 10 × 0,5MB = 5.000MB = 5GB
Coste de egress diario = 5GB × $0,09 = $0,45
Coste de egress mensual = $0,45 × 30 = $13,5
```

Cuanto más exitosa es tu app, más alta la factura. Algunas empresas solo descubren que los costes de egress de S3 dominan sus gastos después de que su app se vuelve viral.

-----

## La lógica de precios de R2

La estrategia de precios de Cloudflare R2 es completamente diferente:

|Concepto  |AWS S3     |Cloudflare R2      |
|----------|-----------|-------------------|
|Almacenamiento|$0,023/GB/mes|$0,015/GB/mes|
|Egress    |$0,09/GB   |**$0 — Completamente gratis**|
|Nivel gratuito|Limitado|10GB almacenamiento + 1M operaciones/mes|

Cero costes de egress hacen que la ventaja de R2 sea muy clara para apps con mucho tráfico. Cuanto más se descargan tus imágenes, mayor es la diferencia de coste frente a S3.

-----

## Diferencias técnicas entre R2 y S3

|            |AWS S3   |Cloudflare R2         |
|------------|---------|----------------------|
|Compatibilidad API|API nativa de S3|Compatible con API de S3|
|Distribución global|Configuración multi-región manual|Distribución global automática|
|Integración con Workers|Llamadas entre redes|Binding directo, sin internet público|
|Configuración CORS|Configuración manual|Se gestiona a través de Workers, puede ser cero CORS|

**La compatibilidad con la API de S3** es importante — la mayoría de paquetes que soportan S3 (como `@aws-sdk/client-s3`) pueden operar R2 directamente, solo cambiando la URL del endpoint. Esto hace que los costes de migración sean muy bajos.

-----

## Usar R2 en Cloudflare Workers

Patrón básico de subida y lectura:

```typescript
// Subir imagen a R2
await env.R2.put(`images/${filename}`, fileBuffer, {
  httpMetadata: { contentType: file.type }
})

// Devolver archivo de R2 a través de Workers
const object = await env.R2.get(`images/${filename}`)
if (!object) return new Response("Not found", { status: 404 })

return new Response(object.body, {
  headers: { "Content-Type": object.httpMetadata?.contentType || "application/octet-stream" }
})
```

Hacer proxy del acceso a R2 a través de Workers (en lugar de exponer URLs públicas directamente) te permite añadir cualquier lógica en Workers: verificar permisos de usuario, rastrear conteos de descargas, añadir control de caché, etc. Con vibefast.app, la configuración del binding de R2 ya está resuelta, solo usa `env.R2` directamente en Workers.

-----

## ¿Cuándo considerar S3?

R2 es mejor para apps en el ecosistema Cloudflare en casi todos los aspectos, pero algunas situaciones podrían seguir necesitando S3:

- Necesitas funciones avanzadas de S3 (como Intelligent Tiering, almacenamiento frío Glacier)
- Tu app no está en Cloudflare, las ventajas de R2 no son tan obvias
- Necesitas integración profunda con servicios existentes que dependen de S3

Para la mayoría de apps independientes corriendo en Cloudflare, R2 es la opción más natural.

-----

## Resumen

Si tu app corre en Cloudflare, usar R2 para almacenar imágenes y archivos multimedia es más rentable que S3:

- Cero costes de egress, más tráfico = más ahorro
- Binding directo con Workers, sin internet público
- Nivel gratuito suficiente para la mayoría de apps que empiezan
- Compatible con API de S3, costes de migración bajos

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Volver al índice del curso](../README-es.md)

¿Listo para empezar con una plantilla full-stack completa de Cloudflare?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Precio early bird $99, sube a $199 el 1 de junio de 2026.
