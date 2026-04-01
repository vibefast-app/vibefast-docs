# Pagos con Stripe: Del sandbox de pruebas a ir en vivo

[English](../en/20-stripe-payment-complete-guide-en.md) · [繁中](../zh/20-stripe-payment-complete-guide-zh.md) · [Español](../es/20-stripe-payment-complete-guide-es.md) · [日本語](../jp/20-stripe-payment-complete-guide-jp.md) · [Português (BR)](../pt-br/20-stripe-payment-complete-guide-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~10 minutos

-----

## Los pagos son la funcionalidad más importante de tu app

Cuando los usuarios pagan, significa que tu producto es genuinamente valioso.

Stripe es el servicio de pago en línea más usado hoy — documentación clara, API bien diseñada, y soporte para prácticamente todas las tarjetas de crédito y métodos de pago principales. Esta guía te lleva por todo el flujo, desde crear una cuenta hasta tu primera transacción real.

-----

## Paso 1: Crear una cuenta de Stripe

Ve a [stripe.com](https://stripe.com) y regístrate.

Después de introducir tu información básica, Stripe requerirá **verificación de identidad (KYC)**, incluyendo:

- Información legal personal o empresarial
- Una cuenta bancaria (para recibir pagos)
- Documentos de identidad

Este proceso normalmente toma 1–3 días hábiles. Hasta que se complete la verificación, solo puedes usar el modo de prueba — no se puede procesar dinero real.

**Nota para usuarios fuera de países soportados:** Stripe no está disponible en todos los países. Si Stripe no está disponible donde estás, tienes dos opciones:

**Opción A: Usar un procesador de pagos local**
Busca proveedores de pago que soporten tu región. La desventaja típica es soporte limitado para tarjetas internacionales, lo que dificulta vender a clientes en el extranjero.

**Opción B: Registrar una empresa en EE.UU. y solicitar Stripe**
Incorporar una LLC en EE.UU. (a través de servicios como Stripe Atlas o Firstbase) te permite solicitar Stripe. El coste único es aproximadamente $500, con tarifas anuales estatales de unos $300. Este es un camino común para emprendedores independientes que quieren aceptar pagos globales y viene con una cuenta bancaria en EE.UU.

Si solo atiendes a clientes locales, la Opción A es más simple. Si estás construyendo para un mercado global, vale la pena considerar la Opción B.

-----

## Paso 2: Entender el modo de prueba y el modo en vivo

Stripe tiene dos entornos completamente separados:

**Modo de prueba (Test Mode)**

- Las claves API empiezan con `sk_test_`
- Todas las transacciones son simuladas — no se mueve dinero real
- Usa los números de tarjeta de prueba de Stripe para recorrer los flujos

**Modo en vivo (Live Mode)**

- Las claves API empiezan con `sk_live_`
- Transacciones reales, dinero real

**Siempre desarrolla y prueba primero en modo de prueba. Solo cambia a modo en vivo después de confirmar que todo funciona.**

Números de tarjeta de prueba de Stripe:

```
Número de tarjeta: 4242 4242 4242 4242
Vencimiento: cualquier fecha futura (ej. 12/30)
CVV: cualquier tres dígitos (ej. 123)
```

Esta tarjeta siempre tiene éxito en modo de prueba. Para probar un pago rechazado:

```
Número de tarjeta: 4000 0000 0000 0002
→ Esta tarjeta siempre es rechazada
```

-----

## Paso 3: Obtener tus claves API

En Stripe Dashboard → Developers → API keys, encontrarás dos claves:

- **Publishable key** (`pk_test_...`): segura para usar en el frontend para inicializar Stripe.js
- **Secret key** (`sk_test_...`): solo backend — nunca expongas esto en código frontend o GitHub

Añade ambas a tus variables de entorno:

```
STRIPE_PUBLISHABLE_KEY=pk_test_xxxxx
STRIPE_SECRET_KEY=sk_test_xxxxx
```

-----

## Paso 4: Entender cómo funciona Stripe Checkout

Stripe ofrece dos enfoques para aceptar pagos:

### Stripe Checkout (recomendado para Vibe Coders)

Stripe proporciona una página de pago preconstruida. Creas una Checkout Session, rediriges al usuario a la página alojada de Stripe, y Stripe los redirige de vuelta a tu app cuando se completa el pago.

```
El usuario hace clic en "Comprar"
  ↓
Tu backend crea una Checkout Session
  ↓
El usuario es redirigido a la página de pago de Stripe
  ↓
El usuario introduce los datos de la tarjeta (en la página de Stripe — tú nunca los ves)
  ↓
Pago exitoso → Stripe redirige a tu URL de éxito
Pago fallido → Stripe redirige a tu URL de cancelación
```

La ventaja: no construyes un formulario de pago, y Stripe maneja toda la seguridad.

### Stripe Elements (avanzado)

Incrusta el formulario de pago de Stripe directamente dentro de tu propia página — apariencia totalmente personalizable, pero requiere más configuración. Checkout es suficiente para la mayoría de apps independientes que están empezando.

-----

## Paso 5: Implementar la Checkout Session

Dile a la IA:

```
Crea un endpoint POST /api/checkout en Cloudflare Workers.

Requisitos:
- Acepta productId y userId
- Crea una Stripe Checkout Session
- Lee la información del producto de la tabla products de D1
- En caso de éxito, redirige a /success?session_id={CHECKOUT_SESSION_ID}
- En caso de cancelación, redirige a /shop

Variable de entorno: env.STRIPE_SECRET_KEY
```

La IA generará algo como esto:

```typescript
import Stripe from 'stripe'

export async function handleCheckout(request: Request, env: Env) {
  const { productId, userId } = await request.json()

  const product = await env.DB.prepare(
    'SELECT * FROM products WHERE id = ?'
  ).bind(productId).first()

  if (!product) {
    return Response.json({ error: 'Product not found' }, { status: 404 })
  }

  const stripe = new Stripe(env.STRIPE_SECRET_KEY)

  const session = await stripe.checkout.sessions.create({
    payment_method_types: ['card'],
    line_items: [{
      price_data: {
        currency: 'usd',
        product_data: { name: product.name },
        unit_amount: product.price, // en centavos — $10.00 = 1000
      },
      quantity: 1,
    }],
    mode: 'payment',
    success_url: `${env.APP_URL}/success?session_id={CHECKOUT_SESSION_ID}`,
    cancel_url: `${env.APP_URL}/shop`,
    metadata: { userId, productId },
  })

  return Response.json({ url: session.url })
}
```

-----

## Paso 6: Configurar Webhooks (el paso más importante)

Mucha gente se salta esto y luego se pregunta por qué su app no responde después de un pago exitoso — es porque el backend nunca se enteró de que el pago ocurrió.

**Webhooks** son notificaciones que Stripe envía a tu servidor cuando ocurren eventos (pago completado, reembolso emitido, suscripción actualizada).

### Añadir un endpoint de Webhook en Stripe Dashboard

Stripe Dashboard → Developers → Webhooks → Add endpoint

Introduce tu URL de webhook: `https://tu-app.com/api/webhook/stripe`

Selecciona los eventos a escuchar — como mínimo:

- `checkout.session.completed` (pago completado)
- `payment_intent.payment_failed` (pago rechazado)

### Implementar el handler del Webhook

```typescript
export async function handleStripeWebhook(request: Request, env: Env) {
  const signature = request.headers.get('stripe-signature')
  const body = await request.text()

  const stripe = new Stripe(env.STRIPE_SECRET_KEY)

  let event
  try {
    // Verificar que esta petición realmente viene de Stripe, no una petición falsa
    event = stripe.webhooks.constructEvent(
      body,
      signature,
      env.STRIPE_WEBHOOK_SECRET
    )
  } catch (err) {
    return new Response('Webhook signature verification failed', { status: 400 })
  }

  if (event.type === 'checkout.session.completed') {
    const session = event.data.object
    const { userId, productId } = session.metadata

    // Registrar el pedido en la base de datos
    await env.DB.prepare(`
      INSERT INTO orders (user_id, product_id, amount, status, stripe_session_id)
      VALUES (?, ?, ?, 'paid', ?)
    `).bind(userId, productId, session.amount_total, session.id).run()

    // Enviar email de confirmación vía Resend
    // ...
  }

  return new Response('OK', { status: 200 })
}
```

Después de configurar el webhook en Stripe Dashboard, recibirás un **Webhook Secret**. Añádelo a tus variables de entorno:

```
STRIPE_WEBHOOK_SECRET=whsec_xxxxx
```

-----

## Paso 7: Probar el flujo completo en modo Sandbox

El modo de prueba (sandbox) de Stripe te permite probar el flujo de pago completo sin mover dinero real.

**Enfoque recomendado: despliega a producción en Cloudflare y prueba con claves de sandbox.**

La razón es directa: los Webhooks de Stripe necesitan una URL HTTPS accesible públicamente. Cloudflare Workers te da una en el momento que despliegas — mucho más simple que configurar un túnel local.

### Flujo de prueba

1. **Confirma que tus variables de entorno usan claves de prueba**
   
   En Cloudflare Dashboard → Workers → tu Worker de API → Settings → Variables, verifica:
   
   ```
   STRIPE_SECRET_KEY = sk_test_xxxxx
   STRIPE_WEBHOOK_SECRET = whsec_xxxxx (webhook secret del modo de prueba)
   ```
1. **Añade un webhook de prueba en Stripe Dashboard**
   
   Stripe Dashboard → Developers → Webhooks → Add endpoint
   
   Introduce tu URL de producción: `https://tu-app.workers.dev/api/webhook/stripe`
   
   Copia el Webhook Secret que Stripe te da y actualízalo en Cloudflare.
1. **Recorre todo el flujo de pago con una tarjeta de prueba**
   
   Abre tu URL de producción, haz clic en "Comprar," y en la página de pago de Stripe introduce:
   
   ```
   Número de tarjeta: 4242 4242 4242 4242
   Vencimiento: cualquier fecha futura (ej. 12/30)
   CVV: cualquier tres dígitos (ej. 123)
   ```
1. **Verifica que cada paso se completó correctamente**
- Stripe Dashboard → Payments: la transacción de prueba aparece
- Stripe Dashboard → Webhooks: webhook entregado exitosamente (respuesta 200)
- Base de datos de tu app: el registro de pedido fue creado
- El usuario recibió un email de confirmación

Solo pasa a modo en vivo después de que cada paso esté correcto.

### Prueba también el flujo de fallo

```
Número de tarjeta: 4000 0000 0000 0002
→ Siempre rechazada — verifica que tu app maneja correctamente los fallos de pago
```

-----

## Paso 8: Cambiar a modo en vivo

Una vez que las pruebas de sandbox pasen, cambia a modo en vivo:

1. Cambia a **Live Mode** en la esquina superior derecha de Stripe Dashboard
1. Obtén tu clave API en vivo (`sk_live_...`)
1. Añade un nuevo endpoint de webhook bajo Live Mode (misma URL, pero registrada bajo Live Mode)
1. Obtén el Webhook Secret en vivo (`whsec_...`)
1. Actualiza las variables de entorno de tu Cloudflare Workers:
   
   ```bash
   npx wrangler secret put STRIPE_SECRET_KEY
   # Introduce sk_live_xxxxx
   
   npx wrangler secret put STRIPE_WEBHOOK_SECRET
   # Introduce el whsec_xxxxx en vivo
   ```

Después de cambiar, haz un pago real con un monto pequeño (ej. $1) para confirmar que todo funciona de extremo a extremo.

-----

## Paso 9: Usando VibeFast — Sáltate todo esto

Si estás usando VibeFast, la buena noticia es: **todo el flujo de Stripe ya está construido.**

VibeFast incluye:

- Lógica de creación de Checkout Session
- Handler de webhook (con verificación de firma y manejo de idempotencia)
- Creación automática de registro de pedido en pago exitoso
- Email de confirmación de pago vía Resend

No necesitas implementar nada del código anterior. Solo necesitas hacer una cosa:

**Configura tus variables de entorno en Cloudflare Dashboard**

Ve a Cloudflare Dashboard → tu Worker de API → Settings → Variables:

```
STRIPE_SECRET_KEY = sk_test_xxxxx (prueba) o sk_live_xxxxx (en vivo)
STRIPE_WEBHOOK_SECRET = whsec_xxxxx
STRIPE_CURRENCY = usd (o eur, mxn, etc.)
STRIPE_PRICE_CENTS = 9900 (en centavos — 9900 = $99.00)
```

No es necesario crear un producto en Stripe Dashboard primero. VibeFast genera el precio dinámicamente al crear la Checkout Session.

Configura las variables, despliega, y los pagos están en vivo.

Recorre el flujo de sandbox (Paso 7) para confirmar que todo funciona, luego intercambia por tus claves en vivo.

-----

## Errores comunes

**La verificación de firma del webhook falla**
La causa más común es leer el body como JSON parseado antes de pasarlo al handler del webhook. El handler debe leer el **texto crudo del body** — si llamas a `request.json()` primero, la verificación de firma fallará.

**Equivocarse con la unidad del monto**
Stripe usa centavos, no dólares. $10.00 debe pasarse como `1000`, no `10`.

**No manejar reintentos de webhook**
Si Stripe no recibe una respuesta 200, reintenta durante hasta 3 días. Haz tu handler de webhook idempotente — procesar el mismo evento dos veces no debería tener efectos secundarios. Usa `stripe_session_id` como clave única en tu base de datos para prevenir pedidos duplicados.

-----

## Resumen

El flujo completo de pagos con Stripe:

1. Crea una cuenta y completa la verificación de identidad
1. Desarrolla y prueba usando modo sandbox (claves de prueba)
1. Usa Checkout Session — deja que Stripe maneje la página de pago
1. Configura webhooks para que tu backend sepa cuándo se completan los pagos
1. Despliega en Cloudflare, recorre todo el flujo con tarjetas de prueba
1. Confirma que cada paso funciona, luego cambia a claves en vivo

Con VibeFast: configura cuatro variables de entorno y los pagos están en vivo inmediatamente.

El paso que más se olvida es el paso 4 — sin webhooks, tu app nunca se entera de quién pagó.

-----

**Danko Peng**  
[@dankopeng](https://x.com/dankopeng) · [vibefast.app](https://vibefast.app)

-----

👉 [Volver al índice del curso](../README-es.md)

¿Listo para empezar con una plantilla full-stack completa de Cloudflare?  
👉 **[vibefast.app](https://vibefast.app)** — Precio early bird $99, sube a $199 el 1 de junio de 2026.
