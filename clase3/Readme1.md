# Aplicación Multi-Contenedor con Docker Compose

Proyecto de demostración de Docker Compose con múltiples servicios, redes y volúmenes.

## Stack Tecnológico

- **Nginx**: Servidor web (puerto 8080)
- **Node.js + Express**: API REST (puerto 3000)
- **PostgreSQL**: Base de datos (puerto 5432)
- **pgAdmin**: Interfaz gráfica para PostgreSQL (puerto 8081)

## Arquitectura

```
┌─────────────────────────────────────────┐
│          RED FRONTEND                   │
│  ┌──────────┐                           │
│  │  Nginx   │ (Puerto 8080)             │
│  └────┬─────┘                           │
└───────┼─────────────────────────────────┘
        │
┌───────┼─────────────────────────────────┐
│       │      RED BACKEND                │
│  ┌────▼─────┐    ┌────────────┐         │
│  │ Node.js  │───▶│ PostgreSQL │         │
│  │   API    │    │            │         │
│  └──────────┘    └─────┬──────┘         │
│  (Puerto 3000)         │                │
│                   ┌────▼─────┐          │
│                   │ pgAdmin  │          │
│                   └──────────┘          │
│                   (Puerto 8081)         │
└─────────────────────────────────────────┘

VOLÚMENES PERSISTENTES:
- postgres_data: Almacena datos de PostgreSQL
- pgadmin_data: Configuración de pgAdmin
```

## Instalación y Uso

### 1. Clonar o crear la estructura del proyecto

```bash
mi-app-docker/
├── docker-compose.yml
├── nginx/
│   ├── html/
│   │   └── index.html
│   └── nginx.conf
├── api/
│   ├── Dockerfile
│   ├── package.json
│   └── server.js
└── db-init/
    └── init.sql
```

### 2. Levantar los servicios

```bash
# Construir e iniciar todos los servicios
docker-compose up -d --build

# Ver logs
docker-compose logs -f

# Ver solo logs de un servicio específico
docker-compose logs -f api
```

### 3. Verifico que todo esté funcionando

```bash
# Ver servicios corriendo
docker-compose ps

docker@ubuntu:~/cursoDocker/clase3/clase3-app-docker$ docker-compose ps
WARN[0000] /home/docker/cursoDocker/clase3/clase3-app-docker/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
NAME          IMAGE                   COMMAND                  SERVICE    CREATED              STATUS              PORTS
mi-api        clase3-app-docker-api   "docker-entrypoint.s…"   api        About a minute ago   Up About a minute   0.0.0.0:3000->3000/tcp, [::]:3000->3000/tcp
mi-nginx      nginx:alpine            "/docker-entrypoint.…"   nginx      About a minute ago   Up About a minute   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp
mi-pgadmin    dpage/pgadmin4:latest   "/entrypoint.sh"         pgadmin    About a minute ago   Up About a minute   443/tcp, 0.0.0.0:8081->80/tcp, [::]:8081->80/tcp
mi-postgres   postgres:14-alpine      "docker-entrypoint.s…"   postgres   About a minute ago   Up About a minute   0.0.0.0:5432->5432/tcp, [::]:5432->5432/tcp


```

## Acceder a los servicios

| Servicio | URL | 
|----------|-----| 
| **Nginx (Frontend)** | http://localhost:8080 | 
| **API REST** | http://localhost:3000 |  
| **PostgreSQL** | localhost:5432 |  
| **pgAdmin** | http://localhost:8081 |  

## Endpoints de la API

## test de los endpoint

### EndPoint para obtener todos los usuarios
```bash
curl http://localhost:3000/api/users

`` Listamos 
docker@ubuntu:~$ curl http://localhost:3000/api/users
[{"id":3,"nombre":"Carlos López","email":"carlos@example.com","created_at":"2025-10-07T23:33:45.308Z"},{"id":2,"nombre":"María García","email":"maria@example.com","created_at":"2025-10-07T23:33:45.308Z"},{"id":1,"nombre":"Juan Pérez","email":"juan@example.com","created_at":"2025-10-07T23:33:45.308Z"}]docker@ubuntu:~$
docker@ubuntu:~$
docker@ubuntu:~$

```

