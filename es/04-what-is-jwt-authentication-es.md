# ¿Qué es JWT? Explicación sencilla de esta tecnología que usas todos los días

[English](../en/04-what-is-jwt-authentication-en.md) · [繁中](../zh/04-what-is-jwt-authentication-zh.md) · [Español](../es/04-what-is-jwt-authentication-es.md) · [日本語](../jp/04-what-is-jwt-authentication-jp.md) · [Português (BR)](../pt-br/04-what-is-jwt-authentication-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~8 minutos

-----

## Empecemos con un escenario real

Abres una app, introduces tu nombre de usuario y contraseña para iniciar sesión.

Luego haces clic en otra página, y otra más, y el sistema siempre sabe quién eres sin pedirte que inicies sesión de nuevo.

Esto parece natural, pero hay un problema detrás:

**HTTP es un protocolo "sin estado".**

Cada petición al servidor es completamente nueva — no recuerda automáticamente "quién envió la última petición." Después de iniciar sesión, cuando haces clic en la siguiente página, ¿cómo sabe el servidor que sigues siendo tú?

Este es el problema que resuelve JWT.

-----

## ¿Qué es JWT?

**JWT (JSON Web Token)** es una forma en la que los servidores "recuerdan quién eres."

Piensa en él como un **pase digital**:

1. Inicias sesión, el servidor confirma que tus credenciales son correctas
2. El servidor te da un pase (token JWT)
3. En cada petición posterior, llevas este pase
4. El servidor ve el pase, sabe quién eres y qué permisos tienes

Todo el proceso no requiere que el servidor recuerde nada — toda la información está en el pase.

-----

## ¿Cómo se ve un JWT?

JWT es una cadena de texto muy larga, así:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjEyMywiZW1haWwiOiJ1c2VyQGV4YW1wbGUuY29tIiwicm9sZSI6InVzZXIiLCJleHAiOjE3MDk0NTEyMDB9.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

Parece complejo, pero en realidad son tres partes separadas por `.`:

```
[Header].[Payload].[Signature]
```

### Header (Encabezado)

Describe qué algoritmo usa este token para la encriptación:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### Payload (Carga útil)

Contiene los datos reales — quién eres, qué permisos tienes, cuándo expira este token:

```json
{
  "userId": 123,
  "email": "user@example.com",
  "role": "user",
  "exp": 1709451200
}
```

Esta parte **no está encriptada**, solo codificada en Base64. Cualquiera con el token puede decodificar y ver este contenido — así que nunca pongas contraseñas ni datos sensibles aquí.

### Signature (Firma)

Esta es la clave. El servidor usa una **clave secreta** que solo él conoce para firmar las dos primeras partes:

```
HMACSHA256(
  base64(header) + "." + base64(payload),
  secret_key
)
```

El propósito de la firma: **prevenir la falsificación**.

Si alguien obtiene tu token e intenta cambiar `role: "user"` a `role: "admin"` en el payload, la firma no coincidirá. El servidor verifica y sabe que el token fue manipulado, rechazándolo inmediatamente.

-----

## ¿En qué se diferencia JWT de las sesiones tradicionales?

Otro método de autenticación común es la **Sesión**:

|          |Sesión                 |JWT               |
|----------|-----------------------|------------------|
|El servidor necesita almacenar estado|✅ Sí (almacena datos de sesión)|❌ No (la info está en el token)|
|Adecuado para sistemas distribuidos|❌ Complejo (múltiples servidores deben compartir sesión)|✅ Encaja naturalmente (cada servidor puede verificar)|
|Se puede revocar el token|✅ Fácil (solo borra la sesión)|⚠️ Complejo (necesita mecanismo extra)|
|Compatible con edge computing|❌ Difícil|✅ Encaje perfecto|

**JWT es especialmente adecuado para edge computing (Cloudflare Workers)** porque Workers no tiene estado — cada petición puede ejecutarse en un nodo edge diferente, no pueden compartir memoria de sesión. JWT lleva toda la información en el token, cualquier nodo que reciba una petición puede verificarlo independientemente sin consultar una base de datos.

Por eso vibefast.app elige JWT en lugar de Sesiones.

-----

## Puntos de seguridad de JWT

### La clave secreta debe ser fuerte y confidencial

La seguridad de JWT depende completamente de la clave secreta. Si alguien obtiene tu clave secreta, puede falsificar el token de cualquier usuario, suplantar a cualquiera, incluido el admin.

La clave secreta debe leerse de variables de entorno, nunca codificarse directamente. Genera una clave secreta aleatoria fuerte con este comando:

```bash
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

Almacena el valor generado en las variables de entorno de Cloudflare Workers, nunca dejes que aparezca en ningún archivo de código.

### El token debe tener tiempo de expiración

Los pases no deberían ser válidos para siempre. El campo `exp` de JWT establece el tiempo de expiración, el servidor rechaza tokens expirados.

La práctica común es una validez del access token de 15 minutos a 7 días, ajustada según las necesidades de seguridad. Después de expirar, los usuarios necesitan iniciar sesión de nuevo.

### El token debe almacenarse de forma segura

Dónde almacenas el token importa:

|Ubicación de almacenamiento|Riesgo            |
|---------------------------|------------------|
|localStorage               |⚠️ Vulnerable a ataques XSS|
|sessionStorage             |⚠️ Lo mismo|
|Cookie HttpOnly            |✅ Más seguro, JS no puede leerlo|
|Memoria (estado de React)  |✅ Seguro pero desaparece al refrescar|

vibefast.app usa Cookie HttpOnly para almacenar tokens, actualmente la práctica segura más común.

### El payload no debe contener datos sensibles

El payload de JWT está codificado en Base64, no encriptado. Cualquiera con el token puede decodificar y ver el contenido en un segundo.

Por eso el payload solo contiene información de identidad (userId, role, email), nunca pongas contraseñas, números de tarjeta de crédito, ni ningún dato que no pueda ser público.

-----

## Un flujo JWT completo

Recorramos todo el flujo en lenguaje sencillo:

```
1. El usuario introduce nombre de usuario/contraseña → se envía al backend

2. El backend consulta la base de datos, confirma que la contraseña es correcta

3. El backend usa la clave secreta para generar un token JWT:
   payload = { userId: 123, role: "user", exp: mañana }
   token = sign(payload, secret_key)

4. Pone el token en una Cookie HttpOnly, lo devuelve al navegador

5. Cada petición posterior, el navegador incluye automáticamente la Cookie

6. El backend recibe la petición:
   - Extrae el token
   - Verifica la firma con la clave secreta
   - Confirma que no ha expirado
   - Extrae el userId del payload, sabe quién es

7. Procesa la petición, devuelve el resultado
```

Todo este flujo ya está implementado en vibefast.app, no necesitas escribirlo tú mismo.

-----

## Preguntas frecuentes

**P: ¿Qué pasa si roban el token JWT?**

Esta es la pregunta más frecuente sobre JWT. Una vez que se emite un token, es difícil revocarlo antes de que expire (a menos que mantengas una lista negra, pero eso pierde la ventaja de JWT de no tener estado).

Solución: acortar la validez del token (ej. 1 hora), combinarlo con un mecanismo de refresh token para renovación automática. vibefast.app actualmente usa una validez de 7 días, adecuada para la mayoría de web apps.

**P: ¿Es obligatorio usar JWT?**

No. Sesión + Cookie es otra solución madura, completamente viable en entornos de servidor tradicionales. Pero en entornos de edge computing de Cloudflare Workers, JWT es una elección más natural.

**P: ¿Cuál es la relación entre JWT y OAuth?**

OAuth es un framework de autorización (permitir que apps de terceros accedan a recursos en tu nombre, como "Iniciar sesión con Google"). JWT es un formato de token comúnmente usado en flujos OAuth, pero JWT puede usarse independientemente sin OAuth.

-----

## Resumen

JWT es un pase digital que permite a los sistemas HTTP sin estado identificar usuarios. Es especialmente adecuado para entornos de edge computing de Cloudflare Workers porque:

- No necesita que el servidor almacene estado de sesión
- Cualquier nodo edge puede verificar independientemente
- El mecanismo de firma previene la falsificación

Tres puntos de seguridad para usar JWT: mantener la clave secreta confidencial, establecer tiempo de expiración, no poner datos sensibles en el payload.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Volver al índice del curso](../README-es.md)

¿Listo para empezar con una plantilla full-stack completa de Cloudflare?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Precio early bird $99, sube a $199 el 1 de junio de 2026.
