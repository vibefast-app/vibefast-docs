# Diseñando UI bonita con IA: Guía completa de prompts para Cursor

[English](../en/11-ai-frontend-design-with-cursor-en.md) · [繁中](../zh/11-ai-frontend-design-with-cursor-zh.md) · [Español](../es/11-ai-frontend-design-with-cursor-es.md) · [日本語](../jp/11-ai-frontend-design-with-cursor-jp.md) · [Português (BR)](../pt-br/11-ai-frontend-design-with-cursor-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~12 minutos

-----

## No necesitas aprender diseño — necesitas describir lo que quieres

Los resultados de frontend con IA de la mayoría de personas son: "funciona, pero es feo."

No es porque la IA no sepa diseñar. Es porque las instrucciones no fueron lo suficientemente específicas. "Hazme una homepage bonita" y "Hazme una homepage SaaS con fondo oscuro, botón CTA naranja y IBM Plex Mono para los títulos" producen resultados completamente diferentes.

Este tutorial te enseña cómo dar el segundo tipo de instrucción.

-----

## Algunos conceptos que conviene conocer primero

No necesitas estudiarlos a fondo — solo lo suficiente para entender de qué habla la IA.

### ¿Qué son React y JSX?

**React** es el framework frontend más usado hoy. Tus páginas se construyen a partir de "componentes" individuales — cada componente es responsable de una sección de la UI.

**JSX** es la sintaxis que usa React, que te permite escribir estructura similar a HTML directamente dentro de JavaScript:

```tsx
// Esto es un componente React
export default function ProductCard({ name, price }) {
  return (
    <div className="p-4 border rounded-lg">
      <h2>{name}</h2>
      <p>${price}</p>
    </div>
  )
}
```

Las llaves `{}` contienen expresiones JavaScript. Todo lo demás parece HTML.

### ¿Qué es Remix?

**Remix** es un framework full-stack construido sobre React — el framework frontend que usa vibefast.app. Conceptos clave:

- **loader**: una función que obtiene datos del backend cuando se carga una página
- **action**: una función que maneja acciones del usuario cuando se envía un formulario
- **route**: cada archivo corresponde a una ruta URL — `app/routes/shop.tsx` corresponde a `/shop`

No necesitas entender todo esto completamente. Cuando encuentres algo confuso, pídele a la IA que te lo explique.

### ¿Qué es Tailwind CSS?

**Tailwind** es el framework CSS que usa vibefast.app. En lugar de escribir CSS, aplicas estilos directamente usando nombres de clase en tu HTML:

```tsx
// Sin Tailwind
<button style={{ backgroundColor: 'orange', padding: '12px 24px', borderRadius: '8px' }}>
  Comprar
</button>

// Con Tailwind
<button className="bg-orange-500 px-6 py-3 rounded-lg text-white font-semibold">
  Comprar
</button>
```

Los nombres de clase de Tailwind son intuitivos: `bg-orange-500` es fondo naranja, `px-6` es padding horizontal, `rounded-lg` son esquinas redondeadas. No necesitas memorizarlos — la IA los conoce todos. Solo describe el efecto que quieres.

### Términos comunes de UI que escucharás

- **Hero**: la sección grande en la parte superior de una página, normalmente con un titular, subtitular y botón CTA
- **CTA (Call to Action)**: un botón que invita al usuario a hacer algo — "Comprar ahora," "Empieza gratis"
- **Card**: un bloque de contenido con borde o sombra
- **Grid**: elementos organizados en columnas — como una sección de características a 3 columnas
- **Responsive**: se adapta correctamente tanto en pantallas móviles como de escritorio

-----

## Cómo describir tu estilo de diseño a la IA

Esta es la habilidad más importante. Al dar instrucciones de diseño, cubre estas dimensiones:

### 1. Estilo general

```
"Tema oscuro"
"Blanco minimalista, mucho espacio en blanco"
"Estética tech con efectos de brillo"
"Cálido, sensación personal — adecuado para una marca individual"
```

### 2. Colores

No digas "azul" — sé específico con los nombres de color de Tailwind o valores hex:

```
# Demasiado vago
"Usa un tema azul"

# Suficientemente específico
"Color primario: orange-500 (#f97316), fondo: zinc-950, texto: zinc-100"
```

Familias de colores comunes en Tailwind: `slate`, `zinc`, `neutral` (grises), `orange`, `blue`, `violet`, `emerald`. Cada una tiene tonos del 50 al 950.

### 3. Tipografía

```
"Títulos en IBM Plex Mono (monoespaciada, sensación técnica)"
"Texto del cuerpo en Inter (moderno, legible)"
"Títulos en Bricolage Grotesque (sans-serif distintiva)"
```

Si no estás seguro, simplemente di "sugiere un emparejamiento de fuentes que encaje con un producto SaaS" y la IA te recomendará algo.

### 4. Detalles de elementos

```
"Botones con forma de píldora (rounded-full)"
"Cards con un borde sutil (border border-zinc-800)"
"Animación al pasar el ratón (hover:scale-105 transition)"
"Título con un acento de subrayado naranja"
```

-----

## Recorrido: Construyendo una sección Hero paso a paso

Usaré el diseño de la propia homepage de vibefast.app como ejemplo para demostrar el flujo completo de prompts.

### Ronda 1: Establecer la estructura básica

En Cursor, pulsa `Cmd+L` y escribe:

```
Crea una sección Hero en apps/web/app/routes/_index.tsx.

Especificaciones de diseño:
- Fondo oscuro: bg-zinc-950
- Diseño centrado
- Pequeña insignia arriba con texto "Edge-native · No Next.js · No Vercel", borde naranja
- Titular principal: grande, blanco, fuente IBM Plex Mono, texto "The Cloudflare-Native Web App Template"
- Subtitular: gris, más pequeño, texto "From idea to production in 40 minutes."
- Dos botones lado a lado: botón primario naranja "Buy Now — $99", botón secundario transparente con borde "View Demo"
- Sección completa centrada verticalmente, altura mínima 100vh
```

La IA genera una estructura JSX completa.

### Ronda 2: Refinar los detalles

Después de ver el resultado, notarás cosas a ajustar. Continúa en Cursor:

```
Ajusta algunas cosas:
1. El tamaño de fuente del titular es muy pequeño — cambia a text-5xl md:text-7xl
2. Aumenta el espacio entre los dos botones — cambia gap-4 a gap-6
3. Añade efecto hover al botón primario: hover:bg-orange-400 transition-colors duration-200
4. Añade un brillo radial naranja muy sutil en el fondo del hero
```

### Ronda 3: Añadir detalles decorativos

```
Añade un patrón de puntos en cuadrícula al fondo del hero.
Usa un color zinc-800 muy tenue.
El objetivo es dar profundidad sin distraer.
Similar al estilo de fondo de linear.app.
```

Después de tres rondas, tienes una sección Hero completa con calidad de diseño casi profesional.

-----

## Usa capturas de pantalla para mostrar sitios de referencia a la IA

Esta es la técnica que más dramáticamente mejora la calidad de la salida de la IA.

Encuentra un diseño web que te guste. Haz una captura. Arrástrala directamente al panel de chat de Cursor. Luego di:

```
Usa como referencia el estilo de diseño de esta captura y crea un componente similar de tarjeta de precios.
Requisitos técnicos:
- Tailwind CSS
- Formato JSX de Remix
- Mantener mi tema oscuro zinc-950 existente
- Reemplazar el contenido de texto con los precios de vibefast.app: $99 early bird, incluye repo privado y actualizaciones de por vida
```

Cursor tiene comprensión visual — puede leer el diseño de tu captura y reimplementarlo en tu stack tecnológico.

**Buenos sitios para capturar como referencia:**

- [linear.app](https://linear.app) — oscuro, minimalista, fuerte estética tech
- [vercel.com](https://vercel.com) — estilo SaaS moderno
- [stripe.com](https://stripe.com) — limpio, alta confianza
- [tailwindui.com](https://tailwindui.com) — referencias de componentes directamente

-----

## Problemas comunes y cómo solucionarlos

### Problema 1: La IA produjo un diseño que se rompe en móvil

**Síntoma:** Se ve genial en escritorio, pero el texto es demasiado grande o el layout se desarma en móvil.

**Solución:**

```
El diseño actual tiene problemas en móvil. Añade ajustes responsive:
- Titular: text-3xl en móvil, text-5xl en tablet, text-7xl en escritorio
- Dos botones: apilar verticalmente en móvil (flex-col), lado a lado en escritorio (sm:flex-row)
- Padding horizontal: px-4 en móvil, px-8 en escritorio
```

### Problema 2: El diseño se ve genérico — como cualquier otro sitio generado por IA

**Síntoma:** Azul y blanco, cards redondeadas, sombras — indistinguible de mil otras apps.

**Solución:** Da instrucciones de personalidad más específicas:

```
El diseño actual es demasiado genérico. Haz estos ajustes para darle más carácter:
1. Reemplaza todo el azul con naranja (orange-500)
2. Añade tachado a "No Next.js" en el titular principal
3. Añade un bloque de código estilo terminal mostrando los tres comandos de instalación
4. Cambia la fuente a IBM Plex Mono para una sensación de ingeniería
```

### Problema 3: Querías un cambio pequeño pero la IA cambió demasiado

**Síntoma:** Dijiste "cambia el color del botón a naranja" y la IA reescribió medio componente.

**Solución:** Sé más preciso:

```
Cambia solo esta cosa y deja todo lo demás intacto:
Cambia bg-blue-500 a bg-orange-500 en el elemento <button>.
No modifiques nada más.
```

-----

## Pequeñas técnicas que mejoran inmediatamente la calidad del diseño

Estas son cosas que aprendí construyendo la homepage de vibefast.app. Cada una es menor, pero combinadas hacen una diferencia notable.

**Añadir animaciones de transición**

```tsx
// Escala sutil al pasar el ratón
className="hover:scale-105 transition-transform duration-200"

// Transición de color al pasar el ratón
className="hover:bg-orange-400 transition-colors duration-200"
```

**Texto con gradiente**

```tsx
className="bg-gradient-to-r from-white to-zinc-400 bg-clip-text text-transparent"
```

**Bordes refinados en cards**

```tsx
// Más refinado que un borde blanco plano
className="border border-zinc-800 hover:border-zinc-600 transition-colors"
```

**Efecto de brillo en fondos oscuros**

```tsx
// Brillo radial naranja
<div className="absolute inset-0 bg-[radial-gradient(ellipse_at_center,rgba(249,115,22,0.15),transparent_70%)] pointer-events-none" />
```

-----

## Plantilla completa de prompt

Copia esta plantilla, reemplaza el contenido entre corchetes, y úsala directamente:

```
Crea un [nombre del componente] en [ruta del archivo].

Estilo de diseño:
- Tema: [oscuro / claro]
- Color primario: [ej. orange-500]
- Fondo: [ej. zinc-950]
- Tipografía: [ej. IBM Plex Mono para títulos, Inter para cuerpo]

Contenido:
- [Describe qué debe mostrar este componente]

Requisitos técnicos:
- Tailwind CSS
- Formato JSX de Remix
- Incluir animaciones hover
- Debe ser responsive (visualización correcta en móvil y escritorio)

Estilo de referencia: [si tienes un sitio de referencia, descríbelo o adjunta una captura]
```

-----

## Resumen

Obtener resultados bonitos de frontend de la IA no depende de si la IA sabe diseñar — depende de **si tú puedes describir claramente lo que quieres.**

El flujo de trabajo:

1. **Decide una dirección de estilo** — oscuro/claro, color primario, tipografía
1. **Da el primer prompt** — estructura y especificaciones de diseño juntas
1. **Itera en los detalles** — ajusta una cosa a la vez
1. **Usa capturas para acelerar** — referencia sitios que te gusten y muéstralos a la IA
1. **Añade toques finales** — animaciones, brillos, gradientes — los pequeños detalles marcan una gran diferencia

El diseño nunca está "terminado" — solo "suficientemente bueno para lanzar." Llega al 80%, lanza, y luego itera.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Volver al índice del curso](../README-es.md)

¿Listo para empezar con una plantilla full-stack completa de Cloudflare?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Precio early bird $99, sube a $199 el 1 de agosto de 2026.
