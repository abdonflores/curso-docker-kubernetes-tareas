<<<<<<< HEAD
# Proyecto Integrador - Docker & Kubernetes

Aplicación full-stack progresiva que evoluciona clase a clase, desde una API REST simple hasta un sistema completo desplegado en Kubernetes con microservicios, base de datos, cache, frontend, Ingress y HPA.

**Autor:** Alejandro Fiengo ([alefiengo.dev](https://alefiengo.dev))
**Curso:** Docker & Kubernetes - Contenedores y Orquestación en la Práctica
**Institución:** i-Quattro

---

## Inicio Rápido

Elige tu plataforma de despliegue:

### Docker Compose (Clases 2-5)
```bash
cd docker-compose/
docker compose up -d --build
```
**[Guía completa Docker Compose](docker-compose/README.md)**

### Kubernetes (Clases 6-8)
```bash
cd k8s/
# Seguir guía de despliegue según tu cluster
```
**[Guía de Despliegue Kubernetes (minikube)](k8s/DEPLOYMENT_GUIDE.md)**
**[Guía de Despliegue Kubernetes (microk8s)](k8s/DEPLOYMENT_GUIDE_MICROK8S.md)**

---

## Evolución del Proyecto

| Versión | Tag | Stack | Qué se agrega |
|---------|-----|-------|---------------|
| **v1.0** | `v1.0-clase2` | Spring Boot | REST API in-memory con Dockerfile multi-stage |
| **v1.1** | `v1.1-clase3` | + PostgreSQL | Persistencia con Spring Data JPA + Docker Compose |
| **v1.2** | `v1.2-clase4` | + Redis + Angular + Kong | Cache, frontend SPA, API Gateway |
| **v1.3** | `v1.3-clase5` | + Seguridad | Trivy scan, optimizaciones, non-root users |
| **v2.0** | `v2.0-clases6-7-8` | **Migración completa a Kubernetes** | Deployments, Services, ConfigMaps, Secrets, StatefulSet, Ingress, HPA |

---

## Arquitectura

### Docker Compose (v1.2 - v1.3)

```
Cliente → Angular :4200 → Kong :8000 → Spring Boot :8080
                                              |
                                              +-- Redis :6379
                                              +-- PostgreSQL :5432
```

**[Ver arquitectura detallada Docker Compose](ARCHITECTURE.md#arquitectura-docker-compose-v12)**

### Kubernetes (v2.0)

```
Cliente → Ingress :80 → Frontend Pods (nginx BFF)
                   |         |
                   |         +-- /api/* → API Pods (2-5 HPA)
                   |                           |
                   +-- /api/* → API Service    +-- Redis
                                               +-- PostgreSQL (StatefulSet + PVC)
```

**[Ver arquitectura detallada Kubernetes](ARCHITECTURE.md#arquitectura-kubernetes-v20)**

---

## Stack Tecnológico

### Backend
- **Spring Boot** 3.5.6 (Java 17)
- **PostgreSQL** 15 (base de datos)
- **Redis** 7 (cache)
- **Spring Data JPA** (ORM)
- **Spring Cache** (abstraction)
- **Spring Actuator** (metrics/health)

### Frontend
- **Angular** 17+
- **nginx** (servidor + BFF proxy)

### Infraestructura

#### Docker Compose
- **Kong** 3.4 (API Gateway)
- **Docker Compose** (orquestación)
- Multi-stage builds
- Non-root users

#### Kubernetes
- **Deployments** + **Services**
- **StatefulSet** (PostgreSQL con persistencia)
- **ConfigMaps** + **Secrets**
- **NGINX Ingress** (routing HTTP)
- **HPA** (Horizontal Pod Autoscaler)
- **Health Probes** (liveness, readiness, startup)
- **BFF Pattern** (nginx proxy en frontend)

---

## Endpoints de la API

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/` | Página de bienvenida |
| GET | `/api/greeting` | Mensaje de saludo |
| GET | `/api/info` | Información de la aplicación |
| GET | `/api/users` | Listar usuarios (con cache) |
| GET | `/api/users/{id}` | Obtener usuario por ID |
| POST | `/api/users` | Crear usuario |
| PUT | `/api/users/{id}` | Actualizar usuario |
| DELETE | `/api/users/{id}` | Eliminar usuario |
| GET | `/actuator/health` | Health check |
| GET | `/actuator/health/liveness` | Liveness probe (K8s) |
| GET | `/actuator/health/readiness` | Readiness probe (K8s) |

---

## Documentación

### Guías de Despliegue
- **[Docker Compose](docker-compose/README.md)** - Despliegue con Docker Compose (v1.2-v1.3)
- **[Kubernetes (minikube)](k8s/DEPLOYMENT_GUIDE.md)** - Guía paso a paso para minikube
- **[Kubernetes (microk8s)](k8s/DEPLOYMENT_GUIDE_MICROK8S.md)** - Guía paso a paso para microk8s

### Documentación Técnica
- **[ARCHITECTURE.md](ARCHITECTURE.md)** - Diagramas de arquitectura, flujos de datos y decisiones de diseño
- **[SECURITY.md](SECURITY.md)** - Buenas prácticas de seguridad y escaneo con Trivy (v1.3)

---

## Trabajar con Tags

Este proyecto usa tags de Git para cada versión del curso:

```bash
# Clonar el repositorio
git clone https://github.com/alefiengo/proyecto-integrador-docker-k8s.git
cd proyecto-integrador-docker-k8s

# Ver todas las versiones disponibles
git tag

# Checkout a una versión específica
git checkout v1.0-clase2    # Versión básica (Clase 2)
git checkout v1.1-clase3    # Con PostgreSQL (Clase 3)
git checkout v1.2-clase4    # Con Redis, Angular, Kong (Clase 4)
git checkout v1.3-clase5    # Con seguridad (Clase 5)
git checkout v2.0-clases6-7-8  # Kubernetes completo (Clases 6-8)

# Comparar cambios entre versiones
git diff v1.2-clase4 v1.3-clase5
```

---

## Verificación Rápida

### Docker Compose
```bash
# Levantar servicios
cd docker-compose/
docker compose up -d

# Verificar que todo funciona
curl http://localhost:8000/api/users  # Via Kong
curl http://localhost:4200            # Frontend

# Ver logs
docker compose logs -f app
```

### Kubernetes
```bash
# Desplegar
cd k8s/
kubectl apply -f 00-namespace/
kubectl apply -f 01-configmaps/
kubectl apply -f 02-secrets/
# ... (ver guía completa)

# Verificar
kubectl get all -n proyecto-integrador

# Port-forward para acceder
kubectl port-forward -n ingress-nginx svc/ingress-nginx-controller 8080:80

# Acceder
curl http://localhost:8080/api/users
curl http://localhost:8080/           # Frontend
```

---

## Desarrollo

### Requisitos Previos

- **Docker** Desktop o Docker Engine
- **Docker Compose** v2
- **Java** 17+
- **Maven** 3.9+
- **Node.js** 18+ (para Angular)

### Kubernetes (adicional)
- **minikube** o **microk8s** o cluster cloud
- **kubectl**
- **Helm** 3+ (opcional)

### Construir Imágenes

```bash
# Backend
docker build -t alefiengo/springboot-api:v2.0 .

# Frontend
docker build -t alefiengo/angular-frontend:v2.0 ./frontend/

# Publicar a Docker Hub (opcional)
docker login
docker push alefiengo/springboot-api:v2.0
docker push alefiengo/angular-frontend:v2.0
```

---

## 🤝 Contribuir

Este es un proyecto educativo para el curso de Docker & Kubernetes. Si encuentras errores o mejoras:

1. Haz fork del proyecto
2. Crea una rama para tu feature (`git checkout -b feature/mejora`)
3. Commit tus cambios (`git commit -m 'feat: agregar mejora'`)
4. Push a la rama (`git push origin feature/mejora`)
5. Abre un Pull Request

---

## 📝 Licencia

Este proyecto es material educativo desarrollado por Alejandro Fiengo para el curso de Docker & Kubernetes en i-Quattro.

---

## 📞 Contacto

- **Autor:** Alejandro Fiengo
- **Website:** [alefiengo.dev](https://alefiengo.dev)
- **GitHub:** [@alefiengo](https://github.com/alefiengo)
- **Curso:** Docker & Kubernetes - i-Quattro

---

## Recursos

- [Spring Boot Documentation](https://docs.spring.io/spring-boot/docs/current/reference/html/)
- [Docker Documentation](https://docs.docker.com/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Angular Documentation](https://angular.io/docs)
=======
# Tareas - Curso Docker & Kubernetes

**Autor**: Luis Abdon Flores  
**Curso**: [Docker & Kubernetes - i-Quattro](https://iquattrogroup.com/mod/url/view.php?id=1251)

---

## 📚 Índice

- [Clase 1](clase1/)  (https://iquattrogroup.com/mod/url/view.php?id=1251)
- [Clase 2](clase2/)  (https://github.com/abdonflores/curso-docker-kubernetes-tareas/tree/main/clase2)
- [Clase 3](clase3/)  (https://github.com/abdonflores/curso-docker-kubernetes-tareas/tree/main/clase3) 
- [Clase 4](clase4/) (https://github.com/abdonflores/curso-docker-kubernetes-tareas/blob/main/clase4)
- [Clase 5](clase5/) (https://github.com/abdonflores/curso-docker-kubernetes-tareas/tree/main/clase5)
- [Clase 6](clase6/)  (https://github.com/abdonflores/curso-docker-kubernetes-tareas/tree/main/clase6)
- [Clase 7](clase7/)  (https://github.com/abdonflores/curso-docker-kubernetes-tareas/tree/main/clase7)
- [Clase 8](clase8/)  (https://github.com/abdonflores/curso-docker-kubernetes-tareas/tree/main/clase8)
>>>>>>> 950be6ef60e41dc54420d0e39482a3eab7e2f5de
