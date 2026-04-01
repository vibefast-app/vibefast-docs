# No entres en pánico: Usa IA para leer mensajes de error y resolver problemas

[English](../en/18-debug-and-errors-en.md) · [繁中](../zh/18-debug-and-errors-zh.md) · [Español](../es/18-debug-and-errors-es.md) · [日本語](../jp/18-debug-and-errors-jp.md) · [Português (BR)](../pt-br/18-debug-and-errors-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~8 minutos

-----

## Cuando veas un mensaje de error en rojo — no lo cierres

La reacción típica de un principiante ante un error es: pánico, cerrar la terminal, intentar de nuevo, mismo resultado.

Pero los mensajes de error no son tu enemigo. **Son tu pista más importante.** Te dicen exactamente qué salió mal y por qué. Aprende a trabajar con ellos y resolverás problemas diez veces más rápido.

Y lo más importante: **no necesitas entender cada error tú mismo. Solo pégalo a la IA.**

-----

## Tres tipos de errores

### Errores de sintaxis

El código está escrito incorrectamente — falta un paréntesis, una comilla sin cerrar, una palabra clave mal escrita. Estos son los más fáciles de arreglar porque Cursor normalmente resalta la línea problemática con un subrayado rojo inmediatamente.

```
SyntaxError: Unexpected token '}'
```

Solo dile a la IA: "Hay un error de sintaxis en este código. Encuéntralo y arréglalo."

### Errores en tiempo de ejecución

La sintaxis está bien, pero el código falla cuando se ejecuta. Ejemplos comunes:

```
TypeError: Cannot read properties of undefined (reading 'name')
```

Esto normalmente significa que esperabas que una variable tuviera un valor, pero en realidad es `undefined` — por ejemplo, una consulta a la base de datos no devolvió nada y intentaste acceder a una propiedad del resultado.

```
ReferenceError: env is not defined
```

Usaste una variable que no ha sido declarada o pasada al scope actual.

### Errores de lógica

Los más difíciles de encontrar. El código se ejecuta sin lanzar error, pero el resultado es incorrecto — el precio del producto se calculó mal, el stock no se dedujo correctamente, el usuario no fue redirigido a la página correcta después del login.

Estos requieren rastrear la ejecución de tu programa para encontrar dónde la lógica falló.

-----

## Primer punto de verificación: Valida el código de la IA con npm run build

Después de que la IA escriba código, **no te apresures a ejecutar `npm run dev` o desplegar. Ejecuta `npm run build` primero.**

```bash
npm run build
```

`npm run build` hace dos cosas:

1. **Verificación de tipos TypeScript** — detecta errores de tipos, variables no definidas, llamadas a funciones incorrectas
1. **Compila el código** — confirma que todo el proyecto se puede empaquetar exitosamente

Esto normalmente es más rápido que iniciar el servidor de desarrollo, y detecta muchos problemas antes de que siquiera ejecutes la app.

**Solo continúa cuando el build tenga éxito:**

```bash
✓ Built in 2.3s   ← esto es lo que quieres ver
```

Si el build falla, la terminal te dice exactamente qué archivo y línea tiene el problema:

```
src/routes/checkout.ts:23:18 - error TS2532: Object is possibly 'undefined'.
```

Esto es mucho más rápido que esperar a descubrir errores en tiempo de ejecución. Pega el error a la IA, deja que arregle el código, ejecuta build de nuevo para confirmar que pasa, luego continúa.

**Haz esto un hábito: La IA escribe código → `npm run build` → pasa → `npm run dev` para probar la funcionalidad.**

-----

## El proceso estándar cuando te encuentras un error

### Paso 1: Copia el mensaje de error completo

No solo hagas una captura de pantalla o notes "hubo un error." Copia el texto completo del error, incluyendo:

- El tipo de error (TypeError, SyntaxError, etc.)
- La descripción del error
- El archivo y número de línea donde ocurrió (el stack trace)

```
TypeError: Cannot read properties of undefined (reading 'price')
    at handleCheckout (api/src/routes/checkout.ts:23:18)
    at Object.<anonymous> (api/src/index.ts:45:5)
```

Esto te dice: en la línea 23 de `checkout.ts`, algo es `undefined` e intentaste leer su propiedad `price`.

### Paso 2: Pégalo a la IA con contexto

```
Estoy construyendo una funcionalidad de checkout en Cloudflare Workers y me sale este error:

TypeError: Cannot read properties of undefined (reading 'price')
    at handleCheckout (api/src/routes/checkout.ts:23:18)

Aquí está mi código:
[pegar el código relevante]

Lo que intento hacer: después de que el usuario haga clic en "Comprar", el backend consulta un producto de D1,
luego crea una Stripe Checkout Session.

Explica qué significa este error, qué lo está causando probablemente, y cómo arreglarlo.
```

### Paso 3: Entiende la explicación antes de pedir que lo arreglen

Después de que la IA explique el problema, asegúrate de entender qué salió mal antes de pedir que arregle el código. No solo digas "arréglalo" — pregunta "¿por qué pasó esto?" primero. De esa forma, cuando veas algo similar en el futuro, sabrás dónde buscar.

-----

## console.log: La herramienta de depuración más simple

Cuando no estás seguro de qué valor tiene una variable, el enfoque más directo es imprimirlo con `console.log`:

```typescript
export async function handleCheckout(request: Request, env: Env) {
  const { productId } = await request.json()
  
  const product = await env.DB.prepare(
    'SELECT * FROM products WHERE id = ?'
  ).bind(productId).first()
  
  // Añade esta línea para ver qué contiene realmente product
  console.log('product:', product)
  
  // Si product es null o undefined, la siguiente línea lanzará un error
  const price = product.price
}
```

### Ver console.log en Cloudflare Workers

Los logs de Cloudflare Workers no aparecen en las herramientas de desarrollador del navegador. Necesitas verlos a través de Cloudflare:

**Método 1: Tiempo real (mejor durante desarrollo)**

```bash
npx wrangler tail
```

Después de ejecutar esto, todos los logs de tu Worker se transmiten directamente a tu terminal en tiempo real.

**Método 2: Dashboard**
Cloudflare Dashboard → Workers & Pages → tu Worker → Logs

-----

## Técnicas avanzadas de depuración con IA

### Pide a la IA que añada logs de depuración

```
Esta función no produce el resultado correcto. Añade sentencias console.log
en los pasos clave para que pueda rastrear cuál es cada valor:

[pegar tu código]
```

### Pide a la IA que explique un stack trace

Un stack trace muestra la cadena de llamadas a funciones que llevaron al error. Es difícil de leer al principio — simplemente pégalo a la IA:

```
Explica qué me está diciendo este stack trace. ¿Dónde se originó el error?

[pegar el stack trace completo]
```

### Pide a la IA que explique código que no entiendes

Cuando encuentres código que no tiene sentido, no lo saltes:

```
Explica qué hace este código en lenguaje sencillo.
Particularmente no entiendo estas líneas:

[pegar el código]
```

-----

## Los errores más comunes en Cloudflare Workers

### `env is not defined` o `env.DB is not defined`

Usaste `env` en algún lugar, pero la función no recibe `env` como parámetro.

```typescript
// ❌ Esta función no tiene acceso a env
async function getProduct(productId: string) {
  const product = await env.DB.prepare(...) // ¿de dónde viene env?
}

// ✅ Pasa env explícitamente
async function getProduct(productId: string, env: Env) {
  const product = await env.DB.prepare(...)
}
```

### `Cannot read properties of null`

Una consulta a la base de datos no encontró registro coincidente y devolvió `null`, pero accediste directamente a una propiedad.

```typescript
const product = await env.DB.prepare('SELECT * FROM products WHERE id = ?')
  .bind(productId).first()

// ❌ Si product es null, esto lanza un error
const price = product.price

// ✅ Verifica primero
if (!product) {
  return Response.json({ error: 'Product not found' }, { status: 404 })
}
const price = product.price
```

### Desplegado exitosamente pero la funcionalidad no funciona

Primero confirma que estás viendo la última versión — Cloudflare tiene caché, y a veces los cambios tardan unos segundos en propagarse después del despliegue.

Usa curl para verificar que tu API devuelve lo que esperas:

```bash
curl -s https://tu-app.com/api/products | jq
```

Si eso se ve bien, ejecuta `wrangler tail` y busca mensajes de error en los logs.

-----

## El enfoque más directo: Haz una captura y muéstrasela a la IA

A veces describir el problema en texto es tedioso — "algo en la página está en la posición incorrecta," "el botón no responde al hacer clic," "el error aparece en un lugar extraño."

**Simplemente haz una captura de pantalla y pégala directamente en el chat de IA de Cursor.**

Las capturas pueden mostrar:

- La pantalla de error tal como aparece en el navegador
- Salida de error de la terminal (más completa que texto copiado — se preservan colores y formato)
- Resultados de UI que no coinciden con lo esperado
- Logs de error en el Dashboard de Cloudflare

Dile a la IA:

```
Aquí está mi problema — captura abajo:
[pegar captura]

Lo que esperaba: [describir]
Lo que veo realmente: [describir]
Ayúdame a averiguar qué está mal.
```

Una captura es más precisa que una descripción en texto. La IA puede ver exactamente lo que tú ves sin depender de tu traducción.

Especialmente útil para:

- **Problemas de layout**: una captura supera describir "algo se movió al lado equivocado"
- **Errores de terminal**: la captura preserva el resaltado de color y el stack trace completo mejor que texto plano
- **Logs de Cloudflare**: captura todo el panel de logs para que la IA pueda ver timestamps y el error completo en contexto

-----

## La mentalidad correcta

Encontrar un error no significa que fallaste. Significa que estás aprendiendo.

Cada error tiene una causa. Cada causa se puede encontrar. No necesitas descifrar todos los detalles tú mismo — la IA es tu compañero de depuración. Pega el error y el código, y puede ayudarte a rastrear la mayoría de problemas.

**El flujo correcto cuando te encuentras un error: copia el error completo → pégalo a la IA → entiende la causa → arréglalo → sigue adelante.**

No entres en pánico. No cierres la terminal. No reintentes lo mismo esperando un resultado diferente.

-----

**Danko Peng**  
[@dankopeng](https://x.com/dankopeng) · [vibefast.app](https://vibefast.app)

-----

👉 [Volver al índice del curso](../README-es.md)

¿Listo para empezar con una plantilla full-stack completa de Cloudflare?  
👉 **[vibefast.app](https://vibefast.app)** — Precio early bird $99, sube a $199 el 1 de junio de 2026.
