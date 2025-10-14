# Tarea 5 - Seguridad y Optimización de Imágenes

## 1. Título y Descripción

### Nombre del proyecto
**E-commerce Microservices Platform**

### Descripción de la aplicación
Aplicación de e-commerce básica construida con una arquitectura de microservicios que permite:
- Gestionar productos (crear y listar)
- Manejar un carrito de compras
- Usar Redis como caché para mejorar el rendimiento en la lectura de productos
- Persistir datos en MongoDB
- Exponer una API unificada mediante un API Gateway (Nginx)
- Mostrar una interfaz web simple (frontend estático)

### Objetivo de optimización
Este proyecto incluye un **análisis de línea base y una optimización de seguridad** aplicando buenas prácticas de contenedores:
- Reducción del tamaño de la imagen
- Eliminación de vulnerabilidades críticas y altas (mediante escaneo con Trivy)
- Uso de usuario no root (`appuser`)
- Multi-stage build
- Health checks
- Metadatos y `.dockerignore`

---

## 2. Tecnologías Utilizadas

| Capa | Tecnología | Versión |
|------|------------|--------|
| **Frontend** | HTML + JavaScript | - |
| **API Products** | Node.js + Express | 18 |
| **API Cart** | Node.js + Express | 18 |
| **Cache** | Redis | alpine |
| **Base de datos** | MongoDB | 4.4 |
| **API Gateway** | Nginx | alpine |
| **Orquestación** | Docker Compose | v2+ |
| **Escaneo de seguridad** | Trivy | v0.67+ |
| **Sistema operativo base** | Alpine Linux | 3.21 |

---

## 3. Análisis de Seguridad y Optimización

**Comparativa: Baseline vs Optimizado**

| Métrica | Baseline | Optimizado | Mejora |
|------|------------|--------|--------|
|Tamaño imagen|175 MB|161 MB|✅ -8%|
|Vulnerabilidades CRITICAL|0|0|—|
|Vulnerabilidades HIGH|1|1*|"⚠️ Misma| pero en contexto seguro"|
|Usuario de ejecución|root|appuser|✅ Seguridad mejorada|
|Multi-stage build|❌|✅|"✅ Menos capas| menos ataque"|
|Health check|❌|✅|✅ Monitoreo activo|
|Imagen base|node:18-alpine|node:18-alpine|✅ Ligera|
|Usuario no root|❌|✅|✅ Reducción de privilegios|

**Comandos de análisis**
```bash
# Línea base
docker build -t mi-app:baseline .
trivy image --severity CRITICAL,HIGH mi-app:baseline
docker run --rm mi-app:baseline whoami  # → root

# Optimizado
npm install  # genera package-lock.json
docker build -t mi-app:optimizado -f Dockerfile.optimizado .
trivy image --severity CRITICAL,HIGH mi-app:optimizado
docker run --rm mi-app:optimizado whoami  # → appuser


```
 **Verificación de optimización**
 ```
 docker@ubuntu:~/cursoDocker/curso4/tarea4$ docker images mi-app
REPOSITORY   TAG          IMAGE ID       CREATED              SIZE
mi-app       optimizado   d7a253ec903e   About a minute ago   161MB
mi-app       baseline     5d94d35b7db3   31 hours ago         175MB
docker@ubuntu:~/cursoDocker/curso4/tarea4$ docker run --rm mi-app:optimizado whoami
appuser
docker@ubuntu:~/cursoDocker/curso4/tarea4$ docker run -d --name test-optimizado -p 5000:5000 mi-app:optimizado
8d7a93b81b53e5d3430244162a3be1ee61ae23defe60fe4cd486473dc609fd0d
docker@ubuntu:~/cursoDocker/curso4/tarea4$ docker ps
CONTAINER ID   IMAGE                     COMMAND                  CREATED          STATUS                    PORTS                                              NAMES
8d7a93b81b53   mi-app:optimizado         "docker-entrypoint.s…"   13 seconds ago   Up 12 seconds (healthy)   0.0.0.0:5000->5000/tcp, [::]:5000->5000/tcp        test-optimizado
b23a4b5b97c4   nginx:alpine              "/docker-entrypoint.…"   26 hours ago     Up 45 minutes             0.0.0.0:8080->80/tcp, [::]:8080->80/tcp            tarea4-gateway-1
9da257f3f263   tarea4-service-products   "docker-entrypoint.s…"   26 hours ago     Up 45 minutes             0.0.0.0:32772->5000/tcp, [::]:32772->5000/tcp      tarea4-service-products-1
c365c9e7fb7f   tarea4-service-cart       "docker-entrypoint.s…"   26 hours ago     Up 45 minutes             0.0.0.0:32771->5001/tcp, [::]:32771->5001/tcp      tarea4-service-cart-1
e65106149b4d   redis:alpine              "docker-entrypoint.s…"   26 hours ago     Up 45 minutes             0.0.0.0:32769->6379/tcp, [::]:32769->6379/tcp      tarea4-redis-1
9fe61e161600   tarea4-frontend           "/docker-entrypoint.…"   26 hours ago     Up 45 minutes             0.0.0.0:32768->80/tcp, [::]:32768->80/tcp          tarea4-frontend-1
2bdc6b95a735   mongo:4.4                 "docker-entrypoint.s…"   26 hours ago     Up 45 minutes             0.0.0.0:32770->27017/tcp, [::]:32770->27017/tcp    tarea4-db-1
d4ea4ac5b847   clase3-app-docker-api     "docker-entrypoint.s…"   6 days ago       Up 51 minutes             0.0.0.0:3000->3000/tcp, [::]:3000->3000/tcp        mi-api
2c0293c85e66   dpage/pgadmin4:latest     "/entrypoint.sh"         6 days ago       Up 51 minutes             443/tcp, 0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   mi-pgadmin
6bf9d823b6c1   postgres:14-alpine        "docker-entrypoint.s…"   6 days ago       Up 51 minutes             0.0.0.0:5432->5432/tcp, [::]:5432->5432/tcp        mi-postgres
docker@ubuntu:~/cursoDocker/curso4/tarea4$ curl http://localhost:5000/health
{"status":"ok","service":"products"}docker@ubuntu:~/cursoDocker/curso4/tarea4$
docker@ubuntu:~/cursoDocker/curso4/tarea4$ docker stop test-optimizado
test-optimizado
docker@ubuntu:~/cursoDocker/curso4/tarea4$
 ```
 

## 4. Funcionalidad Verificada

 
- **Imagen optimizada: non-root, multi-stage, más segura
- **Escaneo de vulnerabilidades con Trivy
