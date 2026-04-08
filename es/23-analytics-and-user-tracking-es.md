# Analíticas y tráfico: Entendiendo qué hacen tus usuarios

[English](../en/23-analytics-and-user-tracking-en.md) · [繁中](../zh/23-analytics-and-user-tracking-zh.md) · [Español](../es/23-analytics-and-user-tracking-es.md) · [日本語](../jp/23-analytics-and-user-tracking-jp.md) · [Português (BR)](../pt-br/23-analytics-and-user-tracking-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~6 minutos

-----

## Necesitas datos para tomar buenas decisiones

"¿De dónde vienen mis usuarios?" "¿Qué página recibe más visitas?" "¿Por qué el tráfico cayó de repente el martes?"

Sin datos, estás adivinando. Adivinas bien y tuviste suerte. Adivinas mal y has perdido tiempo optimizando lo equivocado.

La buena noticia: si tu app corre en Cloudflare, ya tienes datos básicos de tráfico — y no tuviste que instalar nada para obtenerlos.

-----

## Cloudflare Analytics: Gratis, sin configuración, ya funcionando

En el momento en que tu app está desplegada en Cloudflare Workers, Cloudflare empieza automáticamente a registrar todos los datos de tráfico.

Ve a [Cloudflare Dashboard](https://dash.cloudflare.com), haz clic en tu Worker, y selecciona la pestaña "Analytics". Verás:

- **Volumen de peticiones**: cuántas peticiones llegan por día y por hora
- **Desglose de códigos de estado**: la proporción entre 200s, 404s, 500s — detecta anomalías de un vistazo
- **Tiempo de respuesta**: velocidad promedio de respuesta, si se está haciendo más lento
- **Tráfico por región**: de qué países y regiones vienen tus usuarios

Estos datos son:

- Completamente gratuitos
- No requieren cambios en tu código
- Respetuosos con la privacidad — sin rastreo personal, sin banner de consentimiento de cookies necesario
- En tiempo real — sin retrasos de varias horas como otras herramientas

Para una app nueva, esto es todo lo que necesitas.

-----

## ¿Qué puede decirte Cloudflare Analytics?

### ¿Está viniendo alguien?

La pregunta más básica. El gráfico de volumen de peticiones diarias muestra instantáneamente la tendencia de tu tráfico — si está creciendo, cuándo cayó, si un post que publicaste realmente generó tráfico.

### ¿Algo está roto?

El desglose de códigos de estado es un indicador práctico de salud. Un pico repentino de errores 500 significa que algo anda mal en el backend. Muchos 404s significa que algunos enlaces se han roto. Revisa este gráfico después de cada despliegue para confirmar que no has introducido nuevos problemas.

### ¿De dónde vienen tus usuarios?

El desglose regional muestra dónde está tu audiencia principal, lo que te ayuda a decidir en qué mercados enfocarte para la promoción.

-----

## Cloudflare Web Analytics: Rastrear vistas de páginas del frontend

Cloudflare Workers Analytics registra todas las peticiones HTTP, incluyendo llamadas API. Si quieres rastrear específicamente las vistas de páginas del frontend, usa **Cloudflare Web Analytics** — también gratuito, también sin cookies requeridas.

Si tu dominio ya está añadido a Cloudflare, Web Analytics se habilita automáticamente sin configuración necesaria y sin script que añadir a tu código.

Ve a Cloudflare Dashboard → selecciona tu dominio → Web Analytics, y verás:

- Vistas de página por página
- Conteo de visitantes y tiempo en página
- Fuentes de tráfico (directo, búsqueda, redes sociales)
- Tipos de dispositivo (móvil vs escritorio)

-----

## Con qué métricas empezar

Demasiados datos son un problema en sí — terminas en "parálisis por análisis," mirando números sin saber qué hacer con ellos.

**Cuando recién lanzas, rastrea solo tres números:**

1. **Volumen diario de peticiones** — ¿está viniendo alguien? ¿La tendencia es hacia arriba o hacia abajo?
1. **Tasa de errores** — ¿algún pico de errores 500? ¿El último despliegue introdujo nuevos problemas?
1. **Tráfico por región** — ¿de dónde vienen mayormente tus usuarios? ¿Coincide con tu mercado objetivo?

Una vez que tengas tráfico estable, empieza a profundizar en datos a nivel de página.

-----

## Analíticas integradas de vibefast.app: Directo en el Dashboard

Si estás usando vibefast.app, las analíticas de tráfico ya están incorporadas.

Inicia sesión en el panel de admin de vibefast.app y la página principal del Dashboard muestra gráficos de tráfico directamente — sin necesidad de abrir el Cloudflare Dashboard, sin configuración requerida. Los datos de tráfico de tu app están justo frente a ti.

Para una app web en etapa temprana, esto es más que suficiente. Puedes:

- Ver de un vistazo si el conteo de visitantes de hoy subió o bajó respecto a ayer
- Identificar qué páginas están recibiendo más tráfico
- Confirmar si una promoción o post realmente trajo visitantes a tu sitio

-----

## Si necesitas análisis más profundo después

Los datos integrados de Cloudflare son suficientes para empezar. A medida que tu app crece y quieres rastrear comportamiento de usuario más granular — qué botones hace clic la gente, dónde abandonan en un flujo de compra — ahí es cuando vale la pena considerar herramientas de terceros.

Pero ese es un problema para después. Construye la app primero. Consigue el tráfico primero. Luego piensa en analíticas más profundas.

-----

## Resumen

El objetivo de las analíticas no es recopilar datos — es tomar mejores decisiones con datos.

El orden para empezar:

1. **Cloudflare Workers Analytics**: gratuito, sin configuración, disponible en el momento que despliegas — empieza con volumen de peticiones y tasa de errores
1. **Cloudflare Web Analytics**: se habilita automáticamente para cualquier dominio en Cloudflare — vistas de página y fuentes de tráfico sin configuración
1. **Dashboard de vibefast.app**: si estás usando vibefast.app, los gráficos de tráfico están directo en la homepage del admin

Las decisiones basadas en datos no son solo para grandes empresas. Saber lo que tus usuarios realmente hacen supera adivinar cada vez.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Volver al índice del curso](../README-es.md)

¿Listo para empezar con una plantilla full-stack completa de Cloudflare?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Precio early bird $99, sube a $199 el 1 de junio de 2026.
