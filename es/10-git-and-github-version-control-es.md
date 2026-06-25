# Git y GitHub: Fundamentos de control de versiones para Vibe Coders

[English](../en/10-git-and-github-version-control-en.md) · [繁中](../zh/10-git-and-github-version-control-zh.md) · [Español](../es/10-git-and-github-version-control-es.md) · [日本語](../jp/10-git-and-github-version-control-jp.md) · [Português (BR)](../pt-br/10-git-and-github-version-control-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~12 minutos

-----

## ¿Por qué necesitas Git?

Construiste rápidamente una funcionalidad con IA y la desplegaste.

Al día siguiente, descubres que esa funcionalidad rompió otra cosa. Quieres volver a la versión de ayer, pero has modificado docenas de archivos, no sabes qué cambió, y no recuerdas cómo era antes.

**Git resuelve este problema.**

Git es una **herramienta de control de versiones** — registra cada estado "guardado", permitiéndote volver a cualquier punto en el tiempo. Corre en tu propio ordenador, funciona sin internet.

**GitHub** es una plataforma en la nube que te permite hacer backup del historial de versiones de Git en línea. Tu código tiene respaldo en la nube, no hay miedo de perderlo al cambiar de ordenador, y otros pueden ver tu proyecto.

En resumen: **Git es la herramienta, GitHub es el almacén.**

-----

## Conceptos centrales de GitHub

### Repository (Repo)

Un **repositorio** es donde GitHub almacena todo el código y el historial de un proyecto. Cada proyecto corresponde a un repo.

Tu repo puede ser:

- **Público**: Cualquiera puede verlo, adecuado para proyectos open source o contenido educativo
- **Privado**: Solo tú y las personas invitadas pueden verlo, adecuado para código fuente de productos comerciales

El repo vibefast-docs es público, cualquiera puede ver el contenido del tutorial. El código fuente de la plantilla vibefast.app está en otro repo privado, solo los clientes que pagan pueden acceder — así es como se entregan muchas plantillas comerciales.

### Clone

**Clone** copia un repo de GitHub a tu ordenador local:

```bash
git clone https://github.com/vibefast-app/vibefast-docs.git
```

Después de clonar, tienes el código completo y todo el historial en local.

### Fork

**Fork** copia el repo de otra persona a tu propia cuenta de GitHub, permitiéndote modificar libremente tu versión sin afectar el repo original. Este es un método común de colaboración en open source.

-----

## Paso 1: Instalar Git y configurar identidad

### Verificar si Git está instalado

macOS normalmente trae Git preinstalado:

```bash
git --version
# Si muestra un número de versión, está instalado, ej.: git version 2.39.3
```

Si no, instala con Homebrew:

```bash
brew install git
```

### Configurar tu identidad

Git registra quién hizo cada commit. Dile a Git quién eres (solo necesitas hacerlo una vez):

```bash
git config --global user.name "Danko Peng"
git config --global user.email "danko@example.com"
```

El email debe coincidir con tu cuenta de GitHub para que GitHub pueda vincular los commits a tu cuenta.

### Crear cuenta de GitHub

Ve a [github.com](https://github.com) y regístrate, la cuenta gratuita es suficiente.

-----

## Paso 2: Crear un nuevo repo en GitHub

1. Inicia sesión en GitHub, haz clic en **+** arriba a la derecha → **New repository**
2. Rellena **Repository name** (ej., `my-app`, solo inglés, números, guiones)
3. Elige **Public** o **Private**
4. **No** marques "Add a README file" — porque ya tienes un proyecto local, deja que el local tenga prioridad
5. Haz clic en **Create repository**

GitHub crea un repo vacío y muestra los comandos a ejecutar a continuación — los usarás enseguida.

-----

## Paso 3: git init y conectar con GitHub

Tienes una carpeta de proyecto en local, un repo vacío en GitHub, ahora conéctalos.

### Escenario 1: El proyecto local existe, subir a GitHub

```bash
# Entra en tu carpeta de proyecto
cd my-app

# Inicializar Git (hacer que esta carpeta sea rastreada por Git)
git init

# Crear .gitignore primero, excluir archivos que no deben rastrearse (explicado abajo)
echo ".env" >> .gitignore
echo "node_modules/" >> .gitignore

# Primera vez, añadir todos los archivos y hacer commit
git add .
git commit -m "initial commit"

# Conectar al repo de GitHub (reemplaza con tu usuario y nombre de repo)
git remote add origin https://github.com/TU_USUARIO/my-app.git

# Primer push a GitHub
git push -u origin main
```

`git remote add origin` le dice al Git local: "Mi backup en la nube está en esta URL de GitHub, nómbralo origin."

`-u origin main` solo se necesita en el primer push, después solo usa `git push`.

### Escenario 2: Proyecto clonado desde GitHub

Si clonaste desde GitHub (como clonar vibefast.app), Git ya conectó origin automáticamente, solo usa:

```bash
git add .
git commit -m "my first change"
git push
```

### Confirmar que la conexión fue exitosa

```bash
git remote -v
```

La salida debería verse así:

```
origin  https://github.com/TU_USUARIO/my-app.git (fetch)
origin  https://github.com/TU_USUARIO/my-app.git (push)
```

-----

## Paso 4: Flujo de trabajo diario

Después de la configuración, el desarrollo diario son estos tres comandos:

### git add .

```bash
git add .
```

Le dice a Git: "Prepara todos los archivos que he modificado para guardar."

`.` significa "todos los cambios en el directorio actual." Para añadir solo archivos específicos:

```bash
git add apps/web/app/routes/blog.tsx
```

### git commit -m "actualizar funciones de pedido"

```bash
git commit -m "actualizar funciones de pedido"
```

La acción real de guardar. `-m` seguido de la descripción de lo que cambió.

Buenos mensajes de commit te permiten tres meses después saber inmediatamente qué se hizo:

```bash
# ✅ Claro
git commit -m "add reading time display to blog posts"
git commit -m "fix order status not updating after Stripe webhook"
git commit -m "remove unused imports in api/users.ts"

# ❌ Sin sentido
git commit -m "update"
git commit -m "fix bug"
git commit -m "asdfgh"
```

### git push

```bash
git push
```

Sincroniza los commits locales a GitHub. Tu código tiene backup en la nube.

-----

## Ver historial

```bash
git log --oneline
```

La salida se ve así:

```
a3f2c1d add email notification for new orders
9b8e4a2 fix order status not updating after webhook
3d7f1c5 add reading time to blog posts
1a2b3c4 initial commit
```

Cada línea es un commit, la cadena del inicio es el ID del commit. Para volver a una versión:

```bash
git checkout 9b8e4a2
```

-----

## Importante: Lo que se sube a GitHub tiene historial

Esto es algo que muchos no saben, y la forma más fácil de causar problemas serios.

**Eliminar un archivo y hacer push no significa que el archivo desapareció.**

Git está diseñado para preservar todo el historial. Lo que eliminaste todavía puede encontrarse en el historial de commits. Cualquiera que clone tu repo y mire commits antiguos puede ver los contenidos anteriores de ese archivo.

Y GitHub tiene programas automatizados escaneando nuevos pushes 24/7, buscando específicamente claves API filtradas. Desde el momento en que haces push, puede que ya haya sido escaneado.

### Escenario peligroso más común

Accidentalmente hiciste commit de `.env`:

```bash
git add .   # Accidentalmente incluyó .env
git commit -m "add stripe integration"
git push    # Subido a GitHub
```

Luego lo notas, inmediatamente eliminas y haces push:

```bash
git rm .env
git commit -m "remove .env"
git push
```

**Esto no funciona.**

El commit actual no tiene `.env`, pero el commit anterior sí. Cualquiera que mire el historial de commits puede ver tu clave de Stripe, secreto JWT, todos los secretos.

### Pasos correctos de remedio

**Paso 1 (el más importante): Ve inmediatamente a la plataforma correspondiente a revocar esa clave y regenerar una nueva.** Independientemente de cómo limpies el historial de git después, haz esto primero. La clave ya está filtrada, debe considerarse inválida.

**Paso 2: Eliminar del historial completamente.** Usa `git filter-repo`:

```bash
# Instalar
pip install git-filter-repo

# Eliminar .env de todo el historial de commits
git filter-repo --path .env --invert-paths

# Forzar push a GitHub (sobrescribir historial)
git push --force
```

Pero el mejor método es **nunca dejar que entre en git en primer lugar**.

-----

## Prevención: .gitignore

`.gitignore` le dice a Git qué archivos nunca rastrear:

```
# .gitignore
.env
.env.local
.env.production
node_modules/
.wrangler/
dist/
```

**Secuencia crítica: Después de `git init`, antes del primer `git add .`, crea `.gitignore` primero.**

Si haces `git add .` primero y luego creas `.gitignore`, `.env` podría ya estar rastreado, añadirlo a `.gitignore` después no ayudará. El orden importa.

El `.gitignore` de vibefast.app ya está configurado, no necesitas añadirlo manualmente. Recuerda este paso cuando crees proyectos nuevos por tu cuenta.

-----

## Echa un vistazo antes de hacer push

```bash
# Ver archivos preparados para commit
git status

# Ver cambios específicos del contenido
git diff --staged
```

Desarrolla el hábito de echar un vistazo a `git status` antes de hacer push, confirma que no se colaron archivos no deseados.

-----

## Resumen

**Flujo completo desde cero:**

```bash
# 1. Crear nuevo repo en el sitio web de GitHub

# 2. Inicializar en local
cd my-app
git init
echo ".env" >> .gitignore    # Crear .gitignore primero
echo "node_modules/" >> .gitignore
git add .
git commit -m "initial commit"

# 3. Conectar a GitHub y hacer push
git remote add origin https://github.com/TU_USUARIO/my-app.git
git push -u origin main

# 4. Diariamente después
git add .
git commit -m "describe claramente lo que cambiaste"
git push
```

**La conciencia más importante: GitHub tiene historial completo, eliminar no significa desaparecer.** Una vez que una clave API se sube, aunque se elimine inmediatamente, esa clave debe considerarse filtrada — revoca y regenera inmediatamente, sin excepciones.

La prevención siempre es más fácil que el remedio: configura `.gitignore` antes del primer commit, echa un vistazo a `git status` antes de hacer push.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Volver al índice del curso](../README-es.md)

¿Listo para empezar con una plantilla full-stack completa de Cloudflare?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Precio early bird $99, sube a $199 el 1 de agosto de 2026.
