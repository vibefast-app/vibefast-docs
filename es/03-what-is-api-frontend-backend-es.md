# ¿Qué es una API? Explicación sencilla de la comunicación frontend-backend

[English](../en/03-what-is-api-frontend-backend-en.md) · [繁中](../zh/03-what-is-api-frontend-backend-zh.md) · [Español](../es/03-what-is-api-frontend-backend-es.md) · [日本語](../jp/03-what-is-api-frontend-backend-jp.md) · [Português (BR)](../pt-br/03-what-is-api-frontend-backend-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~7 minutos

-----

## Una analogía que usas todos los días

Vas a un restaurante.

No entras a la cocina a cocinar tú mismo. Le dices al camarero lo que quieres. El camarero lleva tu pedido a la cocina, la cocina lo prepara, y el camarero te trae la comida.

**El camarero es la API.**

Una API es el puente de comunicación entre el frontend (tú) y el backend (la cocina). El frontend no necesita saber cómo funciona el backend, solo cómo hablar con la API.

-----

## ¿Qué es exactamente una API?

**API (Application Programming Interface)** es un conjunto de reglas que define cómo se comunican dos sistemas.

En el contexto de las aplicaciones web, normalmente significa:

- **Frontend** (página en el navegador) envía una petición
- **API del backend** (servidor) recibe la petición, la procesa y devuelve resultados

Una petición API típica se ve así:

```
El frontend pregunta: "Dame los datos del usuario con userId = 123"
→ GET /api/users/123

El backend responde: "Aquí están sus datos"
→ { "id": 123, "name": "Danko", "email": "danko@example.com" }
```

-----

## Métodos HTTP: ¿Qué le estás diciendo a la API?

Las peticiones API tienen cuatro acciones comunes, correspondientes a diferentes métodos HTTP:

|Método           |Propósito    |Analogía        |
|-----------------|-------------|----------------|
|`GET`            |Leer datos   |"Muéstrame el menú"|
|`POST`           |Crear datos  |"Quiero pedir este plato"|
|`PUT` / `PATCH`  |Actualizar datos|"Cambia mi pedido a otro plato"|
|`DELETE`         |Eliminar datos|"Cancela este plato"|

-----

## Respuestas de la API: ¿Qué dice el backend?

Las respuestas del backend normalmente contienen dos partes:

**Código de estado HTTP** — un número que te dice el resultado:

|Código|Significado                  |
|------|-----------------------------|
|`200` |Éxito                        |
|`201` |Creado exitosamente          |
|`400` |Tu petición tiene problemas (Bad Request)|
|`401` |No has iniciado sesión (Unauthorized)|
|`403` |No tienes permiso (Forbidden)|
|`404` |No encontrado                |
|`500` |Error del servidor           |

**Contenido de la respuesta** — normalmente en formato JSON:

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

## Arquitectura API de VibeFast

VibeFast usa **Service Binding** para que el frontend y el backend se comuniquen directamente dentro de la red interna de Cloudflare, no a través de internet público.

```
Navegador
  ↓ Petición HTTP
Remix Frontend Worker
  ↓ Service Binding (interno, cero CORS)
Workers API
  ↓
Base de datos D1
```

Esto significa:

- La API del backend no tiene URL pública, acceso externo imposible
- No se necesita configuración de CORS entre frontend y backend
- Cada llamada API ahorra un viaje de red, mayor velocidad

-----

## APIs de terceros: Tu app también llama a APIs de otros

Tu app no solo tiene su propia API, también llama a APIs de otros:

- **Stripe API**: "Crea un pago para mí"
- **Resend API**: "Envía un email a este usuario"
- **Cloudflare API**: `npm run deploy` llama a la API de Cloudflare por detrás

Estas APIs de terceros requieren claves API para usarse — por eso el artículo anterior decía que las claves API deben mantenerse seguras en variables de entorno.

-----

## Diseñar endpoints API con IA

Este es un escenario práctico de Vibe Coding. Puedes decirle directamente a la IA:

> "Crea un endpoint API en Cloudflare Workers, GET /api/posts, que devuelva todos los artículos publicados, ordenados por fecha de creación descendente, 10 por página."

La IA generará código como este:

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

No necesitas escribir esto tú mismo, pero entender qué hace te ayuda a juzgar mejor si la salida de la IA es correcta.

-----

## Resumen

Las APIs son las reglas de comunicación entre frontend y backend. Recuerda tres cosas:

1. El frontend usa métodos HTTP (GET/POST/PUT/DELETE) para decirle a la API qué hacer
2. El backend usa códigos de estado (200/404/500) para decirle al frontend el resultado
3. Los datos normalmente se pasan en formato JSON

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Volver al índice del curso](../README-es.md)

¿Listo para empezar con una plantilla full-stack completa de Cloudflare?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Precio early bird $99, sube a $199 el 1 de junio de 2026.
