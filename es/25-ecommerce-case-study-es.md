# Caso de estudio: Construyendo un e-commerce con VibeFast

[English](../en/25-ecommerce-case-study-en.md) · [繁中](../zh/25-ecommerce-case-study-zh.md) · [Español](../es/25-ecommerce-case-study-es.md) · [日本語](../jp/25-ecommerce-case-study-jp.md) · [Português (BR)](../pt-br/25-ecommerce-case-study-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~10 minutos

-----

## El punto de partida: Compraste VibeFast y quieres construir una tienda

Este tutorial usa un escenario concreto para mostrar cómo se ve realmente: **ir de una idea de e-commerce a un producto funcionando con VibeFast.**

El ejemplo: un e-commerce de jabón artesanal — **SoapCo**. Haciendo jabón y vendiéndolo directamente. Sin necesidad de un sistema de inventario complejo. Un catálogo pequeño — alrededor de 20–30 SKUs en cualquier momento.

-----

## En qué se diferencia del caso de estudio SaaS

El tutorial anterior (23) era un SaaS — los usuarios pagan una cuota mensual para usar tu herramienta.

El e-commerce es diferente: **los usuarios compran productos físicos, cada transacción es de una sola vez, y necesitas gestionar productos, pedidos y envíos.**

Esto cambia la estructura de datos y la lógica de negocio. Pero la base de VibeFast — auth, Stripe, Resend — funciona de la misma manera en ambos casos.

-----

## Paso 1: Clonar y ponerlo en marcha

Igual que el caso SaaS — clona el repo, ejecuta el setup siguiendo la documentación de inicio rápido, y confirma que toda la app funciona en local.

No necesitas cambiar nada en este paso. Solo estás confirmando que el punto de partida es sólido.

-----

## Paso 2: Escribir un documento de requisitos

```
Quiero construir un e-commerce de jabón artesanal usando VibeFast.
La tienda se llama SoapCo. Yo hago el jabón y lo vendo yo mismo.
Catálogo pequeño — alrededor de 20–30 SKUs.

Funcionalidades principales (MVP):
1. Páginas de lista y detalle de productos (imágenes, descripciones de ingredientes, estado de stock)
2. Carrito de compras (añadir múltiples productos, ajustar cantidades)
3. Checkout y pago (Stripe, pago único)
4. Email automático de confirmación de pedido
5. Panel de admin para gestionar productos y pedidos

Base técnica: VibeFast (Auth, Stripe y Resend ya integrados)
No en esta versión: puntos de fidelidad, reseñas de productos, multi-idioma, códigos de descuento
```

-----

## Paso 3: Diseñar la estructura de base de datos

El esquema de e-commerce es ligeramente más complejo que el de SaaS. Haz que la IA lo diseñe:

```
No escribas código todavía.

Basándote en estos requisitos de e-commerce, diseña el esquema de base de datos D1 para:
- products
- orders
- order_items

¿Qué columnas necesita cada tabla? ¿Cuáles son las relaciones entre ellas?
Las imágenes de productos se almacenarán en R2 — la tabla solo necesita guardar la URL de la imagen.
```

La IA te da un borrador. Revísalo:

- ¿`products` tiene `name`, `slug`, `description`, `price`, `stock`, `image_url`? ¿Se ve correcto?
- ¿Qué necesita registrar `orders`? ¿Debería guardar una dirección de envío?
- ¿Qué estados de pedido tienen sentido? ¿`pending`, `paid`, `shipped`, `delivered`?

Una vez confirmado, pide a la IA que genere el SQL y ejecútalo.

-----

## Paso 4: Imágenes de productos

El e-commerce necesita imágenes de productos — la mayor diferencia con construir un SaaS.

La buena noticia: **VibeFast incluye una biblioteca de medios completa** — subida, gestión y visualización de imágenes ya están incorporadas. No necesitas diseñar este flujo tú mismo.

Al crear o editar un producto en el panel de admin, simplemente usa la biblioteca de medios de VibeFast para seleccionar o subir la imagen. Almacenarla en R2, generar una URL, mostrarla en el frontend — todo está resuelto.

Todo lo que necesitas hacer es añadir una columna `image_url` a la tabla `products` y conectarla al componente existente de biblioteca de medios:

```
Añade una columna image_url a la tabla products.
En las páginas de crear y editar producto del panel de admin,
conecta el componente existente de biblioteca de medios de VibeFast para que el usuario pueda elegir una imagen.
Guarda la URL de la imagen seleccionada en esta columna.
```

Eso es todo. No necesitas construir la subida de imágenes desde cero.

-----

## Paso 5: Decisión de diseño del carrito

El carrito de compras es el núcleo de cualquier e-commerce. Dos enfoques:

**Opción A: Almacenar en el navegador (localStorage/Cookie)**

- Los usuarios pueden añadir al carrito sin iniciar sesión
- Simple — no necesita base de datos
- Desventaja: el carrito se pierde si cambian de dispositivo

**Opción B: Almacenar en la base de datos**

- Requiere login para añadir al carrito
- Se sincroniza entre dispositivos
- Más complejo de implementar

Para una tienda pequeña como SoapCo, la Opción A es suficiente. Dile a la IA:

```
Almacena el carrito en una cookie. No se requiere login.
Formato: un array JSON — [{productId, quantity}, ...]
El login o los datos de envío solo se requieren en el checkout.

No escribas código todavía — ¿hay algún problema potencial con este diseño?
```

Discute primero, confirma la dirección, luego implementa.

-----

## Paso 6: Stripe Checkout

VibeFast ya tiene Stripe integrado. El checkout de e-commerce es ligeramente diferente de una suscripción SaaS — este es un pago único y el carrito puede tener múltiples productos.

Dile a la IA:

```
Implementa el flujo de checkout:

1. El usuario hace clic en "Checkout" — el contenido del carrito se envía al backend
2. El backend valida que cada producto tenga stock suficiente
3. Crea una Stripe Checkout Session con todos los artículos del carrito
4. En pago exitoso:
   - Crear registros en orders y order_items
   - Deducir stock de cada producto
   - Enviar email de confirmación de pedido vía Resend
5. Manejar resultado del pago vía webhook

Lista el plan y qué archivos serán afectados antes de escribir código.
```

-----

## Paso 7: Panel de admin

El e-commerce necesita un área de admin simple para gestionar productos y pedidos.

VibeFast ya tiene la estructura del panel de admin en su lugar. Lo que necesitas añadir:

- Gestión de productos (crear, editar, publicar/despublicar, establecer niveles de stock)
- Lista de pedidos (ver todos los pedidos y su estado)
- Actualizar estado de pedido (enviado, entregado)

Estas son todas operaciones CRUD estándar. Pide a la IA que las implemente una por una.

-----

## Paso 8: Branding y diseño

Reemplaza la UI por defecto de VibeFast con la identidad visual de SoapCo.

Los elementos visuales importan mucho en e-commerce — especialmente las fotos de productos y la calidad general de la presentación. Vale la pena dedicar tiempo extra aquí:

```
Rediseña el sitio para que coincida con una marca de jabón artesanal natural:
- Colores primarios: blanco cálido (stone-50) combinado con verde oscuro (green-800)
- Sensación tipográfica: elegante, natural
- Tarjetas de producto: imagen al frente, texto mínimo
- Tono general: artesanal, natural, calidad — evitar sobrediseño
```

-----

## De idea a lanzamiento: La línea de tiempo

```
Día 1:       Clonar, setup, explorar la estructura del proyecto
Día 2:       Diseño de esquema de BD, campo de URL de imagen
Días 3–5:    Páginas de productos (lista + detalle)
Días 6–7:    Lógica del carrito de compras
Días 8–9:    Stripe checkout + webhook + email de pedido
Día 10:      Panel de admin (productos + pedidos)
Días 11–12:  Branding, pulido visual, ajustes de detalle
Día 13:      Probar cada flujo de extremo a extremo, arreglar bugs
Día 14:      Desplegar, preparar lanzamiento
```

Dos semanas de cero a en vivo. Si tuvieras que construir auth y Stripe desde cero, esas dos solas tomarían más de una semana. VibeFast se salta todo eso — las dos semanas completas van a la tienda en sí.

-----

## Detalles fáciles de olvidar

**Deduce stock después de que el pago sea confirmado — no cuando el artículo se añade al carrito.**
Añadir al carrito es solo intención. Si el pago falla o el usuario abandona el checkout, el stock nunca debería haberse deducido. Solo deduce dentro del handler `checkout.session.completed` del webhook de Stripe.

**El email de confirmación de pedido necesita suficiente información.**
Después de pagar, los usuarios necesitan ver: número de pedido, lista detallada, monto total, ventana estimada de entrega, e información de contacto. Este email es la señal de confianza principal después de una compra.

**Prueba el flujo completo de compra — no solo la UI.**
Usa la tarjeta de prueba de Stripe para recorrer el flujo completo: añadir al carrito → checkout → pago → confirmar que llega el email → verificar que el pedido aparece en admin. Cada paso necesita verificarse.

-----

**Danko Peng**  
[@dankopeng](https://x.com/dankopeng) · [vibefast.app](https://vibefast.app)

-----

👉 [Volver al índice del curso](../README-es.md)

¿Listo para empezar con una plantilla full-stack completa de Cloudflare?  
👉 **[vibefast.app](https://vibefast.app)** — Precio early bird $99, sube a $199 el 1 de junio de 2026.
