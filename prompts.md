# Prompts

Aquí van **todos los prompts que lanzaste** para hacer el ejercicio, en el orden en que los
lanzaste, con el modelo y la herramienta de cada uno.

Esto no es papeleo. Lo que se revisa es **cómo pediste las cosas**, no solo lo que salió: un
resultado flojo con un prompt bueno y un resultado flojo con un prompt vago necesitan feedback
distinto, y sin este archivo no se distinguen.

## Cómo rellenarlo

- Un apartado `## Prompt N` por cada prompt.
- **Pega el prompt tal cual lo lanzaste**, dentro del bloque de código, aunque ocupe diez líneas
  y aunque tenga faltas. No lo reescribas para que quede bien: el que arreglaste mentalmente
  después no es el que lanzaste.
- Incluye también los que **no funcionaron**. Suelen ser los más útiles de leer.
- `Modelo` y `Herramienta` en todos. Si cambiaste de una a otra a mitad, se nota aquí.

---

## Prompt 1

**Modelo:** Claude Opus 5.5 (effort medium)
**Herramienta:** Claude Code

```
Cambiar el motor de base de datos del proyecto, de SQLite a PostgreSQL en Docker, con dos bases separadas: una de desarrollo y otra de pruebas. Y después averiguar qué se ha perdido por el camino, que es la parte que importa.

📋 La tarea
🅰 Parte A: cambia el motor
Pídele a tu agente que migre el proyecto de SQLite a PostgreSQL corriendo en Docker, con dos bases de datos: una de desarrollo y otra de pruebas.
Las restricciones son estas, y no son negociables, porque son las mismas con las que se resuelve en el directo. Pásaselas a tu agente:
* El fichero de Compose se llama compose.yaml y los dos servicios se llaman db y db-test.
* La imagen es pgvector/pgvector:pg17. Es la imagen oficial de PostgreSQL con la extensión de vectores ya dentro.
* Los puertos son 54410 para desarrollo y 54411 para pruebas. No el 5432: quien tenga un PostgreSQL suyo levantado se lo encontraría ocupado, y el error que vería no menciona a Docker por ningún lado.
* La base de pruebas va en memoria, sin volumen. Es efímera a propósito: una batería de pruebas que depende de lo que dejó la anterior no es una batería de pruebas.
* Los dos servicios llevan comprobación de salud, y el arranque espera a que estén sanos. La propia imagen avisa de que, la primera vez, crea la base y no acepta conexiones mientras tanto, y de que eso rompe a quien levanta varios contenedores a la vez.
* Sin la clave version: en el fichero de Compose: está obsoleta y Docker imprime un aviso.
* La batería de pruebas apunta a la otra base por su propio fichero de entorno, que el framework carga solo cuando el entorno es de pruebas.
* Y deja atajos en el Makefile para levantar las bases, pararlas, migrar las dos y correr las pruebas.
Ninguna migración existente se toca. Si tu agente propone cambiar una, párate y anótalo: es un hallazgo, y de los buenos.
🅱 Parte B: averigua qué se ha perdido
Esta es la mitad que de verdad se revisa, y se hace aunque la parte A se te haya quedado a medias.
Cuando la batería de pruebas vuelva a estar en verde, no des el trabajo por terminado. Haz estas tres cosas y escribe lo que veas:
1. Corre la comprobación de tipos ((cd backend && npm run typecheck)) y di si está en verde o en rojo. Las dos respuestas son normales, y las dos enseñan algo.
2. Mira el diff del fichero de tipos generado (backend/database/schema.ts) entre la rama de partida y lo que tienes ahora. Ninguna migración ha cambiado, y ese fichero sí. Di qué declaraciones han cambiado de tipo, y de qué a qué.
3. Busca cómo decide el proyecto si una tarea está vencida y léelo con el diff delante. Hay un comentario en ese código que explica por qué la comparación funciona. Di si ese comentario sigue siendo verdad.
Parte C: las tres líneas
El entregable no es que la migración funcione: son las tres líneas. Van en el fichero HALLAZGOS.md de la raíz. La rama de partida ya lo trae con los tres puntos puestos: escribe una línea debajo de cada uno.
1. Cuántas filas cambian de valor en tu cambio de esquema, medido con una consulta, y en qué rama del árbol de reversibilidad cae. Si tu migración no toca datos, dilo tal cual: también es una respuesta.
2. Una cosa que la batería de pruebas no podía ver. Si no encontraste ninguna, escribe qué buscaste y dónde.
3. De qué dudaste, o qué no pudiste comprobar. Esta es la que más sirve en el directo.
```

**Qué salió:** parte A a la primera (23/23 en verde sobre PostgreSQL, typecheck en verde, ninguna migración tocada); `schema.ts` salió idéntico, y el hallazgo apareció al probar en ejecución el cálculo de vencida.
