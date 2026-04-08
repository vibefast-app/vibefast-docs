# Preguntas Frecuentes

[English](./faq.md) · [繁中](./faq-zh.md) · [日本語](./faq-jp.md) · [Español](./faq-es.md) · [Português (BR)](./faq-pt-br.md)

-----

## Sobre el producto

### ¿vibefast.app es open source?

No. vibefast.app template es un producto comercial. Este repositorio público sirve únicamente como introducción y punto de entrada a recursos de aprendizaje.

### ¿Cuál es la diferencia entre el repositorio público y el privado?

El repositorio público contiene la introducción del producto y tutoriales de Vibe Coding. El repositorio privado contiene el código fuente completo y toda la documentación para compradores — se entrega después de la compra.

### ¿Puedo usar vibefast.app template para mi propio producto?

Sí — ese es el caso de uso principal. La mayoría de los compradores lo rebrandean directamente como su propio producto, o lo usan como base para un SaaS o web app personalizado.

### ¿Tengo que mantener todos los módulos predeterminados?

No. vibefast.app template se entrega como un producto de referencia completo, pero puedes mantener, ajustar o eliminar módulos según lo que necesites. Si no necesitas el blog, por ejemplo, puedes eliminarlo sin afectar nada más.

### ¿Se publicarán aquí todos los detalles técnicos?

No. Este repositorio cubre solo las introducciones y tutoriales públicos. La documentación completa de arquitectura, guías de despliegue y la referencia de API están dentro del repositorio privado.

-----

## Sobre la compra

### ¿Cómo lo compro? ¿Cómo obtengo el código después de pagar?

Ve a [vibefast.app](https://vibefast.app) y completa el pago con Stripe. Después del pago, el sistema envía automáticamente una invitación de GitHub a tu email. Acepta la invitación para acceder al repositorio privado. Todo el proceso es automático y normalmente se completa en unos minutos.

Si no has recibido la invitación después de 10 minutos, revisa tu carpeta de spam o contacta a [hello@dankoai.com](mailto:hello@dankoai.com).

### ¿El precio early bird es por tiempo limitado?

Sí. El precio de $99 está disponible hasta el 1 de junio de 2026, luego sube a $199. Comprar ahora versus comprar después del 1 de junio es una diferencia de $100.

### ¿Qué incluye el pago único?

$99 incluye: acceso al repositorio privado, el código fuente completo, toda la documentación para compradores y todas las actualizaciones futuras del template. Sin cuota mensual. Sin costos ocultos.

### ¿Cuál es la política de reembolso?

Dado que es un producto digital con acceso inmediato al código fuente tras la compra, no se ofrecen reembolsos como política general. Si encuentras problemas técnicos después de comprar, contacta primero a [hello@dankoai.com](mailto:hello@dankoai.com) — haremos lo posible para ayudarte a resolverlo.

-----

## Sobre la tecnología

### ¿Cuánto conocimiento técnico necesito para usar vibefast.app template?

Lo suficiente para leer y seguir los tutoriales de este repositorio. vibefast.app template está diseñado para ser amigable con los vibe coders — combinado con herramientas de IA como Cursor o Claude, personas sin experiencia profunda en ingeniería pueden ponerlo a funcionar. Si puedes ejecutar `npm install` y entender lo que el código generado por IA está haciendo, es suficiente.

### ¿Necesito una cuenta de Cloudflare de pago?

No. El plan gratuito de Cloudflare es más que suficiente para lanzar una app real — Workers con 100K solicitudes/día, D1 con 100K lecturas/día, R2 con 1M operaciones/mes. Puedes actualizar cuando tu app realmente tenga el tráfico que lo justifique.

### ¿vibefast.app template soportará otros stacks en el futuro?

vibefast.app template está enfocado en el full-stack de Cloudflare (Remix + Workers + D1 + R2) — esa es su posición central. Soportar otros stacks o plataformas de despliegue no está en el roadmap actual.

### ¿Puedo usar vibefast.app template en múltiples proyectos?

Sí. La licencia actual soporta uso en múltiples proyectos. Puedes usarlo libremente en tantos proyectos personales o de clientes como quieras — incluyendo trabajo de agencia.

### ¿En qué se diferencia vibefast.app template de otros boilerplates como ShipFast o Supastarter?

vibefast.app template está construido nativamente sobre el full-stack de Cloudflare (Remix + Workers + D1 + R2). Las diferencias clave: verdadero despliegue con un solo comando (`npm run setup`), cero cold starts en el edge, sin hosting de base de datos separado, y Service Binding entre frontend y API con cero overhead de CORS. No es una template genérica — es una arquitectura con opinión, probada en producción.

-----

## Sobre el soporte

### ¿A quién contacto si tengo preguntas después de comprar?

Puedes contactarnos en:

- Email: [hello@dankoai.com](mailto:hello@dankoai.com)
- X: [@dankopeng](https://x.com/dankopeng)

### ¿Con qué frecuencia se actualiza vibefast.app template?

vibefast.app template sigue un modelo de mejora continua. Todas las actualizaciones se publican en el repositorio privado y están incluidas en tu compra sin costo adicional.

### Quiero entender Vibe Coding antes de comprar. ¿Por dónde empiezo?

Empieza aquí:

- [¿Qué es Vibe Coding?](./es/01-what-is-vibecoding-es.md) — conceptos clave y terminología
- [¿Por qué Cloudflare es la mejor opción para Vibe Coding?](./es/05-the-best-way-to-vibecoding-on-cloudflare-es.md) — introducción práctica

### Acabo de comprarlo. ¿Qué es lo primero que debo hacer?

Sigue la [Guía de Inicio Rápido](./quickstart-es.md) — irás de clonar a tener una app en vivo en menos de 10 minutos.

-----

¿Preguntas generales? Abre un issue en [GitHub](https://github.com/vibefast-app/vibefast-docs/issues).  
¿Problemas técnicos después de la compra? Abre un issue en el repositorio privado o envía un email a [hello@dankoai.com](mailto:hello@dankoai.com).  
O contacta directamente a [@dankopeng](https://x.com/dankopeng).
