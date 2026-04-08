# Cómo discutir proyectos con IA: Piensa primero, codifica después

[English](../en/14-how-to-discuss-with-ai-before-coding-en.md) · [繁中](../zh/14-how-to-discuss-with-ai-before-coding-zh.md) · [Español](../es/14-how-to-discuss-with-ai-before-coding-es.md) · [日本語](../jp/14-how-to-discuss-with-ai-before-coding-jp.md) · [Português (BR)](../pt-br/14-how-to-discuss-with-ai-before-coding-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~8 minutos

-----

## La mayoría de personas usa la IA de forma incorrecta

Te encuentras con un problema, abres Cursor inmediatamente, dices: "Arréglame este bug" o "Añádeme esta funcionalidad."

La IA genera código, lo pegas, lo ejecutas, parece funcionar. Luego descubres que rompió otra cosa, le pides a la IA que lo arregle, rompe otra cosa más...

Media hora después, estás dando vueltas en círculos, el código cada vez más desordenado, tú cada vez más frustrado.

**El problema no es que la IA no sea buena, es que te saltaste el paso más importante: discutir primero, implementar después.**

-----

## ¿Por qué discutir primero?

La IA es muy buena ejecutando — le dices qué hacer, lo hace, y lo hace rápido.

Pero "ejecutar rápidamente en la dirección equivocada" desperdicia más tiempo que "pensar despacio y luego hacerlo."

Beneficios de discutir primero:

- **Confirmar cuál es tu problema real** — crees que es el problema A, después de discutir descubres que en realidad es el problema B, resolver B es diez veces más rápido que resolver A
- **Dejar que la IA sugiera opciones que no se te ocurrieron** — la IA ha visto toneladas de escenarios similares, sabe qué enfoques existen y sus ventajas e inconvenientes
- **Evitar construir y luego demoler** — las decisiones de arquitectura una vez tomadas incorrectamente son costosas de cambiar, discutir no cuesta casi nada

-----

## Principio central: Primero di "No escribas código todavía"

Cuando quieras discutir un problema, **dile a la IA desde el principio que solo quieres discutir, que no quieres que escriba código todavía**:

```
Quiero discutir un problema contigo, no escribas código todavía,
espera hasta que lleguemos a un consenso antes de implementar.
```

Esta frase es importante. El comportamiento por defecto de la IA es "dar soluciones," tenderá a generar código directamente. Tú dices explícitamente "no escribas código todavía," y realmente entrará en modo de discusión, dándote análisis, opciones, sugerencias, en vez de solo lanzarte código.

-----

## Tres etapas de discusión

### Etapa 1: Expón tu problema claramente

Describe el problema completamente, no solo digas síntomas, da el contexto:

```
Lo que estoy haciendo: [cuál es tu proyecto, dónde estás ahora]
Qué problema enfrento: [descripción específica, no solo "no funciona"]
Lo que he intentado: [qué intentos has hecho]
Lo que quiero lograr: [cuál esperas que sea el resultado final]
```

**Ejemplo — descripción incompleta del problema:**

```
Mi login está roto, arréglalo.
```

**Ejemplo — descripción completa del problema:**

```
No escribas código todavía.

Estoy haciendo una app de suscripción construida con vibefast.app.
Problema: Después de que los usuarios inician sesión, al refrescar la página se desloguean.
Ya intenté: Confirmé que el token JWT se genera correctamente, la cookie también se establece.
Objetivo: El estado de login debería persistir después de refrescar.

¿Cuál crees que podría ser la causa? ¿Cuáles son las posibles soluciones?
```

La segunda descripción permite a la IA dar un diagnóstico significativo, en vez de adivinar una dirección y cambiar cosas al azar.

-----

### Etapa 2: Deja que la IA sugiera opciones

No solo preguntes "cómo hacerlo," pregunta "cuáles son los enfoques, cuáles son sus ventajas e inconvenientes":

```
# Pregunta no suficientemente buena
"¿Cómo implementar la funcionalidad de notificaciones de usuario?"

# Mejor pregunta
"Quiero añadir funcionalidad de notificaciones de usuario, no escribas código todavía.
¿Cuáles son los enfoques de implementación? ¿Cuál es la complejidad y los trade-offs de cada uno?
Mi app actualmente usa la arquitectura vibefast.app (Remix + Cloudflare Workers + D1),
la escala todavía es pequeña, solo yo la mantengo."
```

La IA podría darte tres opciones:

1. Versión simple: almacenar registros de notificación directamente en DB, frontend hace polling
2. Versión intermedia: usar Cloudflare Queues para notificaciones asíncronas
3. Versión compleja: WebSocket para push en tiempo real

Con esta lista, puedes tomar decisiones basadas en tu situación — no necesitas lo más complejo, necesitas lo que se adapte a tu etapa actual.

-----

### Etapa 3: Confirmar consenso antes de implementar

Después de la discusión, antes de empezar la implementación, primero confirma que ambas partes tienen entendimiento compartido sobre lo mismo:

```
Vale, he decidido el enfoque 1 (almacenar notificaciones en DB + polling).
Antes de escribir código, confirmemos nuestro consenso:

1. Crear nueva tabla notifications, campos son id, user_id, message, is_read, created_at
2. Añadir endpoint GET /api/notifications, devuelve las notificaciones no leídas del usuario actual
3. Frontend hace polling cada 30 segundos, muestra punto rojo cuando hay notificaciones nuevas
4. Después de leer notificaciones, llamar PATCH /api/notifications/:id para marcar como leída

¿Es correcto este entendimiento? ¿Hay algo que no haya considerado?
```

Este paso parece redundante, pero es muy valioso. Tú articulas el plan con tus propias palabras, la IA puede señalar lo que te faltó — como "olvidaste considerar la paginación cuando los usuarios tienen muchas notificaciones" — descubrirlo antes de implementar es mucho más fácil que cambiarlo después.

Después de confirmar que no hay problemas, entonces di:

```
Bien, ahora empieza a implementar. Comienza con la migración de base de datos.
```

-----

## Un ejemplo completo de discusión

A continuación hay un extracto de una conversación completa en un escenario real, demostrando cómo se ve realmente este proceso.

-----

**Tú:**

```
No escribas código todavía.

Estoy usando vibefast.app para hacer una app que vende productos digitales.
Problema: Quiero que los compradores puedan descargar repetidamente los archivos que compraron,
pero no quiero que personas que no han pagado accedan directamente a los enlaces de descarga.

Actualmente los archivos en R2 son públicos, cualquiera que conozca la URL puede descargar.

¿Qué métodos pueden proteger estos enlaces de descarga?
```

**IA:**

```
Vale, discutamos. Este problema tiene algunas soluciones comunes:

Enfoque 1: Signed URL (enlaces pre-firmados)
Cuando los usuarios solicitan descarga, el backend primero verifica que tienen registro de compra,
luego genera una R2 Signed URL con tiempo limitado (ej., 15 minutos de validez),
devuelve esta URL al frontend. El frontend usa directamente esta URL para descargar.
Pros: Simple, R2 lo soporta nativamente. Contras: La URL se puede compartir (válida 15 minutos).

Enfoque 2: Descarga proxy por backend
La petición de descarga primero llega a tu Workers API, la API verifica identidad,
Workers lee directamente el stream del archivo de R2, lo devuelve al usuario.
Pros: Control completo, puede rastrear conteos de descargas, limitar veces de descarga.
Contras: Workers debe transmitir el archivo completo, usa tiempo de CPU.

Para tu situación, el enfoque 1 normalmente es suficiente. ¿Quieres limitar las veces de descarga?
```

**Tú:**

```
No necesito limitar las veces, pero quiero rastrear los conteos de descarga.
Enfoque 1 + registrar descargas en DB, ¿funcionaría?
```

**IA:**

```
Sí, esta combinación es muy razonable. El flujo sería:

1. Usuario hace clic en descargar → llega a POST /api/downloads/:productId
2. API verifica que el usuario ha comprado este producto
3. Registra en la tabla download_logs de la DB (user_id, product_id, timestamp)
4. Genera R2 Signed URL válida por 15 minutos
5. Devuelve la URL, el frontend redirige a descargar

Necesita crear nueva tabla download_logs. ¿Es correcto este entendimiento?
```

**Tú:**

```
Sí, eso funciona. Ahora empieza a implementar, comienza con la tabla.
```

-----

Esta conversación tomó unos cinco minutos, pero antes de empezar a codificar ya sabes:

- Qué enfoques existen
- Cuál elegiste y por qué
- Cuál es el alcance de la implementación (una tabla + un endpoint API)

La implementación posterior tiene objetivos claros, la IA no se desviará del rumbo, y tú no descubrirás a mitad de camino que la dirección era incorrecta.

-----

## ¿Cuándo discutir, cuándo codificar directamente?

No todo necesita discusión previa.

**Bueno para discutir primero:**

- Decisiones de arquitectura (¿esta funcionalidad va en frontend o backend?)
- Problemas con múltiples enfoques (¿de qué formas se puede implementar X?)
- No estás seguro de dónde está la raíz del problema (¿por qué se rompió Y?)
- Cambios con mayor alcance de impacto

**Se puede codificar directamente:**

- Ajustes de UI simples y claros (cambiar el color de este botón a naranja)
- Ya sabes claramente cómo hacerlo, solo necesitas que la IA ayude a escribir
- Añadir una nueva página, lógica igual a una página existente

Principio general: **cuando hay incertidumbre, discute primero; cuando hay certeza, codifica directamente.**

-----

## Resumen

Discutir primero, implementar después — uno de los hábitos más valiosos a desarrollar en Vibe Coding.

Recuerda este proceso:

1. **Di "no escribas código todavía"** — deja que la IA entre en modo de discusión
2. **Expón claramente el contexto** — qué estás haciendo, qué enfrentas, qué has intentado, qué quieres lograr
3. **Pide opciones y trade-offs** — "¿cuáles son los enfoques, cuáles son sus ventajas e inconvenientes?"
4. **Confirma consenso** — articula el plan con tus propias palabras, deja que la IA confirme o complemente
5. **Entonces empieza a implementar** — con objetivos claros, la IA puede hacer lo que realmente quieres

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Volver al índice del curso](../README-es.md)

¿Listo para empezar con una plantilla full-stack completa de Cloudflare?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Precio early bird $99, sube a $199 el 1 de junio de 2026.
