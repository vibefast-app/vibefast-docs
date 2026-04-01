# Fundamentos de seguridad para Vibe Coders: Confirma esto antes de salir a producción

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~10 minutos

-----

## No necesitas ser experto en seguridad, pero necesitas saber esto

Muchos Vibe Coders solo empiezan a pensar en seguridad después de que su app está en producción. Esto es normal — cuando usas IA para construir funcionalidades rápidamente, la seguridad fácilmente se queda en la lista de "ya lo haré después."

Pero algunas cosas no son "ya lo haré después," son "debo confirmar antes de salir a producción."

Este artículo no pretende convertirte en ingeniero de seguridad, sino aclarar los puntos de verificación más importantes en un lenguaje que puedas entender.

-----

## 1. Las claves secretas NUNCA deben aparecer en el código

Este es el error más común y más grave.

```typescript
// ❌ Mal: clave secreta hardcodeada en el código
const token = jwt.sign(payload, "my-super-secret-key-123")

// ✅ Correcto: leer de variables de entorno
const token = jwt.sign(payload, env.JWT_SECRET)
```

¿Por qué es tan grave? Porque tu código normalmente se sube a GitHub. Mientras el repo sea público, o se ponga público accidentalmente un día, cualquiera puede ver tu clave secreta, luego falsificar la identidad de cualquier usuario y saltarse toda la autenticación.

**Cómo lo maneja VibeFast:** `npm run setup` genera automáticamente el secreto JWT y lo escribe en las variables de entorno de Cloudflare Workers. Nunca verás el valor de la clave secreta en el código.

**Lo que necesitas confirmar:**

- El archivo `.env` está añadido a `.gitignore`
- Todos los secretos (clave de Stripe, clave de Resend, secreto JWT) se leen de `env`, no están hardcodeados
- Antes de subir código a GitHub, busca en el repo cadenas como `sk_live_`, `re_`, `secret`

-----

## 2. Nunca confíes directamente en la entrada del usuario

Los lugares donde tu app recibe entrada del usuario — formularios, parámetros de URL, peticiones API — cada uno es un vector de ataque potencial.

### SQL Injection

Si concatenas directamente la entrada del usuario en consultas SQL, los atacantes pueden inyectar sentencias SQL maliciosas:

```typescript
// ❌ Peligroso: concatenación directa de strings
const query = `SELECT * FROM users WHERE email = '${userInput}'`
// El atacante introduce: ' OR '1'='1
// Resultado: SELECT * FROM users WHERE email = '' OR '1'='1'
// Esto devuelve todos los datos de usuarios

// ✅ Seguro: usar consultas parametrizadas
const result = await db.prepare(
  "SELECT * FROM users WHERE email = ?"
).bind(userInput).first()
```

D1 soporta consultas parametrizadas, todas las operaciones de base de datos de VibeFast usan este método. Si escribes SQL tú mismo, siempre usa marcadores `?`, nunca concatenación de strings.

### XSS (Cross-Site Scripting)

Si renderizas directamente la entrada del usuario como HTML, los atacantes pueden inyectar scripts maliciosos:

```typescript
// ❌ Peligroso
<div dangerouslySetInnerHTML={{ __html: userInput }} />

// ✅ Seguro: dejar que React/Remix escape automáticamente
<div>{userInput}</div>
```

Remix escapa automáticamente por defecto, mientras no uses `dangerouslySetInnerHTML`, es básicamente seguro.

-----

## 3. Los endpoints API deben verificar identidad y permisos

Cada endpoint API que requiera login debe verificar el token JWT. Esto suena obvio, pero mucha gente lo olvida durante el desarrollo rápido.

```typescript
// ❌ Olvidó la verificación
export async function action({ request }: ActionFunctionArgs) {
  // Procesa directamente, no confirmó si el usuario está logueado
  const data = await request.json()
  await db.prepare("DELETE FROM posts WHERE id = ?").bind(data.id).run()
}

// ✅ Verificar identidad primero
export async function action({ request, context }: ActionFunctionArgs) {
  const user = await requireAuth(request, context.env)
  // Confirmar que está logueado antes de continuar
  const data = await request.json()
  await db.prepare("DELETE FROM posts WHERE id = ?").bind(data.id).run()
}
```

