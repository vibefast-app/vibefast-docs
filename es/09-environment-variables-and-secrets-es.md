# ¿Qué son las variables de entorno? Por qué no puedes poner claves API en el código

[English](../en/09-environment-variables-and-secrets-en.md) · [繁中](../zh/09-environment-variables-and-secrets-zh.md) · [Español](../es/09-environment-variables-and-secrets-es.md) · [日本語](../jp/09-environment-variables-and-secrets-jp.md) · [Português (BR)](../pt-br/09-environment-variables-and-secrets-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~6 minutos

-----

## Empecemos con un desastre real

En 2022, un desarrollador subió código que contenía una clave API de AWS a GitHub.

En cuatro minutos, programas automatizados escanearon y encontraron la clave.

Ocho horas después, recibió una factura de AWS de $50.000.

Esto no es un caso aislado. Miles de claves API se exponen accidentalmente en GitHub cada día, escaneadas y abusadas por herramientas automatizadas.

-----

## ¿Qué son las variables de entorno?

Tu app necesita cierta "información secreta" para funcionar:

- Clave API de Stripe (para pagos)
- Clave API de Resend (para enviar emails)
- Secreto JWT (para firmar tokens de login)
- Cadenas de conexión a base de datos

Esto no puede escribirse en el código, porque el código se sube a GitHub.

**Las variables de entorno** almacenan esta información secreta fuera del código — en el "entorno" del sistema operativo o de la plataforma de despliegue. Tu código lee estos valores en tiempo de ejecución, pero los valores en sí no existen en el código.

-----

## Desarrollo local: archivos .env

Durante el desarrollo local, normalmente creas un archivo `.env`:

```
STRIPE_SECRET_KEY=sk_live_xxxxx
RESEND_API_KEY=re_xxxxx
JWT_SECRET=my-super-secret-key
```

Luego lo lees en el código así:

```typescript
// Node.js / Remix
const stripeKey = process.env.STRIPE_SECRET_KEY

// Cloudflare Workers
const stripeKey = env.STRIPE_SECRET_KEY
```

**Crítico: Los archivos `.env` NUNCA deben subirse a GitHub.**

Confirma que tu `.gitignore` incluye esta línea:

```
.env
.env.local
.env.production
```

-----

## Entorno de producción: Gestión de secretos en la plataforma

El `.env` local solo funciona en tu propio ordenador. Cuando despliegas a producción, necesitas configurar las variables de entorno en la plataforma.

**Método en Cloudflare Workers:**

```bash
# Configurar secreto con Wrangler CLI
npx wrangler secret put JWT_SECRET
# Después de introducir el valor, Cloudflare lo encripta y almacena, no aparecerá en ningún log
```

O configúralo manualmente en Cloudflare Dashboard en Workers → Settings → Variables.

-----

## Errores comunes

### Error 1: Subir .env a GitHub

```bash
# Solución: eliminar del historial de git
git rm --cached .env
git commit -m "remove .env from tracking"
```

Pero si ya se subió, asume que la clave está expuesta, ve inmediatamente a la plataforma correspondiente a revocar y regenerar una nueva clave.

### Error 2: Clave hardcodeada en el código

```typescript
// ❌ Esto expone tu clave
const stripe = new Stripe("sk_live_xxxxx")

// ✅ Enfoque correcto
const stripe = new Stripe(env.STRIPE_SECRET_KEY)
```

### Error 3: console.log de valores .env

```typescript
// ❌ Los logs podrían ser registrados
console.log("Stripe key:", env.STRIPE_SECRET_KEY)

// ✅ Solo confirma la existencia, no imprimas el valor
console.log("Stripe key exists:", !!env.STRIPE_SECRET_KEY)
```

-----

## Resumen

El concepto central de las variables de entorno es simple: **los secretos no pertenecen al código, pertenecen al entorno.**

Tres principios para recordar:

1. Añade `.env` a `.gitignore`
2. Configura los secretos de producción en la plataforma, no en el código
3. Si se exponen accidentalmente, revoca y regenera inmediatamente, sin excepciones

-----

**Danko Peng**  
[@dankopeng](https://x.com/dankopeng) · [vibefast.app](https://vibefast.app)

-----

👉 [Volver al índice del curso](../README-es.md)

¿Listo para empezar con una plantilla full-stack completa de Cloudflare?  
👉 **[vibefast.app](https://vibefast.app)** — Precio early bird $99, sube a $199 el 1 de junio de 2026.
