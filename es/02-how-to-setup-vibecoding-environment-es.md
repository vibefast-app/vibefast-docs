# Primeros pasos con Vibe Coding: Configura tu entorno de desarrollo desde cero

[English](../en/02-how-to-setup-vibecoding-environment-en.md) · [繁中](../zh/02-how-to-setup-vibecoding-environment-zh.md) · [Español](../es/02-how-to-setup-vibecoding-environment-es.md) · [日本語](../jp/02-how-to-setup-vibecoding-environment-jp.md) · [Português (BR)](../pt-br/02-how-to-setup-vibecoding-environment-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~10 minutos

-----

## Sigue esta guía y tu entorno estará listo

Mucha gente se atasca en esta etapa — no porque sea difícil, sino porque no sabe qué hacer ni en qué orden.

Esta guía recorre cada requisito previo, paso a paso. Al final, tendrás un entorno de desarrollo completo para Vibe Coding configurado, con VibeFast ya desplegado y funcionando en Cloudflare.

Lo que necesitas instalar: Node.js, Git, Cursor. Lo que necesitas hacer: clonar el repositorio, ejecutar `npm install`, ejecutar `npm run setup`. Eso es todo.

-----

## Paso 1: Instalar Node.js

Node.js es el entorno de ejecución que permite a tu ordenador ejecutar JavaScript. Sin él, `npm` y `npx` no funcionarán.

Ve a [nodejs.org](https://nodejs.org) y descarga la **versión LTS** (Long Term Support).

Después de instalar, abre tu terminal y verifica:

```bash
node --version
# Deberías ver algo como v22.0.0

npm --version
# Deberías ver algo como 10.0.0
```

Si ves números de versión, la instalación fue exitosa.

-----

## Una nota rápida sobre la terminal

Si no estás familiarizado con la terminal (la ventana negra), no te intimides.

**Mac:** Pulsa `Cmd + Espacio`, busca "Terminal" y ábrela.  
**Windows:** Busca "PowerShell" o instala [Windows Terminal](https://aka.ms/terminal).

La terminal es simplemente un lugar donde te comunicas con tu ordenador usando comandos de texto. Tú escribes un comando, el ordenador lo ejecuta. Aproximadamente la mitad del flujo de trabajo de Vibe Coding ocurre aquí.

-----

## ¿Qué son npm y npx? (Versión de 30 segundos)

**npm (Node Package Manager)** es la herramienta de gestión de paquetes de JavaScript. Desarrolladores de todo el mundo publican sus herramientas y librerías en el registro npm, y tú puedes descargarlas y usarlas gratis.

```bash
npm install stripe   # descarga el paquete de Stripe en tu proyecto
npm run dev          # ejecuta el script "dev" definido en package.json
```

**npx** ejecuta una herramienta directamente sin instalarla primero:

```bash
npx wrangler deploy  # ejecuta wrangler directamente, sin necesidad de npm install
```

Eso es todo lo que necesitas saber por ahora. Si te encuentras con otros comandos npm, pídele a la IA que te los explique.

-----

## Paso 2: Instalar Git

Git es una herramienta de control de versiones que te permite clonar repositorios y rastrear cambios en tu código.

**Mac:** Escribe esto en tu terminal:

```bash
git --version
```

Si Git no está instalado, macOS te pedirá automáticamente instalar Xcode Command Line Tools. Sigue las instrucciones.

**Windows:** Ve a [git-scm.com](https://git-scm.com) y descarga el instalador.

Verifica después de instalar:

```bash
git --version
# Deberías ver algo como git version 2.40.0
```

-----

## Paso 3: Crear una cuenta de Cloudflare

Ve a [cloudflare.com](https://cloudflare.com) y regístrate para obtener una cuenta gratuita.

VibeFast se despliega en Cloudflare, así que necesitarás una cuenta para completar la configuración. Los límites del plan gratuito son más que suficientes para una app nueva.

### Activar R2 (Requiere una Tarjeta de Crédito)

VibeFast usa Cloudflare R2 para almacenar archivos e imágenes. **R2 está desactivado por defecto** y debe activarse manualmente — Cloudflare te pedirá una tarjeta de crédito cuando lo hagas.

Pasos:

1. Inicia sesión en el Cloudflare Dashboard
2. Haz clic en **R2** en el menú lateral izquierdo
3. Haz clic en "Purchase R2 Plan" o "Enable R2" y sigue los pasos para ingresar tu tarjeta de crédito

**No se te cobrará de inmediato.** R2 incluye una capa gratuita mensual (10 GB de almacenamiento, 1 millón de operaciones de lectura). Solo se te cobra si superas esos límites. La tarjeta de crédito se usa para verificación de identidad y para cualquier uso que exceda la capa gratuita. Para una app nueva, no te acercarás a esos límites.

-----

## Paso 4: Clonar VibeFast

Después de comprar VibeFast, recibirás una invitación como colaborador al repositorio privado. Acepta la invitación y ejecuta:

```bash
git clone https://github.com/vibefast-app/vibefast.git my-app
cd my-app
```

`git clone` copia todo el repositorio a tu ordenador. `cd my-app` te mueve dentro de esa carpeta.

-----

## Paso 5: npm install

```bash
npm install
```

Esto lee el `package.json` del proyecto y descarga todos los paquetes necesarios en una carpeta `node_modules`.

La primera ejecución puede tardar unos minutos. Espera el mensaje de finalización.

**¿Por qué es necesario?**  
La carpeta `node_modules` es grande (cientos de MB) y no se almacena en GitHub. Cada vez que clonas un proyecto nuevo, necesitas ejecutar `npm install` para descargar los paquetes localmente.

-----

## Paso 6: npm run setup

```bash
npm run setup
```

Este es el paso más mágico de todo el proceso.

Combinado con el clone e install, ahora solo has ejecutado tres comandos:

```bash
git clone https://github.com/vibefast-app/vibefast.git my-app
cd my-app && npm install
npm run setup
```

`npm run setup` se encarga de todo automáticamente:

1. Te guía para iniciar sesión en tu cuenta de Cloudflare
1. Crea la base de datos D1 y ejecuta el SQL inicial
1. Genera automáticamente un secreto JWT y lo escribe en las variables de entorno de Cloudflare
1. Despliega simultáneamente los Workers del frontend y backend a producción

Cuando termine, tu app estará corriendo en más de 300 nodos edge de Cloudflare en todo el mundo. De clonar a despliegue global — tres comandos, unos minutos. Eso es Vibe Coding en Cloudflare.

La terminal mostrará la URL de tu app. Ábrela en un navegador para confirmar que todo está en línea.

-----

## Paso 7: Instalar Cursor

Cursor es actualmente el editor de código con IA más popular y la herramienta central para Vibe Coding.

**Cursor es software de pago a $20/mes.** Después de registrarte, obtienes una prueba gratuita de 7 días con funcionalidad completa — sin necesidad de tarjeta de crédito. Decide si suscribirte cuando termine la prueba.

Ve a [cursor.com](https://cursor.com), descarga e instala, luego regístrate con tu email para comenzar la prueba de 7 días.

Después de instalar, abre tu proyecto en Cursor:

```bash
cursor .
```

O ve a Archivo → Abrir Carpeta dentro de Cursor y selecciona tu carpeta `my-app`.

Una vez abierto, pulsa `Cmd+L` (Mac) o `Ctrl+L` (Windows) para abrir el panel de chat con IA. Prueba escribir:

```
¿Cuál es la estructura de este proyecto? ¿Puedes explicar las carpetas y archivos principales?
```

Cursor analizará todo tu proyecto y explicará qué hace cada parte.

-----

## Desarrollo local

Una vez configurado tu entorno, tu flujo de desarrollo diario es:

```bash
npm run dev
```

Esto inicia un servidor de desarrollo local. Abre `http://localhost:5173` para ver tu app corriendo localmente.

**Haz cambios en local → confirma que todo funciona → `npm run deploy` para subir a producción.**

Este es el ciclo básico de desarrollo.

-----

## Lo que ahora tienes

Después de completar estos siete pasos, tienes:

- ✅ Node.js y npm instalados
- ✅ Git instalado
- ✅ Una cuenta de Cloudflare
- ✅ VibeFast desplegado en producción y accesible desde todo el mundo
- ✅ Cursor instalado y listo para Vibe Coding

Continúa con el siguiente tutorial y empieza a construir las funcionalidades que quieras.

-----

## ¿Y si no entiendes el código?

Una vez configurado tu entorno, empezarás a ver mucho código desconocido — anotaciones de tipos en TypeScript, async/await, sintaxis JSX…

**No necesitas aprender todo esto antes de empezar.** Ese es precisamente el punto del Vibe Coding.

Cuando encuentres código que no entiendas, pregúntale a Cursor directamente:

```
Explica qué hace este código en lenguaje sencillo.
```

Cuando veas sintaxis que no tiene sentido:

```
¿Qué significa async/await? ¿Por qué se usa aquí?
```

Cuando obtengas un mensaje de error en rojo, copia el texto completo y pégalo a la IA:

```
Me sale este error: [pegar error]
Este es mi código: [pegar código]
Explica qué lo causa y cómo solucionarlo.
```

No necesitas terminar de leer todos los tutoriales antes de empezar a construir. Aprende haciendo — pregunta cuando te atasques. Esa es la forma más rápida de avanzar.

-----

**Danko Peng**  
[@dankopeng](https://x.com/dankopeng) · [vibefast.app](https://vibefast.app)

-----

👉 [Volver al índice del curso](../README-es.md)

¿Listo para empezar con una plantilla full-stack completa de Cloudflare?  
👉 **[vibefast.app](https://vibefast.app)** — Precio early bird $99, sube a $199 el 1 de junio de 2026.