Yendo más allá: **no solo verificar identidad, también verificar permisos**. El Usuario A no debería poder eliminar los posts del Usuario B, aunque ambos estén logueados.

```typescript
// ✅ Verificar identidad + permisos
const post = await db.prepare(
  "SELECT * FROM posts WHERE id = ?"
).bind(postId).first()

if (post.authorId !== user.userId) {
  throw new Response("Forbidden", { status: 403 })
}
```

-----

## 4. Las operaciones sensibles necesitan limitación de tasa

Si tu API de login no tiene limitación de tasa, los atacantes pueden usar programas para probar automáticamente millones de combinaciones de contraseñas (fuerza bruta).

Cloudflare tiene una ventaja natural aquí — puedes usar la función Rate Limiting de Cloudflare para bloquear frecuencias de peticiones anormales antes de que el tráfico entre a Workers, sin necesidad de implementarlo en código.

Endpoints que al menos deberían estar limitados:

- `/login`, `/signup` — prevenir fuerza bruta
- `/api/send-email` — prevenir que se use para enviar spam
- Cualquier operación que genere costes (pagos de Stripe, subidas a R2)

-----

## 5. HTTPS es estándar, no opcional

Tu app debe usar HTTPS, no HTTP.

Buenas noticias: **las apps desplegadas en Cloudflare habilitan HTTPS automáticamente**, sin configuración extra necesaria. Cloudflare gestiona la solicitud y renovación de certificados SSL, no necesitas preocuparte en absoluto.

Pero una cosa a confirmar: si tienes un dominio personalizado, confirma en Cloudflare Dashboard que la configuración SSL/TLS sea Full o Full (Strict), no uses Flexible (en modo Flexible, el segmento de Cloudflare a tu origen sigue siendo HTTP, tiene riesgos de seguridad).

-----

## 6. Los mensajes de error no deben filtrar información interna

Durante el desarrollo puede que estés acostumbrado a mostrar mensajes de error detallados para facilitar la depuración:

```typescript
// ❌ En producción no deberías hacer esto
catch (error) {
  return json({ error: error.message, stack: error.stack })
}
```

`error.stack` expone la estructura de tu código, rutas de archivos, incluso nombres de funciones, proporcionando información útil a los atacantes.

```typescript
// ✅ Entorno de producción
catch (error) {
  console.error(error) // Solo registrar el error completo en logs
  return json({ error: "Something went wrong" }, { status: 500 })
}
```

-----

## 7. Actualizar dependencias regularmente

Tu app depende de muchos paquetes de terceros, estos paquetes ocasionalmente descubren vulnerabilidades de seguridad y lanzan correcciones. Si sigues usando versiones antiguas, estarás expuesto a vulnerabilidades conocidas.

```bash
# Verificar qué paquetes tienen problemas de seguridad
npm audit

# Auto-corregir problemas menores
npm audit fix
```

No necesitas hacer esto diariamente, pero al menos ejecuta `npm audit` una vez al mes, prioriza las vulnerabilidades de severidad high/critical.

-----

## Checklist de seguridad pre-lanzamiento

```
□ Todas las claves secretas en variables de entorno, no hardcodeadas
□ .env añadido a .gitignore
□ Todas las consultas SQL usan enfoque parametrizado
□ Todas las APIs que requieren login verifican JWT
□ Endpoints API sensibles tienen limitación de tasa (o usan Cloudflare Rate Limiting)
□ Mensajes de error en producción no exponen stack traces
□ Ejecutado npm audit, sin vulnerabilidades high/critical
□ SSL/TLS de Cloudflare configurado en Full o Full (Strict)
```

-----

## Resumen

La seguridad no es "ya lo haré cuando tenga tiempo," pero tampoco necesita ser perfecta de una vez.

Para la mayoría de apps independientes, hacer bien los siete puntos mencionados en este artículo ya te hace más seguro que el 80% de las apps pequeñas del mercado. Los atacantes normalmente buscan los objetivos más fáciles — mientras no seas el más débil, puedes evitar la mayoría de riesgos.

-----

¿Preguntas? Encuéntrame en X [@dankopeng](https://x.com/dankopeng).

VibeFast ya tiene la arquitectura de seguridad más básica configurada para ti, permitiéndote enfocarte en el producto:  
👉 **[vibefast.app](https://vibefast.app)** — Precio early bird $99, sube a $199 el 1 de junio de 2026.
