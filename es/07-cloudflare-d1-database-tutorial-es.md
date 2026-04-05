# Fundamentos de la base de datos D1: Usa IA para diseñar tu primera tabla

[English](../en/07-cloudflare-d1-database-tutorial-en.md) · [繁中](../zh/07-cloudflare-d1-database-tutorial-zh.md) · [Español](../es/07-cloudflare-d1-database-tutorial-es.md) · [日本語](../jp/07-cloudflare-d1-database-tutorial-jp.md) · [Português (BR)](../pt-br/07-cloudflare-d1-database-tutorial-pt-br.md)

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Actualizado:** Marzo 2026  
**Tiempo de lectura:** ~8 minutos

-----

## Tu app necesita recordar cosas

Los usuarios se registran — necesitas almacenar sus cuentas. Publican un artículo — necesitas guardarlo. Hacen un pedido — necesitas registrarlo.

**Una base de datos es la memoria a largo plazo de tu app.**

Cloudflare D1 es el servicio de base de datos de Cloudflare — técnicamente SQLite, corriendo en la red edge de Cloudflare en el mismo lugar que tus Workers. Las lecturas y escrituras son extremadamente rápidas.

-----

## ¿Qué es una tabla?

Los datos en una base de datos se organizan en tablas — piensa en ellas como hojas de cálculo:

**Tabla users:**

|id|email            |name |created_at|
|--|-----------------|-----|----------|
|1 |danko@example.com|Danko|2026-03-01|
|2 |user2@example.com|Alice|2026-03-02|

Cada columna es un campo. Cada fila es un registro.

-----

## Usa IA para diseñar una tabla

Este es uno de los escenarios más prácticos de Vibe Coding. No necesitas conocer la sintaxis SQL — solo dile a la IA lo que necesitas:

> "Necesito una tabla para almacenar artículos de blog. Debe tener título, contenido, autor, estado de publicación y marca de tiempo de creación. Usa la sintaxis SQLite de Cloudflare D1."

La IA genera algo como:

```sql
CREATE TABLE posts (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  title TEXT NOT NULL,
  slug TEXT NOT NULL UNIQUE,
  content TEXT,
  author_id INTEGER NOT NULL,
  status TEXT NOT NULL DEFAULT 'draft',
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  updated_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (author_id) REFERENCES users(id)
);
```

No necesitas entender completamente cada línea, pero conocer los conceptos básicos te ayuda a evaluar la salida de la IA.

-----

## Conceptos clave explicados

**PRIMARY KEY**: El identificador único de cada registro. Como un número de DNI — no hay dos registros con el mismo valor.

**NOT NULL**: Este campo no puede estar vacío. El título de un artículo no puede estar en blanco, así que añadimos NOT NULL.

**DEFAULT**: El valor usado cuando no se proporciona ninguno. `status` por defecto es `'draft'`, `created_at` por defecto es la hora actual.

**FOREIGN KEY**: Enlace a otra tabla. `author_id` referencia el `id` en la tabla `users` — registra "quién escribió este artículo."

-----

## Consultar D1 en Cloudflare Workers

Después de crear tus tablas, así trabajas con datos en Workers:

```typescript
// Insertar un nuevo producto
await env.DB.prepare(`
  INSERT INTO products (name, price, stock)
  VALUES (?, ?, ?)
`).bind(name, price, stock).run()

// Leer todos los productos en stock
const products = await env.DB.prepare(`
  SELECT * FROM products
  WHERE stock > 0
  ORDER BY created_at DESC
  LIMIT 10
`).all()
```

No necesitas escribir esto tú mismo — dile a la IA "consulta todos los productos con stock mayor que cero, ordenados por fecha de creación descendente" y genera el código correcto.

-----

## Prompts comunes de IA para operaciones de base de datos

Cosas que puedes decirle directamente a la IA:

**Leer:**

