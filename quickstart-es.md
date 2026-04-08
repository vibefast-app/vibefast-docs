# vibefast.app Guía de Inicio Rápido

[English](./quickstart.md) · [繁中](./quickstart-zh.md) · [日本語](./quickstart-jp.md) · [Español](./quickstart-es.md) · [Português (BR)](./quickstart-pt-br.md)

**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~5 minutos

-----

## De clonar a producción en 3 comandos

```bash
git clone https://github.com/vibefast-app/vibefast.git my-app
cd my-app && npm install
npm run setup
```

Eso es todo.

`npm run setup` es el núcleo de la experiencia de la vibefast.app template. Maneja automáticamente todo lo que de otra forma harías manualmente:

- Iniciar sesión en Cloudflare y verificar tu cuenta
- Crear una base de datos D1, ejecutar el SQL de bootstrap, construir todas las tablas automáticamente
- Generar un secreto JWT y escribirlo en el entorno de Workers
- Desplegar simultáneamente el frontend (Remix) y el backend (Workers API) a producción

Cuando el terminal termine, verás dos URLs en vivo — una para el frontend, otra para la API del backend. Tu app ya está corriendo en la red global de Cloudflare con más de 300 ubicaciones.

-----

## Requisitos

Antes de empezar, asegúrate de tener:

- **Node.js 20+**
- **npm 10+**
- **Una cuenta de Cloudflare** (el plan gratuito es suficiente)
- Usuarios de macOS: `jq` instalado (`brew install jq`)

¿No tienes cuenta de Cloudflare? [Regístrate gratis aquí](https://dash.cloudflare.com/sign-up) — no se requiere tarjeta de crédito.

-----

## ¿Quieres verlo funcionando primero?

No tienes que confiar en la descripción.

[vibefast.app](https://vibefast.app) está construido completamente con la vibefast.app template — la página de marketing, blog, página de precios, login de usuarios y live backend son todas funciones reales de esta plantilla corriendo en producción.

**Regístrate con una cuenta gratuita** y una vez que inicies sesión, podrás explorar:

- Analytics dentro del live backend
- Flujos de Blog y Media funcionando en el sitio en producción
- Vistas sample de Business y User en limited-access mode
- Tu número de registro — qué usuario eres

El flujo de autenticación que acabas de experimentar, la UI del live backend y la velocidad de la página — eso es exactamente lo que compras. No es un demo. Es real.

![vibefast.app traffic analytics dashboard](./asset/analytics_light.png)

-----

## Lo que puedes hacer en la primera hora

La vibefast.app template está diseñada con un objetivo: **los compradores deben poder ir desde la configuración hasta una app personalizada y en vivo en su primera hora.**

### 0–10 minutos: Instalar y desplegar

```bash
npm install
npm run setup
```

Cuando termine, tendrás:

- Una app web completa corriendo en Cloudflare
- Una base de datos D1 con tablas de usuarios, posts y pedidos ya creadas
- Workers de frontend y backend en producción
- Una URL que puedes abrir ahora mismo

### 10–15 minutos: Desarrollo local

```bash
npm run dev
```

Un comando inicia frontend y backend. Abre la URL local que aparece en tu terminal y verás:

- Una página de marketing completa
- Una página de precios
- Un sistema de blog
- Registro e inicio de sesión de usuarios
- Un punto de entrada al live backend

No son pantallas de placeholder. Cada función está conectada y funcionando.

![vibefast.app signup flow](./asset/signup.png)

### 15–40 minutos: Stripe, Resend y branding

Agrega tu API key de Stripe y tu API key de Resend a la configuración, ejecuta `npm run deploy`, luego:

1. Registra una cuenta con tu email de administrador configurado
1. Abre `/admin` y confirma que puedes acceder al live backend
1. Ejecuta un pago de prueba con Stripe y confirma que el webhook se activa
1. Confirma que tanto el email de confirmación de compra como la notificación al admin lleguen

Cuando el flujo de extremo a extremo funcione, tu app está lista.

El branding es directo — la vibefast.app template centraliza todo el texto que querrás cambiar en un solo archivo de configuración: nombre del sitio, dominio, texto de precios, texto de la página principal, configuración SEO. Cámbialos, ejecuta `npm run deploy`, todo se actualiza.

![vibefast.app blog editor](./asset/blogedit.png)

-----

## Referencia de comandos

|Comando                  |Qué hace                                                                  |
|-------------------------|--------------------------------------------------------------------------|
|`npm run setup`          |Configuración inicial: crea la base de datos, genera secreto, despliega Workers|
|`npm run dev`            |Inicia desarrollo local (frontend + backend simultáneamente)              |
|`npm run deploy`         |Despliega a producción (frontend + backend simultáneamente)               |
|`npm run deploy:frontend`|Despliega solo el frontend                                                |
|`npm run deploy:backend` |Despliega solo el backend                                                 |
|`npm run build`          |Construye todos los paquetes                                              |
|`npm run typecheck`      |Verificación de tipos TypeScript en todo el proyecto                      |

-----

## ¿Quieres profundizar en la arquitectura?

- [Por qué Cloudflare es la mejor opción para Vibe Coding](./es/05-the-best-way-to-vibecoding-on-cloudflare-es.md) — Comparación directa con Next.js + Vercel
- [Cloudflare Workers vs. servidores tradicionales](./es/06-cloudflare-workers-vs-traditional-server-es.md) — Los beneficios prácticos de la arquitectura edge

-----

## ¿Listo?

**Early bird $99 — el precio sube a $199 el 1 de junio de 2026.**  
Pago único. Acceso de por vida. Repositorio privado en GitHub. Todas las actualizaciones futuras incluidas.

👉 **[vibefast.app](https://vibefast.app)**