### EndPoint para crear  un usuario
```bash
curl -X POST http://localhost:3000/api/users \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Test User","email":"test@example.com"}'
`` adicionamos 
docker@ubuntu:~$ curl -X POST http://localhost:3000/api/users \
>   -H "Content-Type: application/json" \
>   -d '{"nombre":"Test User","email":"test@example.com"}'
{"id":4,"nombre":"Test User","email":"test@example.com","created_at":"2025-10-08T02:28:05.892Z"}docker@ubuntu:~$


`` Revisamos que se haya registrado 
docker@ubuntu:~$ curl http://localhost:3000/api/users
[{"id":4,"nombre":"Test User","email":"test@example.com","created_at":"2025-10-08T02:28:05.892Z"},{"id":3,"nombre":"Carlos López","email":"carlos@example.com","created_at":"2025-10-07T23:33:45.308Z"},{"id":2,"nombre":"María García","email":"maria@example.com","created_at":"2025-10-07T23:33:45.308Z"},{"id":1,"nombre":"Juan Pérez","email":"juan@example.com","created_at":"2025-10-07T23:33:45.308Z"}]docker@ubuntu:~$
docker@ubuntu:~$

```

### EndPoint para Obtener usuario por ID
```bash
curl http://localhost:3000/api/users/1


docker@ubuntu:~$ curl http://localhost:3000/api/users/1
{"id":1,"nombre":"Juan Pérez","email":"juan@example.com","created_at":"2025-10-07T23:33:45.308Z"}docker@ubuntu:~$


```

### EndPoint para actualizar usuario
```bash
curl -X PUT http://localhost:3000/api/users/1 \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Updated Name","email":"updated@example.com"}'
```

### EndPoint  Eliminar usuario
```bash
curl -X DELETE http://localhost:3000/api/users/1
```

### Health Check
```bash
curl http://localhost:3000/api/health



docker@ubuntu:~$ curl http://localhost:3000/api/health
{"status":"OK","message":"API funcionando correctamente","timestamp":"2025-10-08T02:42:44.780Z"}docker@ubuntu:~$



```



## Comandos útiles  

```bash
# Detener servicios
docker-compose stop

# Iniciar servicios detenidos
docker-compose start

# Reiniciar un servicio específico
docker-compose restart api

# Detener y eliminar contenedores
docker-compose down

# Detener, eliminar contenedores Y volúmenes
docker-compose down -v

# Ver logs en tiempo real
docker-compose logs -f

# Ejecutar comandos dentro de un contenedor
docker-compose exec api sh
docker-compose exec postgres psql -U usuario -d mibasedatos

# Reconstruir un servicio específico
docker-compose build api
docker-compose up -d api

# Escalar un servicio (crear múltiples instancias)
docker-compose up -d --scale api=3
```

## Conceptos demostrados

### 1. **Redes (Networks)**
- `frontend`: Conecta Nginx con la API
- `backend`: Conecta API, PostgreSQL y pgAdmin
 

### 2. **Volúmenes (Volumes)**
- `postgres_data`: Persiste datos de la base de datos
- `pgadmin_data`: Persiste configuración de pgAdmin
 

### 3. **Orquestación**
- `depends_on`: Define orden de inicio
- `restart`: Política de reinicio automático
- Variables de entorno para configuración
- Health checks implícitos