> "Obtén los 10 artículos publicados más recientes de la tabla posts, incluyendo el nombre del autor"

**Insertar:**

> "Añade un nuevo registro de pedido a la tabla orders con los campos user_id, amount y status"

**Actualizar:**

> "Cambia el estado del post con id 5 a publicado"

**Eliminar:**

> "Elimina todos los borradores del user_id 123"

Siempre usa consultas parametrizadas en el código generado (marcadores `?`) para prevenir SQL Injection — se cubre en el tutorial de seguridad.

-----

## Cheatsheet de SQL

No necesitas memorizar la sintaxis SQL — pega a la IA cualquier cosa que no entiendas. Pero estos patrones aparecen constantemente:

### Leer (SELECT)

```sql
-- Obtener todos los productos
SELECT * FROM products

-- Obtener solo columnas específicas
SELECT id, name, price FROM products

-- Filtrar con una condición
SELECT * FROM products WHERE stock > 0

-- Ordenar resultados
SELECT * FROM products ORDER BY created_at DESC

-- Limitar resultados
SELECT * FROM products LIMIT 10

-- Paginación
SELECT * FROM products LIMIT 10 OFFSET 20

-- Múltiples condiciones
SELECT * FROM products WHERE stock > 0 AND price < 500
```

### Insertar (INSERT)

```sql
INSERT INTO products (name, price, stock)
VALUES ('Jabón de lavanda', 280, 50)
```

### Actualizar (UPDATE)

```sql
-- Actualizar un registro específico (siempre usa WHERE — sin él actualizas todas las filas)
UPDATE products SET stock = 45 WHERE id = 1

-- Actualizar múltiples campos a la vez
UPDATE orders SET status = 'shipped', updated_at = CURRENT_TIMESTAMP WHERE id = 5
```

### Eliminar (DELETE)

```sql
-- Eliminar un registro específico (siempre usa WHERE)
DELETE FROM products WHERE id = 1
```

### Unir dos tablas (JOIN)

```sql
-- Obtener pedidos con el email del cliente
SELECT orders.id, orders.amount, users.email
FROM orders
JOIN users ON orders.user_id = users.id
WHERE orders.status = 'paid'
```

**Siempre usa consultas parametrizadas en Cloudflare Workers** (previene SQL Injection):

```typescript
// Usa marcadores ? — nunca concatenes strings directamente
const product = await env.DB.prepare(
  'SELECT * FROM products WHERE id = ?'
).bind(productId).first()

const products = await env.DB.prepare(
  'SELECT * FROM products WHERE stock > ? ORDER BY created_at DESC LIMIT ?'
).bind(0, 10).all()
```

-----

## Límites de D1

**Nivel gratuito:**

- Almacenamiento: 5GB
- Lecturas diarias: 100.000
- Escrituras diarias: 50.000

Más que suficiente para la mayoría de apps que recién empiezan.

**Cuándo D1 no es la opción correcta:**

- Escrituras distribuidas geográficamente a alto volumen (D1 tiene un único nodo primario de escritura)
- Muy alto throughput de escritura concurrente (miles de escrituras por segundo)
- Búsqueda de texto completo compleja (la búsqueda de texto completo de SQLite es limitada)

Para el 90% de apps independientes, D1 es más que suficiente.

-----

## Resumen

D1 es la memoria a largo plazo de tu app — donde almacenas usuarios, artículos, pedidos y cualquier otro dato estructurado.

El flujo de trabajo práctico del Vibe Coder:

1. Dile a la IA en lenguaje natural qué tablas necesitas
1. La IA genera el SQL — revísalo
1. Ejecútalo para crear las tablas
1. Usa `env.DB.prepare()` para consultas, con la IA escribiendo el código de consulta

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Volver al índice del curso](../README-es.md)

¿Listo para empezar con una plantilla full-stack completa de Cloudflare?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Precio early bird $99, sube a $199 el 1 de junio de 2026.
