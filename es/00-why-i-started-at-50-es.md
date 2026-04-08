# Por qué empecé a construir en serio a los 50

[English](../en/00-why-i-started-at-50-en.md) · [繁中](../zh/00-why-i-started-at-50-zh.md) · [Español](../es/00-why-i-started-at-50-es.md) · [日本語](../jp/00-why-i-started-at-50-jp.md) · [Português (BR)](../pt-br/00-why-i-started-at-50-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~8 minutos

-----

## Llevaba 20 años sin escribir código

Estudié informática en la universidad. Después de graduarme, trabajé como ingeniero de software durante algunos años.

Luego monté mi propia empresa.

Una vez que entré en el mundo de dirigir un negocio — gestionar personas, tratar con clientes — la parte técnica de mi vida fue desapareciendo poco a poco. No de un día para otro. Simplemente, a lo largo de dos décadas, lo que una vez sabía quedó tan lejos del estado actual de la industria que casi no contaba.

Durante esos años, tuve muchas ideas. "Estaría genial si existiera una app que hiciera X." "¿Por qué nadie ha resuelto este problema?" Las ideas seguían llegando. Pero no tenía forma de convertirlas en productos reales. Sabía programar, solo que no de la forma moderna. Contratar ingenieros era caro, y el coste de comunicación era aún mayor. ¿Aprender por mi cuenta? Cada vez que lo intentaba, algo me interrumpía — o llegaba a mitad de aprender algo y descubría que necesitaba otras diez cosas primero.

Así que las ideas se quedaron en mi cabeza durante años. Y luego desaparecieron en silencio.

-----

## Entonces algo cambió a finales de 2024

Empecé a usar herramientas de IA en serio — no solo para hacer preguntas, sino para escribir código de verdad.

La primera vez que la IA me generó una aplicación web funcional, me quedé frente a la pantalla con una sensación extraña. ¿Cómo se volvió tan fácil?

No es que no haya curva de aprendizaje. Sigues necesitando entender conceptos básicos. Sigues necesitando juzgar si lo que produce la IA es correcto. Sigues necesitando resolver errores. Pero para mí había una ventaja extra: los fundamentos de mi formación en informática seguían ahí. La lógica, los modelos mentales — solo enterrados bajo veinte años de óxido. La IA me ayudó a cerrar esa brecha.

El muro que decía "estás demasiado atrasado para crear productos" de repente se hizo mucho más bajo.

-----

## ¿Por qué Cloudflare?

Probé diferentes stacks tecnológicos.

Next.js y Vercel son la combinación más documentada, y los usé durante un tiempo. Pero cada vez que algo fallaba, tenía que perseguir el problema entre Vercel, Supabase y AWS — documentación dispersa en tres plataformas, sin un punto claro por donde empezar.

Entonces miré en serio el ecosistema de Cloudflare y descubrí que resolvía la mayoría de lo que me molestaba:

- Una cuenta, un panel de control, una factura
- Workers significa cero servidores que gestionar — desplegar es simplemente desplegar
- D1 está justo al lado de Workers — sin llamadas a bases de datos a través de la red
- R2 para almacenamiento de archivos con cero costes de transferencia

Y lo más importante: un solo comando para llevar una app de cero a un entorno de producción desplegado globalmente.

Para un emprendedor en solitario, cada servicio que no tienes que gestionar es una distracción menos que te aleja de tu producto.

-----

## Cómo nació vibefast.app

Después de construir varios proyectos en Cloudflare, noté que siempre hacía lo mismo al inicio de cada proyecto nuevo: configurar autenticación, configurar la base de datos, configurar Stripe, configurar el email, conectar el frontend con el backend.

Días de trabajo. Y cada vez, caía en al menos una de las mismas trampas que ya había pisado antes.

Empecé a consolidar toda esa configuración en un punto de partida reutilizable — algo que pudiera clonar para cada nuevo proyecto, cambiar la marca y lanzar.

Luego pensé: si este punto de partida me es útil a mí, probablemente también les sea útil a otros Vibe Coders.

Así nació vibefast.app.

-----

## ¿Qué tiene de diferente empezar a los 50?

Siendo honesto — algunas cosas son más difíciles.

Aprendo cosas nuevas más lento que a los veinte. Cuando miro TypeScript complejo, mi cerebro necesita más tiempo. Veinte años fuera del campo no es algo de lo que te recuperes completamente en unos meses, incluso con una base en informática.

Pero algunas cosas son más fáciles.

Sé exactamente qué problema quiero resolver, porque tengo suficientes años de experiencia real en negocios para reconocer lo que realmente duele. No necesito buscar "una buena idea para construir." Construyo las cosas que desearía que existieran.

Y ya no necesito demostrarle nada a nadie. Una ventaja de tener cincuenta es que las opiniones de los demás pesan menos. Eso hace más fácil tomar decisiones en las que realmente crees, en lugar de decisiones que crees que serán aprobadas.

-----

## Para quien piense que empezó demasiado tarde

Si estás en algún momento de tu vida, leyendo sobre personas que empezaron a programar a los catorce y sintiendo que perdiste tu oportunidad —

No creo que exista algo como "demasiado tarde para empezar."

Las herramientas de IA han bajado la barrera significativamente. No necesitas aprender un lenguaje de programación desde cero ni entender cada principio subyacente. Lo que necesitas es: un problema real que quieras resolver, la disposición para aprender lo suficiente para evaluar lo que produce la IA, y la paciencia para seguir adelante cuando las cosas fallan.

Todo lo que hay en el repositorio vibefast-docs es lo que he aprendido, escrito en un lenguaje que cualquiera puede seguir. El curso es gratuito porque quiero que esta puerta esté abierta para más personas.

Si quieres un punto de partida ya configurado — para que puedas enfocarte en construir tu producto en lugar de montar infraestructura — para eso existe vibefast.app.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Volver al índice del curso](../README-es.md)

¿Listo para empezar con una plantilla full-stack completa de Cloudflare?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Precio early bird $99, sube a $199 el 1 de junio de 2026.