### Revisiones
```bash
# Verificar que postgres esté corriendo
docker-compose ps postgres

docker@ubuntu:~/cursoDocker/clase3/clase3-app-docker$ docker-compose ps postgres
WARN[0000] /home/docker/cursoDocker/clase3/clase3-app-docker/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
NAME          IMAGE                COMMAND                  SERVICE    CREATED       STATUS       PORTS
mi-postgres   postgres:14-alpine   "docker-entrypoint.s…"   postgres   3 hours ago   Up 3 hours   0.0.0.0:5432->5432/tcp, [::]:5432->5432/tcp

# Ver logs de postgres
docker-compose logs postgres

mi-postgres  | The files belonging to this database system will be owned by user "postgres".
mi-postgres  | This user must also own the server process.
mi-postgres  |
mi-postgres  | The database cluster will be initialized with locale "en_US.utf8".
mi-postgres  | The default database encoding has accordingly been set to "UTF8".
mi-postgres  | The default text search configuration will be set to "english".
mi-postgres  |
mi-postgres  | Data page checksums are disabled.
mi-postgres  |
mi-postgres  | fixing permissions on existing directory /var/lib/postgresql/data ... ok
mi-postgres  | creating subdirectories ... ok
mi-postgres  | selecting dynamic shared memory implementation ... posix
mi-postgres  | selecting default max_connections ... 100
mi-postgres  | selecting default shared_buffers ... 128MB
mi-postgres  | selecting default time zone ... UTC
mi-postgres  | creating configuration files ... ok
mi-postgres  | running bootstrap script ... ok
mi-postgres  | sh: locale: not found
mi-postgres  | 2025-10-07 23:33:41.448 UTC [35] WARNING:  no usable system locales were found
mi-postgres  | performing post-bootstrap initialization ... ok
mi-postgres  | syncing data to disk ... ok
mi-postgres  |
mi-postgres  |
mi-postgres  | Success. You can now start the database server using:
mi-postgres  |
mi-postgres  |     pg_ctl -D /var/lib/postgresql/data -l logfile start
mi-postgres  |
mi-postgres  | initdb: warning: enabling "trust" authentication for local connections
mi-postgres  | You can change this by editing pg_hba.conf or using the option -A, or
mi-postgres  | --auth-local and --auth-host, the next time you run initdb.
mi-postgres  | waiting for server to start....2025-10-07 23:33:44.521 UTC [41] LOG:  starting PostgreSQL 14.19 on x86_64-pc-linux-musl, compiled by gcc (Alpine 14.2.0) 14.2.0, 64-bit
mi-postgres  | 2025-10-07 23:33:44.523 UTC [41] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
mi-postgres  | 2025-10-07 23:33:44.528 UTC [42] LOG:  database system was shut down at 2025-10-07 23:33:42 UTC
mi-postgres  | 2025-10-07 23:33:44.536 UTC [41] LOG:  database system is ready to accept connections
mi-postgres  |  done
mi-postgres  | server started
mi-postgres  | CREATE DATABASE
mi-postgres  |
mi-postgres  |
mi-postgres  | /usr/local/bin/docker-entrypoint.sh: running /docker-entrypoint-initdb.d/init.sql
mi-postgres  | CREATE TABLE
mi-postgres  | INSERT 0 3
mi-postgres  | CREATE INDEX
mi-postgres  | psql:/docker-entrypoint-initdb.d/init.sql:23: NOTICE:  Base de datos inicializada correctamente
mi-postgres  | DO
mi-postgres  |
mi-postgres  |
mi-postgres  | 2025-10-07 23:33:45.317 UTC [41] LOG:  received fast shutdown request
mi-postgres  | waiting for server to shut down....2025-10-07 23:33:45.318 UTC [41] LOG:  aborting any active transactions
mi-postgres  | 2025-10-07 23:33:45.324 UTC [41] LOG:  background worker "logical replication launcher" (PID 48) exited with exit code 1
mi-postgres  | 2025-10-07 23:33:45.325 UTC [43] LOG:  shutting down
mi-postgres  | 2025-10-07 23:33:45.381 UTC [41] LOG:  database system is shut down
mi-postgres  |  done
mi-postgres  | server stopped
mi-postgres  |
mi-postgres  | PostgreSQL init process complete; ready for start up.
mi-postgres  |
mi-postgres  | 2025-10-07 23:33:45.559 UTC [1] LOG:  starting PostgreSQL 14.19 on x86_64-pc-linux-musl, compiled by gcc (Alpine 14.2.0) 14.2.0, 64-bit
mi-postgres  | 2025-10-07 23:33:45.559 UTC [1] LOG:  listening on IPv4 address "0.0.0.0", port 5432
mi-postgres  | 2025-10-07 23:33:45.559 UTC [1] LOG:  listening on IPv6 address "::", port 5432
mi-postgres  | 2025-10-07 23:33:45.590 UTC [1] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
mi-postgres  | 2025-10-07 23:33:45.596 UTC [58] LOG:  database system was shut down at 2025-10-07 23:33:45 UTC
mi-postgres  | 2025-10-07 23:33:45.607 UTC [1] LOG:  database system is ready to accept connections


# Verificar red backend
docker network inspect mi-app-docker_backend

docker@ubuntu:~/cursoDocker/clase3/clase3-app-docker$ docker network inspect red-backend
[
    {
        "Name": "red-backend",
        "Id": "6fff9d5065762c802fe55f7a795d9bbc325ed1fb40f99aab5fa0dbbd68da81ed",
        "Created": "2025-10-07T19:48:45.021778598-07:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv4": true,
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.20.0.0/16",
                    "Gateway": "172.20.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "09b72ba1d999d687ef5ac8a1010d9ff97520a2da718c6ed5e41023dfecbef5b8": {
                "Name": "mi-pgadmin",
                "EndpointID": "b33114b7f50e4345c6fcb66f99627d2c0ed23907a880096a38d9cce78ced8d1a",
                "MacAddress": "fa:a1:f3:f0:db:b7",
                "IPv4Address": "172.20.0.4/16",
                "IPv6Address": ""
            },
            "63999fe41835ecd40dcaa2b68e862d540d51a1268ce04abe9434f80b887a7f87": {
                "Name": "mi-nginx",
                "EndpointID": "dfe54d3b6504c0c82a93433bfe822d404d42d8e9a8aaccba7d8a30eb07f16ab3",
                "MacAddress": "12:a1:bb:a4:82:58",
                "IPv4Address": "172.20.0.5/16",
                "IPv6Address": ""
            },
            "ad386ac870d38c5bf9601cb86751ecf39558cc93dc96bc1fc42b5f5349e9ec48": {
                "Name": "mi-postgres",
                "EndpointID": "8985cf4f22afd73d1724cf94842328e010a7bfaa2323dfcedf90949209165b70",
                "MacAddress": "aa:2c:45:bc:08:d4",
                "IPv4Address": "172.20.0.2/16",
                "IPv6Address": ""
            },
            "b9380c8eefd8ef397ddddd80525d2faa8b3a8ca2d60919f8ef20c880466fbb71": {
                "Name": "mi-api",
                "EndpointID": "2c285a2e6d3e87317f7989b527361bf1b9e4d0dd9a9daf3b1d5dc0f6f66d1aa9",
                "MacAddress": "c6:27:27:73:9a:ff",
                "IPv4Address": "172.20.0.3/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {
            "com.docker.compose.config-hash": "d4e63b25de7a3ed2deb2b741691f8846e79455bf0b3fab4c0e5cc5e8e19f9712",
            "com.docker.compose.network": "backend",
            "com.docker.compose.project": "clase3-app-docker",
            "com.docker.compose.version": "2.40.0"
        }
    }
]


```
### Reiniciar todo desde cero
```bash
`` docker-compose down -v

docker@ubuntu:~/cursoDocker/clase3/clase3-app-docker$ docker-compose down -v
WARN[0000] /home/docker/cursoDocker/clase3/clase3-app-docker/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
[+] Running 8/8
 ✔ Container mi-pgadmin   Removed                                                                                                                                                                              4.1s
 ✔ Container mi-nginx     Removed                                                                                                                                                                              1.0s
 ✔ Container mi-api       Removed                                                                                                                                                                             10.5s
 ✔ Container mi-postgres  Removed                                                                                                                                                                              0.5s
 ✔ Volume datos-pgadmin   Removed                                                                                                                                                                              0.0s
 ✔ Network red-frontend   Removed                                                                                                                                                                              0.5s
 ✔ Network red-backend    Removed                                                                                                                                                                              0.2s
 ✔ Volume datos-postgres  Removed  
 
 
 `` docker-compose up -d --build


WARN[0000] /home/docker/cursoDocker/clase3/clase3-app-docker/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
[+] Building 3.2s (12/12) FINISHED
 => [internal] load local bake definitions                                                                                                                                                                     0.0s
 => => reading from stdin 567B                                                                                                                                                                                 0.0s
 => [internal] load build definition from dockerfile                                                                                                                                                           0.0s
 => => transferring dockerfile: 161B                                                                                                                                                                           0.0s
 => [internal] load metadata for docker.io/library/node:16-alpine                                                                                                                                              2.9s
 => [internal] load .dockerignore                                                                                                                                                                              0.0s
 => => transferring context: 2B                                                                                                                                                                                0.0s
 => [1/5] FROM docker.io/library/node:16-alpine@sha256:a1f9d027912b58a7c75be7716c97cfbc6d3099f3a97ed84aa490be9dee20e787                                                                                        0.0s
 => [internal] load build context                                                                                                                                                                              0.0s
 => => transferring context: 124B                                                                                                                                                                              0.0s
 => CACHED [2/5] WORKDIR /app                                                                                                                                                                                  0.0s
 => CACHED [3/5] COPY package*.json ./                                                                                                                                                                         0.0s
 => CACHED [4/5] RUN npm install                                                                                                                                                                               0.0s
 => CACHED [5/5] COPY . .                                                                                                                                                                                      0.0s
 => exporting to image                                                                                                                                                                                         0.0s
 => => exporting layers                                                                                                                                                                                        0.0s
 => => writing image sha256:3586009e2f97948644475b3d544e30d5ca5c1e2a4d7b049b85862b4a095c8a3b                                                                                                                   0.0s
 => => naming to docker.io/library/clase3-app-docker-api                                                                                                                                                       0.0s
 => resolving provenance for metadata file                                                                                                                                                                     0.0s
[+] Running 9/9
 ✔ clase3-app-docker-api  Built                                                                                                                                                                                0.0s
 ✔ Network red-backend    Created                                                                                                                                                                              0.1s
 ✔ Network red-frontend   Created                                                                                                                                                                              0.1s
 ✔ Volume datos-postgres  Created                                                                                                                                                                              0.0s
 ✔ Volume datos-pgadmin   Created                                                                                                                                                                              0.0s
 ✔ Container mi-postgres  Started                                                                                                                                                                              1.7s
 ✔ Container mi-pgadmin   Started                                                                                                                                                                              3.4s
 ✔ Container mi-api       Started                                                                                                                                                                              3.2s
 ✔ Container mi-nginx     Started                                      
```

