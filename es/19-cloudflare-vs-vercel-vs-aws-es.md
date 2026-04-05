# Por qué elegí Cloudflare en vez de Vercel y AWS

[English](../en/19-cloudflare-vs-vercel-vs-aws-en.md) · [繁中](../zh/19-cloudflare-vs-vercel-vs-aws-zh.md) · [Español](../es/19-cloudflare-vs-vercel-vs-aws-es.md) · [日本語](../jp/19-cloudflare-vs-vercel-vs-aws-jp.md) · [Português (BR)](../pt-br/19-cloudflare-vs-vercel-vs-aws-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~8 minutos

-----

## Esto no es una comparación neutral

Hay montones de artículos "Vercel vs AWS vs Cloudflare: La comparación completa" por ahí. Cada uno termina con "depende de tu caso de uso."

Este no es ese tipo de artículo.

Soy un emprendedor en solitario que gasta su propio dinero y tiempo construyendo productos. Esta es mi decisión real: **Elegí Cloudflare, y creo que la mayoría de Vibe Coders deberían hacerlo también.**

Si quieres un análisis equilibrado y neutral, probablemente esto no es para ti. Si quieres saber lo que piensa alguien que realmente usa estas plataformas — sigue leyendo.

-----

## El problema de Vercel no es la tecnología — es el modelo de negocio

La experiencia de desarrollo de Vercel es genuinamente buena. No voy a discutir eso. Despliegues automáticos con `git push`, URLs de preview auto-generadas — son detalles bien pensados y bien hechos.

Pero el modelo de negocio de Vercel se construye sobre una premisa: **cuanto más exitosa sea tu app, más pagas.**

El ancho de banda de salida cuesta dinero. El tiempo de ejecución de funciones serverless cuesta dinero. Una vez que superas el nivel gratuito, tu factura escala con tu tráfico.

Eso no es inherentemente malo — Vercel es una empresa, necesita ingresos. Pero para emprendedores independientes, significa que tu curva de costes está directamente ligada al crecimiento de usuarios. El período cuando tu app recién empieza a despegar es a menudo el mismo período cuando aún no tienes suficientes ingresos para cubrirlo. Es exactamente cuando la factura empieza a dispararse.

El problema mayor es: **Vercel no tiene base de datos ni almacenamiento de archivos.**

Necesitas buscar Supabase para tu base de datos, AWS S3 para almacenamiento de imágenes, Resend para email. Cada servicio que añades significa otra cuenta, otra factura, otra cosa que puede fallar. Crees que estás usando una plataforma — en realidad estás gestionando una red distribuida de servicios.

-----

## El problema de AWS no es el coste — es la complejidad

Los costes de AWS no son realmente tan locos como algunos afirman. Comparado con lo que cobra Vercel a escala, AWS es a menudo más barato en la práctica.

Pero AWS tiene un problema diferente: **está diseñado para equipos de ingeniería, no para emprendedores en solitario.**

VPCs, Security Groups, IAM Roles, Load Balancers, Auto Scaling Groups — solo poner en marcha una app web básica requiere entender una docena de conceptos y configurar docenas de opciones. Cualquier mala configuración puede significar vulnerabilidades de seguridad, caídas del servicio, o cargos inexplicables.

La filosofía de diseño de AWS es máxima flexibilidad. Puedes controlar cada detalle. Pero la mayoría de apps independientes no necesitan esa flexibilidad — lo que necesitan es "configurarlo una vez y no volver a pensar en ello."

La IA puede generar configuraciones de Terraform y templates de CloudFormation por ti. Pero aún necesitas entender qué están haciendo esas cosas, de lo contrario cuando algo falle no tendrás idea de por dónde empezar.

-----

## ¿Qué problema resuelve realmente Cloudflare?

No elegí Cloudflare porque sea lo más barato o porque sea lo más rápido. Lo elegí porque resuelve el problema que más me importa: **dejarme poner toda mi energía en el producto, no en la infraestructura.**

**Una cuenta. Un panel de control. Una factura.**

Workers ejecuta tu código, D1 almacena tus datos, R2 almacena tus archivos — todo dentro de Cloudflare. Sin cambiar entre cuatro o cinco plataformas, sin gestionar cuatro o cinco conjuntos de claves API. Cuando algo falla, solo hay un lugar donde mirar.

**Cero costes de transferencia de salida.**

Si un millón de usuarios descargan imágenes de R2, el coste de egress es cero. Cuanto más exitosa se vuelve tu app, más obvia se hace esta ventaja. Los costes de egress de Vercel y AWS se acumulan rápido con mucho tráfico.

**Despliegue global desde el primer día.**

Cloudflare tiene más de 300 nodos edge. Tu código se ejecuta automáticamente lo más cerca posible de cada usuario. Un usuario en Madrid se conecta a un nodo europeo. Sin configuración de CDN, sin configuración multi-región — simplemente funciona.

**Nivel gratuito generoso.**

Workers obtiene 100.000 peticiones diarias gratis. D1 obtiene 5 millones de lecturas mensuales gratis. R2 obtiene 10GB de almacenamiento gratis. Para una app nueva, puede que nunca alcances los límites. Escala cuando tengas los usuarios y los ingresos para justificarlo.

-----

## Limitaciones de Cloudflare — deberías conocerlas también

No estoy escribiendo un anuncio, así que esto es en lo que Cloudflare no es bueno:

**Workers tiene límites de tiempo de CPU.** Plan gratuito: 10ms de tiempo de CPU por petición. Plan de pago: hasta 30 segundos. Transcodificación de video, procesamiento pesado de datos, inferencia de modelos IA — estas cosas no pertenecen a Workers.

**D1 es SQLite, no PostgreSQL.** Si necesitas funcionalidades complejas de base de datos — joins sofisticados, procedimientos almacenados, búsqueda de texto completo — D1 puede no ser suficiente. La mayoría de apps independientes nunca necesitan esto, pero si tu lógica de negocio es inusualmente compleja, piénsalo bien.

**El ecosistema es relativamente nuevo.** D1 aún está madurando. La funcionalidad principal es estable y lista para producción, pero si tu app tiene requisitos muy específicos, verifica que estén soportados antes de comprometerte.

-----

## Una pregunta para ayudarte a decidir

No necesitas un árbol de decisión. Una pregunta es suficiente:

**¿Estás construyendo solo, o con un equipo?**

Construyendo solo: elige Cloudflare. Tu tiempo es tu recurso más valioso. Cualquier elección que reduzca el tiempo que pasas gestionando infraestructura es la elección correcta.

Construyendo con equipo: depende de tu stack. Si el equipo ya está profundamente metido en Next.js, Vercel está bien. Si tienes requisitos complejos de backend, tanto Cloudflare como AWS funcionan — depende del background técnico del equipo.

-----

## La conclusión

Vercel es bueno, pero su estructura de costes y el ecosistema fragmentado de servicios lo hacen agotador de gestionar como desarrollador independiente.

AWS es poderoso, pero su complejidad significa que la mayoría de emprendedores independientes gastan demasiado tiempo en configuración y no suficiente tiempo construyendo.

Cloudflare no es perfecto, pero me da lo que realmente quiero: **un lugar para gestionar todo, costes predecibles, y despliegue global desde el primer día.**

Esa es mi elección. Tu situación puede ser diferente. Pero si estás construyendo tu primer producto, trabajando solo, y no quieres gastar tiempo gestionando infraestructura — mi recomendación es: empieza con Cloudflare.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Volver al índice del curso](../README-es.md)

¿Listo para empezar con una plantilla full-stack completa de Cloudflare?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Precio early bird $99, sube a $199 el 1 de junio de 2026.
