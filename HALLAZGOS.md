# Hallazgos

Aquí van **las tres líneas** del ejercicio, una por cada punto de abajo. Es lo único que hay que
traer hecho: un cambio de motor a medias con estas tres líneas escritas vale más que lo contrario,
porque lo que se discute en el directo es dónde te chocaste.

Escribe **una sola línea por punto**, con tus palabras y con lo que mediste, no con lo que suponías.

## 1. Las filas que cambian y la rama

Cuántas filas cambian de valor en tu cambio de esquema, medido con una consulta, y en qué rama del
árbol de reversibilidad cae. Si tu migración no toca datos, dilo tal cual: también es una respuesta.

- 0 filas cambian de valor: ninguna migración se ha tocado y el cambio de motor no mueve datos (medido con `select count(*)` en users/tasks/auth_access_tokens y `tasks where due_date is not null`, en la SQLite de partida y en la PostgreSQL nueva: 0 en todas), así que cae en la rama de «no toca datos» → reversible sin pérdida (volver a SQLite y `docker compose down -v`).

## 2. Lo que la batería de pruebas no podía ver

Una cosa que la batería de pruebas no podía ver. Si no encontraste ninguna, escribe qué buscaste y dónde.

- `isOverdue` deja de funcionar al leer de PostgreSQL: el driver `pg` devuelve la columna `date` como objeto `Date` aunque `schema.ts` siga declarando `dueDate: string`, así que `this.dueDate < referenceDay` compara `Date` con texto y siempre da `false` (una tarea con vencimiento 2026-08-01, mirada desde 2026-08-20, devuelve `isOverdue: false` en el GET y `dueDate: "2026-08-01T00:00:00.000Z"`; en SQLite da `true` y `"2026-08-01"`); la batería no lo ve porque ningún test toca `dueDate`/`isOverdue`, y el typecheck sigue en verde porque el tipo lo fija `schema_rules.ts`, no la base.

## 3. Tu duda

De qué dudaste, o qué no pudiste comprobar.

- Dudé de qué esperaba el enunciado del diff de `schema.ts`: en mi caso sale byte a byte idéntico (la regla de `schema_rules.ts` fuerza `string` sea cual sea el motor), así que el cambio no está en las declaraciones sino en lo que llega en ejecución; tampoco comprobé cómo se comporta el `Date` con un `TZ` distinto de UTC, donde la fecha podría desplazarse un día.