## Probado otros comandos
### docker network ls
docker@ubuntu:~/cursoDocker/clase3/clase3-app-docker/nginx/html$ docker network ls
NETWORK ID     NAME                   DRIVER    SCOPE
ee6d53429f28   bridge                 bridge    local
0a0eb0c03ec8   compose-demo_default   bridge    local
d04cc40d340c   docker_default         bridge    local
233a097e182f   host                   host      local
c58201c33ae2   none                   null      local
eadb942ef706   red-backend            bridge    local
f66eeee451a7   red-frontend           bridge    local

### docker exec
docker@ubuntu:~/cursoDocker/clase3/clase3-app-docker/nginx/html$ docker ps
CONTAINER ID   IMAGE                   COMMAND                  CREATED         STATUS         PORTS                                              NAMES
5f640fd3dd8f   nginx:alpine            "/docker-entrypoint.…"   7 minutes ago   Up 7 minutes   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp            mi-nginx
d4ea4ac5b847   clase3-app-docker-api   "docker-entrypoint.s…"   7 minutes ago   Up 7 minutes   0.0.0.0:3000->3000/tcp, [::]:3000->3000/tcp        mi-api
2c0293c85e66   dpage/pgadmin4:latest   "/entrypoint.sh"         7 minutes ago   Up 7 minutes   443/tcp, 0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   mi-pgadmin
6bf9d823b6c1   postgres:14-alpine      "docker-entrypoint.s…"   7 minutes ago   Up 7 minutes   0.0.0.0:5432->5432/tcp, [::]:5432->5432/tcp        mi-postgres
docker@ubuntu:~/cursoDocker/clase3/clase3-app-docker/nginx/html$ ^C
docker@ubuntu:~/cursoDocker/clase3/clase3-app-docker/nginx/html$ docker exec mi-api ping mi-postgres
PING mi-postgres (172.20.0.2): 56 data bytes
64 bytes from 172.20.0.2: seq=0 ttl=64 time=0.330 ms
64 bytes from 172.20.0.2: seq=1 ttl=64 time=0.153 ms
64 bytes from 172.20.0.2: seq=2 ttl=64 time=0.315 ms
64 bytes from 172.20.0.2: seq=3 ttl=64 time=0.243 ms
64 bytes from 172.20.0.2: seq=4 ttl=64 time=0.144 ms
64 bytes from 172.20.0.2: seq=5 ttl=64 time=0.149 ms
64 bytes from 172.20.0.2: seq=6 ttl=64 time=0.146 ms
64 bytes from 172.20.0.2: seq=7 ttl=64 time=0.087 ms
64 bytes from 172.20.0.2: seq=8 ttl=64 time=0.090 ms

 
## Checklist de conceptos aplicados

- [x] Múltiples servicios orquestados
- [x] Redes personalizadas (frontend y backend)
- [x] Volúmenes persistentes para datos
- [x] Volúmenes bind para desarrollo
- [x] Variables de entorno 
