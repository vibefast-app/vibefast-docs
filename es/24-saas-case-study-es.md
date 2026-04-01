# Caso de estudio: Construyendo un producto SaaS con VibeFast

[English](../en/24-saas-case-study-en.md) · [繁中](../zh/24-saas-case-study-zh.md) · [Español](../es/24-saas-case-study-es.md) · [日本語](../jp/24-saas-case-study-jp.md) · [Português (BR)](../pt-br/24-saas-case-study-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~10 minutos

-----

## El punto de partida: Compraste VibeFast y tienes una idea de SaaS

Este tutorial no trata sobre las funcionalidades de VibeFast — la documentación de inicio rápido cubre eso.

Se trata de mostrar un flujo de trabajo real: **¿cómo se ve realmente ir de una idea de SaaS a un producto funcionando usando VibeFast?**

El ejemplo: un SaaS que ayuda a emprendedores en solitario a rastrear facturas de clientes — **InvoiceTrack**. Conjunto de funcionalidades simple: crear facturas, rastrear estado de pago, enviar recordatorios automáticos antes de las fechas de vencimiento.

-----

## Paso 1: Clonar y ponerlo en marcha primero

Después de comprar VibeFast, recibirás una invitación de colaborador al repo privado. Acéptala, luego clona:

```bash
git clone https://github.com/vibefast-app/vibefast.git invoicetrack
cd invoicetrack
npm install
```

Sigue la documentación de inicio rápido para ejecutar el setup. En unos minutos tienes una app completamente funcional — login, flujo de pagos, panel de admin — todo corriendo con el contenido por defecto de VibeFast.

**Construir sobre esto es diez veces más rápido que empezar desde cero.**

-----

## Paso 2: Escribir un documento de requisitos

Antes de tocar cualquier código, haz que la IA te ayude a organizar tus requisitos (el tutorial de planificación de trabajo cubre este proceso en detalle):

```
Quiero construir un SaaS de gestión de facturas para emprendedores en solitario usando VibeFast.
El producto se llama InvoiceTrack.

Funcionalidades principales (MVP):
1. Los usuarios pueden crear registros de clientes (nombre de empresa, contacto, email)
2. Los usuarios pueden crear facturas para clientes (líneas de detalle, montos, fechas de vencimiento)
3. Rastrear estado de facturas (borrador, enviada, pagada, vencida)
4. Enviar automáticamente emails de recordatorio 3 días antes de la fecha de vencimiento
5. Dashboard mostrando el saldo total pendiente

Base técnica: VibeFast (Auth, Stripe y Resend ya integrados)
No en esta versión: Exportación PDF, multi-moneda, sub-cuentas
```

-----

## Paso 3: Diseñar la estructura de base de datos

Con los requisitos claros, haz que la IA diseñe las tablas:

```
No escribas código todavía.

Basándote en estos requisitos, diseña el esquema de base de datos D1 para:
- clients
- invoices
- invoice_items

¿Qué columnas necesita cada tabla? ¿Cuáles son las relaciones entre ellas?
```

La IA te da un esquema. Revísalo línea por línea — pregunta sobre cualquier cosa que no entiendas, objeta lo que no te guste, y una vez satisfecho, pide a la IA que genere el SQL para crear las tablas.

-----

## Paso 4: Construir una funcionalidad a la vez

Con la estructura de datos confirmada, trabaja por capas:

**Capa 1 — Base**

```
□ Crear tablas clients, invoices e invoice_items
□ Confirmar que el auth de VibeFast protege todas las nuevas rutas
```

**Capa 2 — Funcionalidades principales**

```
□ Gestión de clientes (crear, editar, listar)
□ Creación de facturas (seleccionar cliente, añadir líneas de detalle, establecer fecha de vencimiento)
□ Actualizaciones de estado de facturas
□ Dashboard mostrando saldo total pendiente
□ Emails de recordatorio de fecha de vencimiento (vía Cron Trigger)
```

**Capa 3 — Pulido**

```
□ Mejoras de diseño de página de facturas
□ Refinamiento de plantillas de email
□ Funcionalidad de búsqueda y filtrado
```

Cada funcionalidad sigue el mismo ciclo: discutir primero → confirmar en modo Plan → implementar → probar en local → commit.

-----

## Paso 5: Rebranding

La UI por defecto de VibeFast es tu punto de partida, no tu producto final. Intercambia la marca de InvoiceTrack:

Dile a la IA:

```
Reemplaza el branding por defecto de VibeFast con InvoiceTrack.
Esto incluye:
- Título del sitio y meta title
- Texto del logo (texto por ahora, imagen después)
- Cambiar color primario de naranja a azul (blue-600)
- Texto Hero en la homepage
```

El rebranding típicamente toma menos de una hora.

-----

## Paso 6: Configurar facturación con Stripe

Precio de InvoiceTrack: $9/mes, hasta 50 facturas.

VibeFast ya tiene Stripe integrado. Solo necesitas:

1. Crear un producto de suscripción de $9/mes en Stripe Dashboard
1. Actualizar el Stripe Price ID en tus variables de entorno
1. Decirle a la IA: "Modifica la lógica de suscripción para que los usuarios gratuitos solo puedan crear 3 facturas, luego pide que hagan upgrade"

El flujo de pago en sí no necesita reescribirse — la integración de Stripe de VibeFast funciona directamente.

-----

## De idea a primer usuario que paga: La línea de tiempo

¿Cuánto tiempo tomó realmente — de clonar a un producto en vivo con un usuario que paga?

```
Día 1:     Clonar, setup, explorar la estructura del proyecto
Días 2–3:  Diseño de base de datos, base de Capa 1
Días 4–7:  Funcionalidades principales de Capa 2
Días 8–9:  Rebranding, configuración de Stripe
Día 10:    Pruebas, corrección de bugs, despliegue
Día 11:    Lanzamiento, empezar a promocionar
```

Once días de cero a en vivo. No por alguna habilidad especial — porque VibeFast ya tenía auth, pagos y despliegue resueltos. Todo el tiempo fue a las funcionalidades reales del producto de InvoiceTrack.

-----

**Danko Peng**  
[@dankopeng](https://x.com/dankopeng) · [vibefast.app](https://vibefast.app)

-----

👉 [Volver al índice del curso](../README-es.md)

¿Listo para empezar con una plantilla full-stack completa de Cloudflare?  
👉 **[vibefast.app](https://vibefast.app)** — Precio early bird $99, sube a $199 el 1 de junio de 2026.
