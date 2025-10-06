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
# Explicación de Stage.
Stage |Función |
build |Instala dependencias (desarrollo y producción) |
production |Contiene solo archivos necesarios y dependencias de prod |

