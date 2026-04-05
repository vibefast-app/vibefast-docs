# Cómo crear un plan de trabajo para Vibe Coding: Del análisis de requisitos al modo Plan de la IA

[English](../en/17-vibe-coding-work-plan-and-ai-plan-mode-en.md) · [繁中](../zh/17-vibe-coding-work-plan-and-ai-plan-mode-zh.md) · [Español](../es/17-vibe-coding-work-plan-and-ai-plan-mode-es.md) · [日本語](../jp/17-vibe-coding-work-plan-and-ai-plan-mode-jp.md) · [Português (BR)](../pt-br/17-vibe-coding-work-plan-and-ai-plan-mode-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~10 minutos

-----

## Cómo empieza el Vibe Coding de la mayoría

Tienes una idea, abres Cursor, dices "hazme una app XX."

La IA empieza a generar, tú empiezas a modificar, modificando descubres que la dirección es incorrecta, lo tiras y empiezas de nuevo. Generas de nuevo, modificas de nuevo, mal de nuevo.

Tres días después, tienes un montón de funcionalidades a medio hacer, ninguna completa, y tampoco sabes qué hacer a continuación.

**El problema no es la IA, es que no tienes un plan.**

-----

## ¿Por qué los Vibe Coders necesitan especialmente planes?

Los ingenieros tradicionales tienen años de formación, saben cómo descomponer problemas, estimar carga de trabajo, priorizar.

La ventaja de los Vibe Coders es la velocidad — pero la velocidad también es una trampa. La IA te permite "hacer cualquier cosa rápido," así que fácilmente corres muy rápido en la dirección equivocada.

El propósito del plan no es frenarte, sino asegurar que tu velocidad se use en el lugar correcto.

-----

## Paso 1: Escribe un documento de requisitos

Antes de abrir Cursor, primero dedica 30 minutos a escribir un documento de requisitos.

El documento de requisitos es el primer documento de todo el proyecto de Vibe Coding — existe antes de que escribas cualquier línea de código. Este documento es la base de consenso entre tú y la IA, cada vez que empieces una nueva tarea después, puedes pegárselo a la IA, ella conoce todo el contexto del proyecto, sin necesidad de re-explicar cada vez.

### ¿No sabes cómo escribirlo? Deja que la IA haga un borrador

Si es la primera vez que haces un producto, quizás no sepas cómo convertir las ideas vagas en tu cabeza en un documento completo. Esto es completamente normal — simplemente deja que la IA te ayude a hacer el borrador.

Digamos que compraste la plantilla VibeFast, quieres hacer un e-commerce de jabón artesanal. Tienes una idea vaga en la cabeza, pero no sabes cómo organizarla en un documento.

Abre Claude o ChatGPT, expón directamente tu idea:

```
Quiero usar la plantilla VibeFast (Remix + Cloudflare fullstack)
para hacer un e-commerce de jabón artesanal.
Hago jabón yo mismo, quiero vender en línea, los clientes pueden pedir, pagar, recibir notificaciones de envío.
El inventario no es grande, cada vez unos 20–30 artículos.

Por favor ayúdame a escribir un documento de requisitos, incluyendo:
1. Quiénes son los usuarios objetivo, cuáles son sus problemas
2. Funcionalidades principales del MVP (máximo cinco)
3. Stack tecnológico (basado en la arquitectura existente de VibeFast)
4. Qué no hará esta versión
5. Lista de tareas de desarrollo, dividida en capas de base/core/pulido

No escribas código todavía, solo escribe el documento.
```

La IA podría generar un borrador como este:

```
# Documento de requisitos del e-commerce de jabón artesanal

## Usuarios objetivo
- Quiénes son: Consumidores que gustan del jabón artesanal natural, 25–45 años, se preocupan por ingredientes y calidad
- Sus problemas: No encuentran canales de compra de jabón artesanal con calidad garantizada e ingredientes transparentes
- Qué quieren: Navegación simple, pedidos seguros, saber el estado de su pedido

## Funcionalidades principales del MVP
1. Lista de productos y páginas de detalle
2. Carrito de compras y checkout (pago con Stripe)
3. Gestión de pedidos (clientes pueden ver el estado del pedido)
4. Enviar automáticamente email de notificación después del envío
5. Panel de administración para gestionar productos y pedidos

## Stack tecnológico (basado en VibeFast)
- Frontend/Backend: Arquitectura existente de VibeFast (Remix + Cloudflare Workers)
- Base de datos: D1 (productos, pedidos, usuarios)
- Almacenamiento de imágenes: R2 (imágenes de productos)
- Pagos: Stripe (ya integrado en VibeFast)
- Email: Resend (ya integrado en VibeFast)

## Esta versión no hará
- Sistema de puntos de membresía
- Funcionalidad de reseñas de productos
- Múltiples idiomas
- App móvil

## Lista de tareas

### Capa 1 — Base
- [ ] Crear tabla products
- [ ] Crear tablas orders y order_items
- [ ] Configurar R2 para imágenes de productos

### Capa 2 — Funcionalidades principales
- [ ] Páginas de lista y detalle de productos
- [ ] Lógica del carrito de compras
- [ ] Flujo de checkout con Stripe
- [ ] Email de confirmación de pedido
- [ ] Gestión de productos y pedidos en admin

### Capa 3 — Pulido
- [ ] Carga diferida de imágenes de productos
- [ ] Optimización de página de estado de pedidos
- [ ] Meta tags SEO
```

Después de obtener el borrador, **léelo línea por línea, no te apresures a aceptar**:

- No entiendes algo, pregunta: "¿Qué es la tabla order_items? ¿Por qué separada de orders?"
- No te gusta algo, di: "No necesito sistema de membresía, los clientes compran sin registrarse, cambia a checkout como invitado"
- Falta algo, añade: "Quiero añadir funcionalidad de gestión de inventario, cada producto debería tener límite de cantidad"
- No estás seguro de algo, discute: "¿Las notificaciones de envío deberían ser por email o SMS? ¿Cuáles son las ventajas e inconvenientes de cada uno?"

Varias rondas de ida y vuelta, hasta que mires este documento y sientas "sí, esto es lo que quiero construir" — solo entonces está completo.

**Este documento es tuyo, no de la IA.** La IA te ayuda a redactar, pero cada decisión necesita tu comprensión y aprobación. No incluyas lo que no entiendas, cambia lo que no te convenza.

No necesita ser tan completo como un documento de producto formal, pero debería cubrir estas preguntas:

### 1. ¿Para quién es este producto?

```
Usuarios objetivo: Consumidores que gustan del jabón artesanal natural, se preocupan por ingredientes y calidad
Sus problemas: No encuentran canales de compra de jabón artesanal con calidad garantizada e ingredientes transparentes
Qué quieren: Navegación simple, pedidos seguros, saber claramente el estado del pedido
```

Aclarar quiénes son los usuarios, cada decisión de funcionalidad posterior se juzga por "¿es esto útil para mis usuarios objetivo?"

### 2. ¿Cuáles son las funcionalidades principales?

Lista las funcionalidades que tu app **debe tener**, máximo cinco:

```
Funcionalidades principales (MVP):
1. Páginas de lista y detalle de productos
2. Carrito de compras y checkout (pago con Stripe)
3. Gestión de pedidos (clientes pueden ver el estado)
4. Enviar automáticamente email de notificación después del envío
5. Panel de administración para gestionar productos y pedidos
```

Esta lista es tu MVP. Antes del lanzamiento, estas cinco terminadas es suficiente. Otras funcionalidades que se te ocurran, ponlas primero en la lista de "ya lo haré después."

### 3. Stack tecnológico

VibeFast ya decidió la mayoría del stack tecnológico por ti, solo necesitas confirmar qué servicios usar:

```
Frontend/Backend: Arquitectura existente de VibeFast (Remix + Cloudflare Workers)
Base de datos: D1 (productos, pedidos)
Almacenamiento de imágenes: R2 (imágenes de productos)
Pagos: Stripe (ya integrado en VibeFast)
Email: Resend (ya integrado en VibeFast)
```

Una vez decidido el stack tecnológico, no cambies a mitad de camino. Cambiar de tecnología cuesta mucho, y normalmente no es por la tecnología en sí, sino porque aún no estás familiarizado.

### 4. Qué no hacer

Esta columna es tan importante como "qué hacer":

```
Esta versión no hará:
- Sistema de puntos de membresía
- Funcionalidad de reseñas de productos
- Múltiples idiomas
- App móvil
```

Declarar claramente qué no hacer evita que estés constantemente añadiendo funcionalidades durante el desarrollo (scope creep), haciendo que el MVP nunca se termine.

-----

## Paso 2: Descomponer requisitos en tareas específicas

Después de escribir el documento de requisitos, descompón cada funcionalidad en tareas de desarrollo específicas.

**Tareas no suficientemente específicas:**

```
- Hacer páginas de productos
- Hacer checkout
```

**Tareas suficientemente específicas:**

```
- Crear tabla products (name, description, price, stock, image_url)
- Crear tablas orders y order_items
- Implementar endpoint GET /api/products (lista de productos)
- Implementar endpoint GET /api/products/:slug (detalle de producto)
- Implementar endpoint POST /api/checkout (crear sesión de Stripe)
- Crear UI de página de lista de productos (/shop)
- Crear UI de página de detalle de producto (/shop/:slug)
```

Cuanto más específicas sean las tareas, más fácil es estimar "cuánto tardará esto," y también más fácil decirle a la IA qué hacer.

-----

## Paso 3: Priorizar

Divide las tareas en tres capas:

**Capa 1 — Base (hacer primero)**  
Sin esto, las otras funcionalidades no pueden funcionar:

- Esquema de base de datos
- Sistema de autenticación
- Estructura básica del proyecto

**Capa 2 — Funcionalidades principales (hacer después)**  
Las funcionalidades principales de tu MVP:

- Flujo de pago
- Páginas principales de usuario
- Panel de administración

**Capa 3 — Pulido (hacer al final)**  
Mejora el producto, pero no es esencial:

- Animaciones y micro-interacciones
- Optimización SEO
- Diseño de páginas de error

Siempre empieza por la Capa 1. Mucha gente empieza por la Capa 3 (ajustar colores de botones, añadir animaciones), resultado: la base aún no está construida.

-----

## Paso 4: Usa el modo Plan de la IA

Ahora tienes documento de requisitos y lista de tareas, puedes empezar a usar la IA.

Pero no le digas directamente a la IA que empiece — primero usa el **modo Plan**.

### ¿Qué es el modo Plan?

Cursor tiene una funcionalidad llamada **Plan** (selecciona "Plan" en el modo Chat). En el modo Plan, describes lo que quieres hacer, la IA:

1. Analiza tus requisitos
2. Lista lo que planea hacer, qué archivos modificar
3. **Espera tu confirmación antes de ejecutar**

La diferencia de decirle directamente a la IA que empiece es: puedes ver el "plan" de la IA antes de que haga nada, confirmar si la dirección es correcta.

### Cómo usar el modo Plan

En el panel de Chat de Cursor, cambia al modo **Plan**, escribe:

```
Quiero implementar las páginas de lista y detalle de productos del e-commerce de jabón artesanal.

Contexto:
- Esta es una app de e-commerce basada en VibeFast (Remix + Cloudflare Workers + D1)
- D1 ya tiene la tabla products, campos: id, name, slug, description, price, stock, image_url
- Las imágenes de productos están en R2

Qué hacer:
1. GET /api/products — devolver todos los productos en stock (stock > 0)
2. GET /api/products/:slug — devolver detalles de un producto
3. Crear página de lista de productos /shop, mostrar imágenes, nombres, precios
4. Crear página de detalle de producto /shop/:slug, mostrar toda la información y botón de añadir al carrito

Por favor primero lista tu plan y qué archivos vas a modificar, no empieces todavía.
```

La IA responderá con un plan como este:

```
Mi plan:

1. Añadir apps/api/src/routes/products.ts
   - GET /products: consultar todos los productos con stock > 0
   - GET /products/:slug: consultar un producto individual

2. Añadir apps/web/app/routes/shop._index.tsx
   - loader llama a la API de products
   - Mostrar grid de productos, cada card tiene imagen, nombre, precio

3. Añadir apps/web/app/routes/shop.$slug.tsx
   - loader llama a la API products/:slug
   - Mostrar información completa del producto
   - Botón de añadir al carrito (UI primero, lógica del carrito en el siguiente paso)

4. Añadir apps/web/app/components/ProductCard.tsx
   - Componente de tarjeta de producto reutilizable

¿Confirmas que este plan está bien?
```

Después de revisar y confirmar que no hay problemas, entonces di "vale, empieza a implementar."

Pero "confirmar que no hay problemas" no es echar un vistazo casual y asentir — este paso como el documento de requisitos, necesita atención seria:

**No entiendes, pregunta claramente:**

```
Dijiste que añadirías un componente ProductCard,
¿cuál es la relación entre este componente y shop._index.tsx?
¿Por qué separarlo, no se puede escribir directamente en la página?
```

**Tienes dudas, exprésalas:**

```
Las imágenes de productos están en R2, ¿cómo obtiene tu plan las URLs de imágenes?
Las imágenes de R2 son privadas, ¿necesitan proxy a través de Workers?
```

**No te gusta, pide modificación:**

```
No quiero hacer un componente ProductCard reutilizable, en esta versión escríbelo directamente en la página,
lo extraeré después cuando sea necesario. Por favor ajusta el plan.
```

**Confirma el alcance del impacto:**

```
¿Cuántos archivos añadirá este plan? ¿Modificará archivos existentes?
¿Podría afectar funcionalidades existentes?
```

Después de aclarar todas estas preguntas y modificar a tu satisfacción, entonces di "vale, empieza a implementar."

Un plan que aún no ha empezado, modificarlo cuesta cero. Una funcionalidad ya medio implementada cuando descubres que la dirección era incorrecta, modificarla puede costar potencialmente mucho.

### La mentalidad central del modo Plan: Los planes son para revisar, no para aprobar

Mucha gente ve que la IA lista un plan, por costumbre dice "bien, empecemos" — porque el plan se ve razonable, y tienes ganas de ver progreso.

Pero los planes se listan para que los **revises**, no para que les **pongas el sello de aprobación**.

Varias veces le dije a la IA que lo hiciera directamente, modificó archivos que no esperaba, o usó una arquitectura que no me gustaba, lo descubrí después de terminar. El modo Plan te permite descubrir estos problemas antes de que la IA empiece — pero la premisa es que realmente leas con cuidado, realmente hagas preguntas.

Trata la revisión de planes como una sesión de trabajo independiente, no te apresures. Esos diez minutos valen la pena.

-----

## Un ritmo de trabajo completo

Juntando todo lo anterior, tu ritmo de trabajo es más o menos así:

**Al empezar un proyecto nuevo (una vez)**

1. Escribir documento de requisitos (30 minutos)
2. Descomponer en tareas específicas (30 minutos)
3. Priorizar Capa 1/2/3 (10 minutos)

**Al empezar a trabajar cada día**

1. Mira la lista de tareas, elige 1–3 tareas para hoy
2. Para partes inciertas, primero discute con "no escribas código todavía" (referencia al artículo anterior)
3. Después de confirmar la dirección, usa el modo Plan para que la IA liste el plan
4. Confirma el plan, empieza a implementar
5. Prueba en local, si no hay problemas entonces commit + push

**Revisión semanal**

1. ¿Qué tareas se completaron?
2. ¿Cuáles se atascaron? ¿Por qué?
3. ¿Hay lugares que necesiten reajuste de prioridad?

-----

## Plantilla de documento de requisitos

Puedes copiar directamente esta plantilla para empezar:

```markdown
# Documento de requisitos de [Nombre del proyecto]

## Usuarios objetivo
- Quiénes son:
- Sus problemas:
- Qué quieren:

## Funcionalidades principales (MVP, máximo cinco)
1. 
2. 
3. 
4. 
5. 

## Stack tecnológico
- Frontend:
- Backend:
- Base de datos:
- Pagos:
- Email:

## Esta versión no hará
- 
- 
- 

## Lista de tareas

### Capa 1 — Base
- [ ] 
- [ ] 

### Capa 2 — Funcionalidades principales
- [ ] 
- [ ] 

### Capa 3 — Pulido
- [ ] 
- [ ] 
```

Gastar 30 minutos rellenando esta plantilla te ahorrará mucho tiempo de "descubrir dirección incorrecta a mitad de camino" después.

-----

## Resumen

La ventaja de velocidad del Vibe Coding necesita planes para asegurar la dirección correcta:

1. **Escribe documento de requisitos** — quiénes son los usuarios, qué hacer, qué no hacer
2. **Descompón en tareas específicas** — cuanto más específicas mejor, conveniente para decirle a la IA qué hacer
3. **Prioriza Capa 1/2/3** — siempre construye la base primero
4. **Usa el modo Plan** — haz que la IA liste el plan primero, tú confirmas y luego implementa
5. **Elige 1–3 tareas diarias** — pasos pequeños y rápidos, cada día hay cosas completadas

Los planes no son restricciones, aseguran que tu velocidad se use en el lugar correcto.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Volver al índice del curso](../README-es.md)

¿Listo para empezar con una plantilla full-stack completa de Cloudflare?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Precio early bird $99, sube a $199 el 1 de junio de 2026.
