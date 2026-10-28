# Prompts

Todos los prompts lanzados para el ejercicio de la sesión 13 (imagen + pipeline), en orden y tal cual.

---

## Prompt 1

**Modelo:** Opus 5.5
**Herramienta:** Claude Code

```
Parte A del ejercicio: empaquetar la API (backend/, AdonisJS 7) en una imagen Docker.

Escribe backend/Dockerfile y backend/.dockerignore, y añade tres targets al Makefile de la raíz.

Dockerfile, multietapa con exactamente dos etapas:
- build: node:24 (la misma línea que exige el proyecto). Copia package.json y package-lock.json
  primero para cachear la capa de dependencias, npm ci con TODAS las dependencias, copia el resto
  y npm run build (deja la app compilada en build/).
- final: la imagen de Node 24 más pequeña razonable (alpine o slim, justifica cuál). Solo copia
  build/ desde la etapa anterior, y dentro de build/ instala las dependencias de producción con
  npm ci --omit=dev, que es como AdonisJS documenta el despliegue. Ni código fuente, ni
  devDependencies, ni ningún .env en la imagen final.
- Corre con un usuario sin privilegios (el `node` de la imagen vale), EXPOSE 3333 y
  CMD ["node", "bin/server.js"].
- Ninguna ENV con valor en el Dockerfile: la configuración entra al arrancar el contenedor.

.dockerignore: fuera node_modules, build, tmp y cualquier .env (.env, .env.*). Lo que no haga
falta para construir, también fuera.

Makefile (raíz), con estos nombres exactos y documentados con `## ...` como el resto:
- image: construye backend/ con la etiqueta flowsync-api:local.
- image-run: arranca la imagen en segundo plano con --name flowsync-api-local, publica
  IMAGE_PORT (por defecto 3334) sobre el 3333 del contenedor, --env-file backend/.env y encima
  -e HOST=0.0.0.0 -e DB_HOST=host.docker.internal -e APP_URL=http://localhost:$(IMAGE_PORT)
  (Docker no expande el ${HOST}:${PORT} del .env). Termina con curl -s a
  http://localhost:$(IMAGE_PORT)/ (dale unos segundos de margen al arranque).
- image-stop: para y borra el contenedor; que no falle si no existe.
Añádelos al .PHONY.

No ejecutes make image ni arranques el contenedor: eso lo hago yo. Sigue el estilo de comentarios
del Makefile (explicar el porqué). Commitea en la rama actual pipeline-Ivan-Cortes-Lexaris.
```

**Qué salió:**
