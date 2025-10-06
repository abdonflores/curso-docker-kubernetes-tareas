# 1. Descripción de la Aplicaciónss

## Lenguaje: JavaScript

## Framework: Express (Node.js)

## Endpoints:
```
  /: Retorna saludo general

  /saludo: Retorna saludo específico

  Puerto configurable vía variable de entorno PORT

```
# 2. Dockerfile

```
# ---------- STAGE 1: Build ----------
FROM node:20-alpine AS build

# vamos al directorio de trabajo
WORKDIR /app

# Copiar package.json y package-lock.json si existe
COPY package*.json ./

# Instalamos dependencias
RUN npm install

# Copiar el todo  el código
COPY . .

# ---------- STAGE 2: Production ----------
FROM node:20-alpine AS production

# Creamos usuario no root
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

# Copiar solo node_modules desde stage anterior
COPY --from=build /app/node_modules ./node_modules
COPY --from=build /app/src ./src
COPY --from=build /app/package*.json ./

# Exponemos el puerto
EXPOSE 3000

# Las variables de entorno
ENV NODE_ENV=production
ENV PORT=3000

# Etiquetas
LABEL maintainer="Tu Nombre <flores.luis.abdon@gmail.com>"
LABEL version="1.0"
LABEL description="Aplicación Node.js dockerizada con multi-stage build"

# HEALTHCHECK 
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD wget --quiet --spider http://localhost:3000 || exit 1

# Cambiamos a usuario no root
USER appuser

# Comando para ejecutar
CMD ["node", "src/index.js"]


```
## Explicación de Stage.
Stage |Función |
|--------|------|
build |Instala dependencias (desarrollo y producción) |
production |Contiene solo archivos necesarios y dependencias de prod |

## Tabla de instrucciones principales:

instrucción |Explicación |
|--------|------|
FROM		|Define imagen base|
WORKDIR		|Directorio de trabajo dentro del contenedor|
COPY		|Copia archivos/directorios al contenedor|
RUN			|Ejecuta comandos en el contenedor|
USER		|Establece usuario no root|
CMD			|Comando de inicio|
ENV			|Variables de entorno|
LABEL		|Metadata de la imagen|
EXPOSE		|Expone el puerto para el contenedor|
HEALTHCHECK	|Verifica salud del servicio|

# 3.Proceso de Build
```
docker build -t mi-app:1.0 .
```
```
D:\xxxxxx\cursos\docker\Curso2\my-app>docker build -t mi-app:1.0 .
[+] Building 30.4s (15/15) FINISHED                                                                                                                                                         docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                        0.0s
 => => transferring dockerfile: 1.23kB                                                                                                                                                                      0.0s
 => [internal] load metadata for docker.io/library/node:20-alpine                                                                                                                                           1.7s
 => [internal] load .dockerignore                                                                                                                                                                           0.0s
 => => transferring context: 96B                                                                                                                                                                            0.0s
 => [build 1/5] FROM docker.io/library/node:20-alpine@sha256:eabac870db94f7342d6c33560d6613f188bbcf4bbe1f4eb47d5e2a08e1a37722                                                                              20.5s
 => => resolve docker.io/library/node:20-alpine@sha256:eabac870db94f7342d6c33560d6613f188bbcf4bbe1f4eb47d5e2a08e1a37722                                                                                     0.0s
 => => sha256:c88300f8759af46375ccc157a0a0dbf7cdaeded52394b5ce2ce074e3b773fe82 42.75MB / 42.75MB                                                                                                           18.8s
 => => sha256:9824c27679d3b27c5e1cb00a73adb6f4f8d556994111c12db3c5d61a0c843df8 3.80MB / 3.80MB                                                                                                              3.1s
 => => sha256:0de821d16564893ff12fae9499550711d92157ed1e6705a8c7f7e63eac0a2bb9 449B / 449B                                                                                                                  1.3s
 => => sha256:fd345d7e43c58474c833bee593321ab1097dd720bebd8032e75fbf5b81b1e554 1.26MB / 1.26MB                                                                                                              2.2s
 => => extracting sha256:9824c27679d3b27c5e1cb00a73adb6f4f8d556994111c12db3c5d61a0c843df8                                                                                                                   0.3s
 => => extracting sha256:c88300f8759af46375ccc157a0a0dbf7cdaeded52394b5ce2ce074e3b773fe82                                                                                                                   1.5s
 => => extracting sha256:fd345d7e43c58474c833bee593321ab1097dd720bebd8032e75fbf5b81b1e554                                                                                                                   0.1s
 => => extracting sha256:0de821d16564893ff12fae9499550711d92157ed1e6705a8c7f7e63eac0a2bb9                                                                                                                   0.0s
 => [internal] load build context                                                                                                                                                                           0.1s
 => => transferring context: 764B                                                                                                                                                                           0.0s
 => [build 2/5] WORKDIR /app                                                                                                                                                                                0.3s
 => [production 2/6] RUN addgroup -S appgroup && adduser -S appuser -G appgroup                                                                                                                             0.8s
 => [build 3/5] COPY package*.json ./                                                                                                                                                                       0.1s
 => [build 4/5] RUN npm install                                                                                                                                                                             6.0s
 => [production 3/6] WORKDIR /app                                                                                                                                                                           0.0s
 => [build 5/5] COPY . .                                                                                                                                                                                    0.1s
 => [production 4/6] COPY --from=build /app/node_modules ./node_modules                                                                                                                                     0.3s
 => [production 5/6] COPY --from=build /app/src ./src                                                                                                                                                       0.1s
 => [production 6/6] COPY --from=build /app/package*.json ./                                                                                                                                                0.1s
 => exporting to image                                                                                                                                                                                      0.9s
 => => exporting layers                                                                                                                                                                                     0.4s
 => => exporting manifest sha256:b8b8bacc316ef0cb8ae98697dc2e1ef8554540a01357534b50a8fd5f0dfce424                                                                                                           0.0s
 => => exporting config sha256:82bb7f56cea710fdf7844cf8119cc035279da2d80a8f13a17ea8dcae96a392b7                                                                                                             0.0s
 => => exporting attestation manifest sha256:15dc1041722172495e7584429a9932ea7a0b721d9267ee319d6bec693ae1d5b9                                                                                               0.1s
 => => exporting manifest list sha256:508a9937e88b00c8e9e4610e0341b4ebe607df5c63e1257b2ba5cef2eae54dbf                                                                                                      0.0s
 => => naming to docker.io/library/mi-app:1.0                                                                                                                                                               0.0s
 => => unpacking to docker.io/library/mi-app:1.0                                                                                                                                                            0.3s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/x1hhefqyvhacbetxuawd5l8n3

D:\xxxxxx\cursos\docker\Curso2\my-app>docker images mi-app
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
mi-app       1.0       508a9937e88b   2 minutes ago   197MB
```

# 4. Testing Local
> revisar capturas de pantalla
> paso1_construir_la_imagen.png
> paso_2_revisaTamaño.png
> paso_3_Probar endpoints.png
> docker_ps.png
> docker_image.png
# 5. Publicación en Docker Hub
> publicacion.png
# 6. Optimizaciones Aplicadas

Optimización |Descripción |
|--------|------|
Multi-stage build|	Separa dependencias de dev|
Imagen base ligera (Alpine)	|Reduce el tamaño|
Usuario no root	|Mejora la seguridad|
.dockerignore	|Excluye archivos innecesarios|
HEALTHCHECK	|Verifica que el servicio responde|
