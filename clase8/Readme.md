# Proyecto: Despliegue de Stack Kubernetes con HPA e Ingress
## Nombre: Luis Abdón Flores

## a) Descripción del proyecto
**Objetivo:**
Desplegar una aplicación de 2 capas (frontend + backend) usando Ingress para routing, health probes para monitoreo y HPA para escalado automático.
**Stack desplegado:**

| Componente | Imagen / Servicio | Probes               | Notas                                                |
| ---------- | ----------------- | -------------------- | ---------------------------------------------------- |
| Frontend   | `nginx:alpine`    | Liveness & Readiness | Servicio ClusterIP `frontend-service`                |
| Backend    | `nginx:alpine`    | Liveness & Readiness | Servicio ClusterIP `backend-service`                 |
| Ingress    | NGINX Ingress     | -                    | Path-based routing: `/` → frontend, `/api` → backend |
| HPA        | Backend           | CPU Usage 50%        | Escala entre 2 y 5 pods según carga                  |
**Conceptos aplicados:**
- **Ingress Controller:** Direcciona rutas HTTP a los servicios correctos.
- **Health Probes (liveness/readiness):** Mantienen pods sanos y listos antes de recibir tráfico.
- **Horizontal Pod Autoscaler (HPA):** Escala automáticamente los pods del backend según consumo de CPU.
## b) Instrucciones de despliegue

1. Habilitar addons en Minikube:
   ```
    minikube start
    minikube addons enable ingress
    minikube addons enable metrics-server
   ```
3. Aplicar manifests completos del stack:
   
   ``` bash
  docker@ubuntu:~/cursoDocker/curso8$  kubectl apply -f backend.yaml -n tarea-clase8
  deployment.apps/backend unchanged
  service/backend-service unchanged
  docker@ubuntu:~/cursoDocker/curso8$ kubectl apply -f frontend.yaml -n tarea-clase8
  deployment.apps/frontend unchanged
  service/frontend-service unchanged
  docker@ubuntu:~/cursoDocker/curso8$ kubectl apply -f ingress.yaml -n tarea-clase8
  ingress.networking.k8s.io/app-ingress unchanged
  docker@ubuntu:~/cursoDocker/curso8$ kubectl apply -f hpa-backend.yaml -n tarea-clase8
  horizontalpodautoscaler.autoscaling/backend-hpa unchanged
   ```
5. Verificar recursos desplegados:
  ```
 docker@ubuntu:~/cursoDocker/curso8$ kubectl get all -n tarea-clase8
NAME                           READY   STATUS    RESTARTS   AGE
pod/backend-6c7b9685f4-drrtz   1/1     Running   0          43m
pod/backend-6c7b9685f4-rqxq7   1/1     Running   0          43m
pod/frontend-9d8dc8895-qfndn   1/1     Running   0          68m

NAME                       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
service/backend-service    ClusterIP   10.109.184.177   <none>        80/TCP    69m
service/frontend-service   ClusterIP   10.97.246.217    <none>        80/TCP    68m

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/backend    2/2     2            2           43m
deployment.apps/frontend   1/1     1            1           68m

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/backend-6c7b9685f4   2         2         2       43m
replicaset.apps/frontend-9d8dc8895   1         1         1       68m

NAME                                              REFERENCE            TARGETS              MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/backend-hpa   Deployment/backend   cpu: <unknown>/50%   2         5         2          66m

   ```
7. Probar Ingress:
    ```
    docker@ubuntu:~/cursoDocker/curso8$ kubectl get ingress -n tarea-clase8
NAME          CLASS    HOSTS   ADDRESS        PORTS   AGE
app-ingress   <none>   *       192.168.49.2   80      67m
docker@ubuntu:~/cursoDocker/curso8$

   ```
9. Probar HPA con carga:
     ```
docker@ubuntu:~/cursoDocker/curso8$ kubectl get hpa -n tarea-clase8
NAME          REFERENCE            TARGETS              MINPODS   MAXPODS   REPLICAS   AGE
backend-hpa   Deployment/backend   cpu: <unknown>/50%   2         5         2          67m
docker@ubuntu:~/cursoDocker/curso8$

   ```
11. Comandos de verificación
   ```
  kubectl get all -n tarea-clase8
  kubectl get ingress -n tarea-clase8
  kubectl get hpa -n tarea-clase8

  docker@ubuntu:~/cursoDocker/curso8$ curl http://$(minikube ip)/
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
docker@ubuntu:~/cursoDocker/curso8$ curl http://$(minikube ip)/api
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
docker@ubuntu:~/cursoDocker/curso8$
 
   ```
