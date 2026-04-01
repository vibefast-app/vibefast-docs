# Cómo probar tu app: Deja que la IA genere comandos curl para cada funcionalidad

[English](../en/21-ai-testing-guide-en.md) · [繁中](../zh/21-ai-testing-guide-zh.md) · [Español](../es/21-ai-testing-guide-es.md) · [日本語](../jp/21-ai-testing-guide-jp.md) · [Português (BR)](../pt-br/21-ai-testing-guide-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~7 minutos

-----

## Después de desplegar, ¿cómo sabes que realmente funciona?

Mucha gente despliega, abre un navegador, hace clic por ahí, y asume que todo está bien porque nada se rompió visiblemente.

Pero el navegador solo prueba lo que puedes ver. ¿Tu API está devolviendo los datos correctos? ¿El frontend realmente devuelve un 200? ¿Un login fallido devuelve el código de error correcto? Nada de eso es visible haciendo clic.

Mi hábito: **cada vez que termino una funcionalidad, la pruebo con curl inmediatamente.** Ya sea desarrollo local o un despliegue en producción, curl funciona en ambos entornos y los resultados son inmediatos.

-----

## ¿Qué es curl?

curl es una herramienta de línea de comandos para enviar peticiones HTTP. Viene preinstalada en Mac y Linux, y también está disponible en Windows.

Sin paquetes npm que instalar. Sin código que escribir. Una línea en la terminal prueba cualquier URL — páginas frontend y APIs backend por igual.

**Y no necesitas memorizar la sintaxis de curl. Solo pídele a la IA que genere los comandos.**

-----

## Paso 1: Pide a la IA que genere tus comandos curl

Después de terminar cualquier funcionalidad, dile a la IA directamente:

```
Acabo de desplegar un e-commerce de jabón artesanal en https://soapco.com.

Genera comandos curl para probar estas funcionalidades:
1. La homepage devuelve exitosamente
2. API de lista de productos (GET /api/products)
3. Detalle de un producto (GET /api/products/lavender-soap)
4. API de login (POST /api/auth/login) con email: test@example.com, password: test123
5. Usando el token del login, prueba la API protegida de lista de pedidos (GET /api/orders)
6. Llama a la lista de pedidos sin token — confirma que está bloqueada (debería devolver 401)
```

La IA genera un conjunto completo de comandos curl. Cópialos y pégalos en tu terminal y ejecútalos.

**Esta es la mentalidad central para las pruebas de los Vibe Coders: tú describes qué probar, la IA escribe cómo probarlo.**

-----

## curl también funciona con páginas frontend

Mucha gente asume que curl es solo para APIs de backend. Funciona igual de bien con páginas frontend:

```
Genera comandos curl para probar estas páginas frontend:
1. La homepage https://soapco.com/ devuelve 200
2. La página de lista de productos /shop devuelve exitosamente
3. Una página que no existe /xyz devuelve 404 (no 200)
4. El dashboard /admin, cuando se accede sin iniciar sesión, redirige al login (debería devolver 302 o 401)
```

La IA genera algo como:

```bash
# Probar homepage
curl -s -o /dev/null -w "%{http_code}" https://soapco.com/
# Esperado: 200

# Probar página que no existe
curl -s -o /dev/null -w "%{http_code}" https://soapco.com/xyz
# Esperado: 404

# Probar que la página admin está bloqueada sin login
curl -s -o /dev/null -w "%{http_code}" https://soapco.com/admin
# Esperado: 302 o 401, nunca 200
```

`-o /dev/null` descarta el body HTML para que no sature tu terminal. `-w "%{http_code}"` imprime solo el código de estado — para pruebas de frontend, el código de estado normalmente es todo lo que necesitas.

-----

## Cuando necesitas ver la respuesta completa

A veces necesitas ver el body completo de la respuesta de la API. Dile a la IA:

```
Genera un comando curl para llamar a GET https://soapco.com/api/products
y mostrar la respuesta JSON formateada para lectura fácil.
```

La IA genera:

```bash
curl -s https://soapco.com/api/products | jq
```

`jq` es una herramienta de formateo JSON. Instálala en Mac con `brew install jq`, o simplemente pregúntale a la IA "cómo instalo jq en mi sistema."

-----

## Cuando necesitas ver el código de estado

¿No estás seguro de si una API está devolviendo 200, 401, o 500? Dile a la IA:

```
Genera un comando curl para llamar a este endpoint y mostrar
tanto el código de estado HTTP como el body de la respuesta:
POST https://soapco.com/api/auth/login
body: {"email": "test@example.com", "password": "wrongpassword"}
```

La IA genera:

```bash
curl -i -X POST https://soapco.com/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "test@example.com", "password": "wrongpassword"}'
```

`-i` incluye el código de estado HTTP y los headers de respuesta en la salida.

-----

## Probar en local funciona igual

No necesitas esperar al despliegue — prueba en local tan pronto como esté corriendo:

```
Genera comandos curl para probar la API de productos en local.
El servidor local corre en http://localhost:8787
```

La IA intercambia la URL a localhost. Todo lo demás es idéntico.

**Prueba en local → confirma que funciona → despliega → prueba de nuevo con la URL de producción.**

Ambos pasos deben pasar antes de que puedas darlo por terminado.

-----

## Un buen hábito: Pide a la IA que agrupe tus pruebas en un script

Una vez que tienes múltiples funcionalidades que probar, pide a la IA que las consolide:

```
Agrupa estas pruebas en un script test.sh.
Cada prueba debería imprimir "PASS" o "FAIL",
y el script debería mostrar un conteo final de pruebas pasadas y fallidas.

Pruebas:
1. La homepage devuelve 200
2. La API de lista de productos devuelve 200
3. Un producto que no existe devuelve 404
4. La API de pedidos sin token devuelve 401

URL base: https://soapco.com
```

Luego ejecútalo después de cada despliegue:

```bash
bash test.sh
```

Unos segundos para confirmar que todas tus funcionalidades principales siguen funcionando — sin necesidad de ejecutar comandos uno por uno.

-----

## Resumen

curl es la herramienta de pruebas más práctica para Vibe Coders — funciona tanto para frontend como para backend. El flujo principal son dos pasos:

1. **Describe lo que quieres probar en lenguaje natural** — páginas, APIs, casos de éxito, casos de fallo
1. **La IA genera los comandos curl, tú copias-pegas y ejecutas** — verifica si los resultados coinciden con las expectativas

Prueba después de cada funcionalidad. Prueba en local, luego prueba de nuevo después de desplegar. Cuanto antes detectes un problema, más fácil es arreglarlo.

-----

**Danko Peng**  
[@dankopeng](https://x.com/dankopeng) · [vibefast.app](https://vibefast.app)

-----

👉 [Volver al índice del curso](../README-es.md)

¿Listo para empezar con una plantilla full-stack completa de Cloudflare?  
👉 **[vibefast.app](https://vibefast.app)** — Precio early bird $99, sube a $199 el 1 de junio de 2026.
