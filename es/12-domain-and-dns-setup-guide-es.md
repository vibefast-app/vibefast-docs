# ¿Qué es un dominio y DNS? Qué hacer después de comprar un dominio

[English](../en/12-domain-and-dns-setup-guide-en.md) · [繁中](../zh/12-domain-and-dns-setup-guide-zh.md) · [Español](../es/12-domain-and-dns-setup-guide-es.md) · [日本語](../jp/12-domain-and-dns-setup-guide-jp.md) · [Português (BR)](../pt-br/12-domain-and-dns-setup-guide-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~7 minutos

-----

## Compraste un dominio. ¿Y ahora qué?

Mucha gente compra un dominio y luego no sabe qué hacer.

El dominio está listo, la app está desplegada, pero los dos no están conectados — visitar el dominio muestra una página en blanco o "sitio no encontrado."

Este tutorial explica la relación entre dominios y DNS, y recorre exactamente cómo apuntar tu dominio a Cloudflare Workers.

-----

## ¿Qué es un dominio?

Un **dominio** es la dirección de tu app — algo como `vibefast.app`.

Tu app en realidad corre en una dirección IP, como `104.21.45.123`. Pero nadie quiere memorizar números, así que existen los dominios — un nombre memorable que mapea a una dirección numérica.

Los dominios se compran en registradores de dominios (Namecheap, GoDaddy y Cloudflare Registrar todos funcionan). `.app`, `.com`, `.io` son diferentes dominios de nivel superior (TLDs) con precios y casos de uso ligeramente diferentes.

-----

## ¿Qué es DNS?

**DNS (Domain Name System)** es el sistema que traduce nombres de dominio en direcciones IP.

Piensa en él como una guía telefónica:

```
vibefast.app → 104.21.45.123
google.com → 142.250.80.46
```

Cuando alguien escribe `vibefast.app` en un navegador, esto es lo que pasa:

```
1. El navegador pregunta a DNS: ¿cuál es la dirección IP de vibefast.app?
2. DNS la busca y responde: 104.21.45.123
3. El navegador se conecta a esa IP y carga el sitio
```

Todo el proceso ocurre en milisegundos — nunca lo notas.

-----

## Tipos de registros DNS

Al configurar DNS, verás varios tipos de registro:

|Tipo de registro|Propósito                         |Ejemplo                                        |
|----------------|----------------------------------|-----------------------------------------------|
|`A`             |Apunta un dominio a una dirección IP|`vibefast.app → 104.21.45.123`              |
|`CNAME`         |Apunta un dominio a otro dominio  |`www.vibefast.app → vibefast.app`              |
|`MX`            |Configuración de servidor de correo|Enruta email a `@vibefast.app`                |
|`TXT`           |Propósitos de verificación        |Demuestra propiedad del dominio a Google, Stripe, etc.|

-----

## Conectar tu dominio a Cloudflare Workers

La configuración tiene dos partes: **primero, mover la gestión DNS de tu dominio a Cloudflare; segundo, vincular tu dominio personalizado dentro de Workers.**

### Paso 1: Mover la gestión DNS a Cloudflare (recomendado)

Si compraste tu dominio a través de Cloudflare Registrar, el DNS ya está gestionado por Cloudflare — salta al Paso 2.

Si tu dominio está registrado en otro lugar (Namecheap, GoDaddy, etc.):

1. Inicia sesión en [Cloudflare Dashboard](https://dash.cloudflare.com) y haz clic en "Add a domain"
1. Introduce tu nombre de dominio y selecciona el plan gratuito
1. Cloudflare escanea automáticamente e importa tus registros DNS existentes — verifica que no falte nada
1. Cloudflare te da dos direcciones de Nameserver, por ejemplo:
   
   ```
   aria.ns.cloudflare.com
   bob.ns.cloudflare.com
   ```
1. Ve a tu registrador de dominio y reemplaza los Nameservers actuales con estos dos
1. Espera la propagación — normalmente unos minutos a unas horas, máximo 48 horas

Una vez que toma efecto, toda tu configuración DNS vive dentro de Cloudflare junto con tus Workers, D1 y R2.

### Paso 2: Configurar un dominio personalizado en Workers

Después de que DNS está gestionado por Cloudflare, ve a la configuración de tu Worker para vincular el dominio:

1. Inicia sesión en [Cloudflare Dashboard](https://dash.cloudflare.com)
1. Haz clic en "Workers & Pages" en la barra lateral izquierda
1. Haz clic en tu **Worker de frontend** (en VibeFast, este es el worker web, no el worker de API)
1. Ve a "Settings" → "Domains & Routes"
1. Haz clic en "Add" → "Custom Domain"
1. Selecciona tu dominio del desplegable — listará dominios ya registrados en tu cuenta de Cloudflare
1. Haz clic en "Add Custom Domain"

Nota: tu dominio debe estar ya en tu cuenta de Cloudflare (es decir, el Paso 1 está completo) antes de que aparezca en el desplegable.

Cloudflare automáticamente:

- Añade el registro DNS correspondiente
- Solicita y configura un certificado SSL
- Enruta el tráfico de ese dominio a tu Worker

Unos minutos después, abre un navegador, escribe tu dominio, y tu app carga.

-----

## Cómo verificar la configuración

```bash
# Verifica los registros DNS de tu dominio
nslookup vibefast.app

# O usa dig
dig vibefast.app

# Confirma el certificado HTTPS
curl -I https://vibefast.app
```

Si ves la IP correcta o CNAME en la salida, el DNS se ha propagado exitosamente.

-----

## Preguntas frecuentes

**P: ¿Cuánto tarda la propagación DNS?**

En teoría hasta 48 horas, pero en la práctica normalmente unos minutos a unas horas. Si han pasado más de 2 horas sin cambios, verifica que todos los registros estén rellenados correctamente.

**P: ¿Necesito configurar `www.vibefast.app` y `vibefast.app` por separado?**

Sí. Necesitas configurar cada uno por separado:

- `vibefast.app` (el dominio raíz/apex)
- `www.vibefast.app` (el subdominio www)

Típicamente apuntarías ambos a Workers, o apuntarías `www` al dominio apex con un CNAME, y configurarías una redirección para que los usuarios siempre lleguen a una versión canónica.

**P: ¿Necesito obtener un certificado SSL yo mismo?**

No. Una vez que tu dominio apunta a Cloudflare, los certificados SSL se provisionan y renuevan automáticamente. Tu app soporta HTTPS automáticamente — nada que configurar.

-----

## Resumen

Un dominio es la dirección de tu app. DNS es el sistema que traduce esa dirección a una IP. El flujo de configuración:

1. Compra un dominio
1. Mueve la gestión DNS a Cloudflare (recomendado)
1. Vincula el dominio personalizado en Cloudflare Workers → "Settings" → "Domains & Routes"
1. Cloudflare gestiona HTTPS automáticamente

Todo el proceso normalmente toma menos de 30 minutos.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Volver al índice del curso](../README-es.md)

¿Listo para empezar con una plantilla full-stack completa de Cloudflare?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Precio early bird $99, sube a $199 el 1 de junio de 2026.
