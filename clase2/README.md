# Lab: Node.js + Express con Dockerfile Multi-Stage
## Clase 2
### 1. Descripción de la Aplicación

API REST simple con Express que expone:
- `GET /` - Mensaje de bienvenida con timestamp
- `GET /health` - Estado de salud de la aplicación
- `GET /api/users` - Lista de usuarios (dummy data)
- `GET /api/users/:id` - Usuario específico por ID

---

### 2. Dockerfile

```bash
# Stage 1: Build
# Usar imagen base ligera (alpine)
FROM node:18-alpine AS build

# Definir directorio de trabajo
WORKDIR /app

# Copiar archivos de dependencias
COPY package*.json ./

# Instalar todas las dependencias
RUN npm install

# Copiar el resto del código fuente
COPY . .

# Stage 2: Production
# Usar imagen base ligera (stage producción)
FROM node:18-alpine
# Crear usuario no-root para seguridad
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001
# Definir directorio de trabajo
WORKDIR /app

# Copiar archivos de dependencias
COPY package*.json ./

# Instalar solo dependencias de producción
RUN npm i --only=production && \
    npm cache clean --force

# Copiar archivo principal desde el stage de build
COPY --from=build /app/app.js ./

# Asignar permisos al usuario no-root
RUN chown -R nodejs:nodejs /app

# Usar usuario no-root
USER nodejs

# Documentar puerto expuesto
EXPOSE 3000

# Definir variables de entorno
ENV NODE_ENV=production \
    PORT=3000

# Agregar metadata con labels
LABEL maintainer="sergio.salcedo@diaconia.bo"
LABEL version="1.0"
LABEL description="Aplicación Node.js con Docker multi-stage"

# Ultima actualización de docker nos permite redirigir (verificación de salud)
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node -e "require('http').get('http://localhost:3000/health', (r) => {process.exit(r.statusCode === 200 ? 0 : 1)})"

# Comando para iniciar la aplicación
CMD ["node", "app.js"]
```
| Instrucción | Descripción | Ejemplo |
|-------------|-------------|---------|
| `FROM` | Imagen base | `FROM node:18-alpine` |
| `WORKDIR` | Directorio de trabajo | `WORKDIR /app` |
| `COPY` | Copiar archivos | `COPY package.json ./` |
| `RUN` | Ejecutar comando en build | `RUN npm install` |
| `CMD` | Comando por defecto | `CMD ["node", "app.js"]` |
| `EXPOSE` | Documentar puerto | `EXPOSE 3000` |
| `ENV` | Variable de entorno | `ENV NODE_ENV=production` |
| `USER` | Usuario para ejecutar | `USER nodejs` |
| `LABEL` | Metadata | `LABEL version="1.0"` |
| `HEALTHCHECK` | Verificación de salud | `HEALTHCHECK CMD curl -f http://localhost/health` |

---

### 3. Comandos a Ejecutar

```bash
# Navegar al directorio del lab
cd D:\Curso-docker-repositorio\curso-docker-kubernetes-tareas\clase2\02-nodejs-express-multistage

# Construir la imagen con multi-stage
docker build -t mi-app:1.0 .

# Ver la imagen creada
docker images mi-app
```

### Salida Esperada

```
[+] Building 2.0s (17/17) FINISHED                                                                 docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                               0.0s
 => => transferring dockerfile: 859B                                                                               0.0s
 => [internal] load metadata for docker.io/library/node:18-alpine                                                  1.5s
 => [auth] library/node:pull token for registry-1.docker.io                                                        0.0s
 => [internal] load .dockerignore                                                                                  0.0s
 => => transferring context: 121B                                                                                  0.0s
 => [build 1/5] FROM docker.io/library/node:18-alpine@sha256:8d6421d663b4c28fd3ebc498332f249011d118945588d0a35cb9  0.1s
 => => resolve docker.io/library/node:18-alpine@sha256:8d6421d663b4c28fd3ebc498332f249011d118945588d0a35cb9bc4b8c  0.0s
 => [internal] load build context                                                                                  0.0s
 => => transferring context: 122B                                                                                  0.0s
 => CACHED [stage-1 2/7] RUN addgroup -g 1001 -S nodejs &&     adduser -S nodejs -u 1001                           0.0s
 => CACHED [stage-1 3/7] WORKDIR /app                                                                              0.0s
 => CACHED [stage-1 4/7] COPY package*.json ./                                                                     0.0s
 => CACHED [stage-1 5/7] RUN npm i --only=production &&     npm cache clean --force                                0.0s
 => CACHED [build 2/5] WORKDIR /app                                                                                0.0s
 => CACHED [build 3/5] COPY package*.json ./                                                                       0.0s
 => CACHED [build 4/5] RUN npm install                                                                             0.0s
 => CACHED [build 5/5] COPY . .                                                                                    0.0s
 => CACHED [stage-1 6/7] COPY --from=build /app/app.js ./                                                          0.0s
 => CACHED [stage-1 7/7] RUN chown -R nodejs:nodejs /app                                                           0.0s
 => exporting to image                                                                                             0.2s
 => => exporting layers                                                                                            0.0s
 => => exporting manifest sha256:f8e80e35d6ad3798be12d1f24b8ea0274e46e0303bf8b36a28b9013675c01344                  0.0s
 => => exporting config sha256:b35e11ddd8dd31ba1e9684821b836504380a2dc6032c75893bd884cf688f85b4                    0.0s
 => => exporting attestation manifest sha256:e8a3d61c6b2233171d3bdce5be2b690dc1591011b33b023da35b5ac160f97020      0.1s
 => => exporting manifest list sha256:e795682b0a505b7f9b88d055aaf3434d934268a817d3861ca9f2ec87020c3b73             0.0s
 => => naming to docker.io/library/mi-app:1.0                                                                      0.0s
 => => unpacking to docker.io/library/mi-app:1.0                                                                   0.0s
```

```
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
mi-app       1.0       e795682b0a50   29 minutes ago   192MB
```

---

### 4. Testing Local

```bash
# Visualizar imagen
docker images mi-app

# Verificar que está corriendo
docker ps

# Ver logs
docker logs mi-app
```
**Screenshot:**

![Imagen creada](screenshots/image-node.png)
![Container creado](screenshots/ps-node.png)
![Logs](screenshots/logs.png)

### 5. Publicación en Docker Hub

- Comandos de tag y push
```bash
# Tagear imagen para Docker Hub
docker tag mi-app:1.0 sergiosalcedolemuz/mi-app:1.0

# Push a Docker Hub
docker push sergiosalcedolemuz/mi-app:1.0
```
- URL pública de tu imagen en Docker Hub
```bash
https://hub.docker.com/repository/docker/sergiosalcedolemuz/mi-app
```
- Screenshot de la página en Docker Hub
![Docker-hub](screenshots/hub-creado.png)

### 6. Optimizaciones Aplicadas

- Se aplicaron multi-stages
![History](screenshots/history.png)

### 7. Conclusiones

- Al no tener dominio de ninguna de las opciones que se debe elegir, se me complicó entender lo de optimización, pero aprendí el manejo de esta parte de docker
