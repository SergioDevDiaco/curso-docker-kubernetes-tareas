# Clase 2 - Dockerización de Mi Aplicación

## Aplicación

**Lenguaje:** Node.js
**Framework:** Express
**Descripción:** API REST para gestión de tareas

**Endpoints:**
- GET / - Página de bienvenida
- GET /api/tasks - Lista de tareas
- POST /api/tasks - Crear tarea

## Dockerfile

```dockerfile
# Stage 1: Build
FROM node:18-alpine AS build
...

# Stage 2: Production
FROM node:18-alpine
...
```

**Explicación:**

| Stage | Propósito |
|-------|-----------|
| Build | Instalar todas las dependencias... |
| Production | Solo runtime... |

## Build

\`\`\`bash
docker build -t tasks-api:1.0 .
\`\`\`

**Salida:**
\`\`\`
[+] Building 32.5s ...
Successfully tagged tasks-api:1.0
\`\`\`

**Tamaño final:** 145MB

## Testing

![Docker Images](screenshots/docker-images.png)
![Container Running](screenshots/docker-ps.png)
![API Response](screenshots/curl-response.png)

## Docker Hub

**URL:** https://hub.docker.com/r/miusuario/tasks-api

![Docker Hub](screenshots/dockerhub.png)

## Optimizaciones

- Multi-stage build: redujo de 320MB a 145MB
- Usuario non-root
- .dockerignore excluye node_modules

## Conclusiones

Aprendí a optimizar imágenes...
```

---

## Recursos de Ayuda

- [Cheatsheet de la Clase 2](../cheatsheet.md)
- [Lab de Node.js Multi-Stage](../labs/nodejs-express-multistage/)
- [Guía de Entrega de Tareas](../../../ENTREGA_TAREAS.md)
- [Dockerfile Best Practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
- [Docker Hub Quick Start](https://docs.docker.com/docker-hub/)

---

## Entrega

1. Completa tu repositorio con la estructura y documentación solicitada
2. Asegúrate de que tu imagen en Docker Hub sea **pública**
3. Verifica que todos los screenshots sean claros y legibles
4. **Adjunta el enlace de tu repositorio en Moodle**

**Fecha límite:** Antes de la Clase 3 (ver fecha específica en Moodle)

**Importante:** Cualquier commit posterior a la fecha límite será descalificado.

---

## Preguntas Frecuentes

### ¿Puedo usar una aplicación que ya tenía de antes?

Sí, siempre y cuando cumpla con los requisitos (endpoints, dependencies, etc.)

### ¿Qué pasa si no tengo cuenta en Docker Hub?

Créala gratis en https://hub.docker.com/signup - es requisito del curso.

### ¿Puedo hacer build sin multi-stage?

No, el multi-stage es obligatorio para esta tarea. Es el concepto principal de la clase.

### ¿Cuántos stages mínimo?

Mínimo 2 (build + production). Puedes tener más si lo justificas.

### Mi imagen es muy grande, ¿está mal?

Depende del lenguaje. Node.js alpine ~150MB es normal. Java puede ser ~200MB. Si tienes >500MB, revisa optimizaciones.
