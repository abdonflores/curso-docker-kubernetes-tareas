# Tarea 4 - Microservicios con Cache y Gateway

##  Descripción
Aplicación de e-commerce básica construida con una arquitectura de microservicios. Permite gestionar productos y un carrito de compras, con caché en Redis, persistencia en MongoDB, y un API Gateway con Nginx.

### 🛠️ Tecnologías utilizadas
- **Frontend**: HTML + JavaScript (servido con Nginx)
- **Backend**: Node.js + Express
- **Cache**: Redis
- **Base de datos**: MongoDB
- **API Gateway**: Nginx
- **Orquestación**: Docker Compose

```

##   Arquitectura
```   
 Cliente (Navegador / curl)
│
▼
[ Nginx Gateway ] ← puerto 8080
├── /api/products → service-products (puerto 5000)
├── /api/cart → service-cart (puerto 5001)
└── / → frontend (puerto 80)
│
▼
┌────────────────────────┐
│     Docker Network      │ ← ecommerce-net (DNS automático)
└────────────────────────┘
│
┌───────────────┴───────────────┐
▼                               ▼
[ Redis ]                    [ MongoDB ]
 (cache)                     (persistencia)


```

##  Servicios
```
| Servicio         | Tecnología | Puerto Externo | Puerto Interno | Descripción                     |
|------------------|------------|----------------|----------------|----------------------------------|
| `gateway`        | Nginx      | 8080           | 80             | API Gateway                      |
| `service-products`| Node.js   | —              | 5000           | Gestión de productos + Redis     |
| `service-cart`   | Node.js    | —              | 5001           | Gestión del carrito              |
| `redis`          | Redis      | —              | 6379           | Caché para productos             |
| `db`             | MongoDB    | —              | 27017          | Persistencia de datos            |
| `frontend`       | Nginx      | —              | 80             | Interfaz web estática            |

> 🔹 Los puertos marcados con "—" no están expuestos al host, solo son accesibles dentro de la red Docker.

```

 docker@ubuntu:~/cursoDocker/curso4/tarea4/gateway$ docker compose ps
WARN[0000] /home/docker/cursoDocker/curso4/tarea4/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential conf                             usion
NAME                        IMAGE                     COMMAND                  SERVICE            CREATED         STATUS         PORTS
tarea4-db-1                 mongo:4.4                 "docker-entrypoint.s…"   db                 4 minutes ago   Up 4 minutes   0.0.0.0:32773->27017/tcp, [::]:32773->                             27017/tcp
tarea4-frontend-1           tarea4-frontend           "/docker-entrypoint.…"   frontend           4 minutes ago   Up 4 minutes   0.0.0.0:32774->80/tcp, [::]:32774->80/                             tcp
tarea4-gateway-1            nginx:alpine              "/docker-entrypoint.…"   gateway            4 minutes ago   Up 4 minutes   0.0.0.0:8080->80/tcp, [::]:8080->80/tc                             p
tarea4-redis-1              redis:alpine              "docker-entrypoint.s…"   redis              4 minutes ago   Up 4 minutes   0.0.0.0:32775->6379/tcp, [::]:32775->6                             379/tcp
tarea4-service-cart-1       tarea4-service-cart       "docker-entrypoint.s…"   service-cart       4 minutes ago   Up 4 minutes   0.0.0.0:32776->5001/tcp, [::]:32776->5                             001/tcp
tarea4-service-products-1   tarea4-service-products   "docker-entrypoint.s…"   service-products   4 minutes ago   Up 4 minutes   0.0.0.0:32777->5000/tcp, [::]:32777->5                             000/tcp
docker@ubuntu:~/cursoDocker/curso4/tarea4/gateway$

```
 
 
