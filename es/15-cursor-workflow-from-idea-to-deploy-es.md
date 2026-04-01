# Cómo uso Cursor para llevar una funcionalidad de idea a producción

[English](../en/15-cursor-workflow-from-idea-to-deploy-en.md) · [繁中](../zh/15-cursor-workflow-from-idea-to-deploy-zh.md) · [Español](../es/15-cursor-workflow-from-idea-to-deploy-es.md) · [日本語](../jp/15-cursor-workflow-from-idea-to-deploy-jp.md) · [Português (BR)](../pt-br/15-cursor-workflow-from-idea-to-deploy-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~10 minutos

-----

## Un proceso real de desarrollo de funcionalidad

Usaré un ejemplo concreto para demostrarlo: **añadir la funcionalidad de "alerta de stock bajo" a un e-commerce de jabón artesanal** — en las páginas de detalle de producto, cuando el stock es 3 o menos, mostrar "Solo quedan X" en texto rojo.

De la idea a producción, todo el proceso tomó unos 20 minutos.

-----

## Paso 1: Definir los requisitos claramente (5 minutos)

El mayor problema que tiene mucha gente al usar herramientas de IA: los requisitos no se expresan con suficiente claridad, los resultados de la IA se desvían constantemente, y después de ir y venir varias veces es más agotador que escribirlo tú mismo.

Antes de empezar, piensa en tres cosas:

**Qué quiero:** En la página de detalle de producto, cuando el stock <= 3, mostrar "Solo quedan X" encima del botón de añadir al carrito en texto rojo pequeño.

**Dónde cambiar:** Archivo de ruta de la página de detalle de producto (`/shop/:slug`)

**Lógica:** Juzgar a partir del campo `stock` devuelto por la API, solo mostrar cuando <= 3, cuando es 0 mostrar "Agotado" y desactivar el botón.

-----

## Paso 2: Decirle a Cursor (3 minutos)

Abre Cursor, pulsa `Cmd+L` para abrir el chat, escribe:

> "En la página de detalle de producto (`/shop/:slug`), cuando el campo `stock` del producto sea <= 3, mostrar 'Solo quedan X' en texto rojo pequeño encima del botón de añadir al carrito. Cuando el stock sea 0, mostrar 'Agotado' y poner el botón como desactivado. Usar Tailwind CSS."

Cursor ve toda la estructura del proyecto, sabe dónde está el archivo, el código generado funciona directamente.

-----

## Paso 3: Revisar el código generado por la IA (2 minutos)

Cursor generó algo como esto:

```typescript
// Lógica de estado de stock
const isOutOfStock = product.stock === 0
const isLowStock = product.stock > 0 && product.stock <= 3
```

```tsx
{/* Alerta de stock */}
{isLowStock && (
  <p className="text-sm text-red-500 mb-2">
    Solo quedan {product.stock}
  </p>
)}
{isOutOfStock && (
  <p className="text-sm text-red-500 mb-2">Agotado</p>
)}

{/* Botón de añadir al carrito */}
<button
  disabled={isOutOfStock}
  className={`w-full py-3 rounded-lg font-semibold ${
    isOutOfStock
      ? 'bg-zinc-300 text-zinc-500 cursor-not-allowed'
      : 'bg-orange-500 text-white hover:bg-orange-400'
  }`}
>
  {isOutOfStock ? 'Agotado' : 'Añadir al carrito'}
</button>
```

Se ve correcto, lógica clara, sin problemas obvios.

-----

## Paso 4: Probar en local (5 minutos)

```bash
npm run dev
```

Abrir la página de detalle de producto, confirmar:

- Stock > 3: el botón se muestra normal, sin alerta
- Stock <= 3: aparece "Solo quedan X" en rojo
- Stock = 0: muestra "Agotado", botón gris y no se puede hacer clic

A veces los estilos generados por la IA difieren ligeramente de lo esperado. Le digo directamente a Cursor: "El gris del botón Agotado es demasiado claro, cámbialo a zinc-400," y lo modifica directamente.

-----

## Paso 5: Desplegar (2 minutos)

```bash
npm run deploy
```

Esperar a que se complete el despliegue, abrir la URL de producción para confirmar que la funcionalidad está en vivo.

Proceso completo: **idea → en producción, 20 minutos.**

-----

## Hábitos que hacen las herramientas de IA más efectivas

De mi experiencia de más de un año haciendo vibe coding, algunos hábitos que mejoran la calidad de la salida de la IA:

**Indica "en qué archivo"**
No solo digas "añádeme una alerta de stock," di "añadir en la ruta de la página de detalle de producto, el archivo es `shop.$slug.tsx`." Cursor puede ver todo el proyecto, pero darle un punto de partida claro hace la salida más precisa.

**Explica "por qué hacerlo así"**
"El chino se calcula a 300 caracteres por minuto" es mejor que "calcula el tiempo de lectura." La información de contexto permite a la IA tomar decisiones más apropiadas.

**Cambia una cosa a la vez**
No digas todo de una vez "añade tiempo de lectura, añade botón de compartir, cambia el estilo del título." Divide en tres peticiones, confirma que cada una está bien antes de continuar. Cambiar varias cosas simultáneamente, cuando surgen problemas es difícil identificar dónde.

**Pregunta sobre código que no entiendes**
"¿Qué hace este `Math.ceil`?" Pregunta directamente en Cursor, te lo explicará. No necesitas entender completamente cada línea, pero deberías tener comprensión básica de la lógica clave, de lo contrario cuando surjan problemas no sabrás dónde buscar.

**Prueba en local antes de desplegar**
Ejecuta `npm run dev` en local una vez, confirma que no hay problemas antes de `npm run deploy`. Descubrir problemas después de desplegar a producción, hacer rollback es más complicado.

-----

## ¿Qué funcionalidades se adaptan a este flujo de trabajo?

**Muy adecuado:**

- Añadir y modificar componentes de UI
- Nuevos endpoints API
- Consultas de base de datos
- Lógica de validación de formularios
- Ajustes de estilo

**Necesitan más precaución:**

- Lógica que involucra pagos (webhooks de Stripe, cálculos de montos)
- Modificaciones relacionadas con autenticación
- Cambios de base de datos que afectan la estructura de datos existente

Para lo segundo, el código generado por la IA puede ser un punto de partida, pero revisa más cuidadosamente, prueba manualmente cada caso límite cuando sea necesario.

-----

## Resumen

Flujo de trabajo central de Vibe Coding:

```
1. Pensar bien los requisitos (qué quieres, dónde, cómo se calcula)
2. Dar instrucciones claras a la IA (indica la ruta del archivo, la lógica de negocio)
3. Revisar la salida de la IA, juzgar si es correcta
4. Probar en local
5. Desplegar
```

Punto más importante: **Tú eres el director, la IA es quien ejecuta.** En cada paso tú juzgas la dirección, la IA implementa los detalles.

-----

**Danko Peng**  
[@dankopeng](https://x.com/dankopeng) · [vibefast.app](https://vibefast.app)

-----

👉 [Volver al índice del curso](../README-es.md)

¿Listo para empezar con una plantilla full-stack completa de Cloudflare?  
👉 **[vibefast.app](https://vibefast.app)** — Precio early bird $99, sube a $199 el 1 de junio de 2026.
