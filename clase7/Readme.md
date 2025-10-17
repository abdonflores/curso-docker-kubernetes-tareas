# Tarea para Casa - Clase 7
```
Nombre: Luis Abdon Flores
```
# Objetivo
Aplicar los conceptos de Namespaces, ConfigMaps, Secrets y StatefulSets desplegando PostgreSQL con persistencia en Kubernetes.

Nota importante: Esta tarea se enfoca en los conceptos de Kubernetes, NO en desarrollo de aplicaciones. Usarás imágenes pre-construidas.

# Requisitos
Desplegar PostgreSQL en Kubernetes usando:

Namespace dedicado
ConfigMap para configuración no sensible
Secret para credenciales
StatefulSet con persistencia (PVC)
Headless Service para acceso interno
Tiempo estimado: 2-3 horas

# Descripción:

## Objetivo de la tarea
Conceptos aplicados (namespace, configmap, secret, statefulset, pvc)

- **Desplegar PostgreSQL en Kubernetes aplicando los conceptos:**
- **Namespace**
- **ConfigMap**
- **Secret**
- **StatefulSet (con PVC)**
- **Headless Service**

### Instrucciones paso a paso:

``Crear namespace``

```
docker@ubuntu:~/cursoDocker/curso7$ kubectl create namespace tarea-clase7
namespace/tarea-clase7 created
```

``Aplicar ConfigMap``

```
docker@ubuntu:~/cursoDocker/curso7$ kubectl create configmap postgres-config \
>   --namespace=tarea-clase7 \
>   --from-literal=POSTGRES_DB=mibasedatos \
>   --from-literal=PGDATA=/var/lib/postgresql/data/pgdata
configmap/postgres-config created
```

``Aplicar Secret``

```
docker@ubuntu:~/cursoDocker/curso7$ kubectl create secret generic postgres-secret \
>   --namespace=tarea-clase7 \
>   --from-literal=POSTGRES_USER=admin \
>   --from-literal=POSTGRES_PASSWORD=mipassword123
secret/postgres-secret created
docker@ubuntu:~/cursoDocker/curso7$ sudo nano secret.yaml
docker@ubuntu:~/cursoDocker/curso7$ kubectl apply -f secret.yaml
Warning: resource secrets/postgres-secret is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply. kubectl apply should only be used on resources created declaratively by either kubectl create --save-config or kubectl apply. The missing annotation will be patched automatically.
secret/postgres-secret configured
docker@ubuntu:~/cursoDocker/curso7$ kubectl get secret postgres-secret
NAME              TYPE     DATA   AGE
postgres-secret   Opaque   2      43s
docker@ubuntu:~/cursoDocker/curso7$ kubectl describe secret postgres-secret
Name:         postgres-secret
Namespace:    tarea-clase7
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
POSTGRES_PASSWORD:  13 bytes
POSTGRES_USER:      5 bytes

```

``Aplicar Headless Service``

```
docker@ubuntu:~/cursoDocker/curso7$ sudo nano postgres-headless.yaml
docker@ubuntu:~/cursoDocker/curso7$ kubectl apply -f postgres-headless.yaml
Warning: spec.SessionAffinity is ignored for headless services
service/postgres-headless created
docker@ubuntu:~/cursoDocker/curso7$ kubectl get service postgres-headless
NAME                TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
postgres-headless   ClusterIP   None         <none>        5432/TCP   7s
docker@ubuntu:~/cursoDocker/curso7$ sudo nano postgres-statefulset.yaml
docker@ubuntu:~/cursoDocker/curso7$ kubectl apply -f postgres-statefulset.yaml
statefulset.apps/postgres created

```


``Aplicar StatefulSet``

```
docker@ubuntu:~/cursoDocker/curso7$ sudo nano postgres-statefulset.yaml
docker@ubuntu:~/cursoDocker/curso7$ kubectl apply -f postgres-statefulset.yaml
statefulset.apps/postgres created
docker@ubuntu:~/cursoDocker/curso7$ kubectl get statefulset postgres
NAME       READY   AGE
postgres   0/1     11s

```

``Verificar que todo está corriendo  y  Probar PostgreSQL``
```
docker@ubuntu:~/cursoDocker/curso7$ kubectl get pods -l app=postgres
NAME         READY   STATUS              RESTARTS   AGE
postgres-0   0/1     ContainerCreating   0          19s
docker@ubuntu:~/cursoDocker/curso7$ kubectl get pvc
NAME                          STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
postgres-storage-postgres-0   Bound    pvc-c319f2ec-dfe4-49af-9f76-70b620ca131b   1Gi        RWO            standard       <unset>                 27s
docker@ubuntu:~/cursoDocker/curso7$ kubectl exec -it postgres-0 -- psql -U admin -d mibasedatos
psql (15.14)
Type "help" for help.

mibasedatos=# CREATE TABLE estudiantes (
mibasedatos(#     id SERIAL PRIMARY KEY,
mibasedatos(#     nombre VARCHAR(100),
mibasedatos(#     carrera VARCHAR(100)
mibasedatos(# );
CREATE TABLE
mibasedatos=# INSERT INTO estudiantes (nombre, carrera) VALUES
mibasedatos-#     ('Juan Perez', 'Ingeniería de Sistemas'),
mibasedatos-#     ('Maria Lopez', 'Ingeniería de Sistemas'),
mibasedatos-#     ('Carlos Gomez', 'Ingeniería de Sistemas');
INSERT 0 3
mibasedatos=# SELECT * FROM estudiantes;
 id |    nombre    |        carrera
----+--------------+------------------------
  1 | Juan Perez   | Ingeniería de Sistemas
  2 | Maria Lopez  | Ingeniería de Sistemas
  3 | Carlos Gomez | Ingeniería de Sistemas
(3 rows)

mibasedatos=# \q
docker@ubuntu:~/cursoDocker/curso7$ kubectl delete pod postgres-0
pod "postgres-0" deleted from tarea-clase7 namespace
docker@ubuntu:~/cursoDocker/curso7$ kubectl get pods -w
NAME         READY   STATUS    RESTARTS   AGE
postgres-0   1/1     Running   0          7s
^Cdocker@ubuntu:~/cursoDocker/curso7$ kubectl exec -it postgres-0 -- psql -U admin -d mibasedatos -c "SELECT * FROM estudiantes;"
 id |    nombre    |        carrera
----+--------------+------------------------
  1 | Juan Perez   | Ingeniería de Sistemas
  2 | Maria Lopez  | Ingeniería de Sistemas
  3 | Carlos Gomez | Ingeniería de Sistemas
(3 rows)

```

``Demostrar persistencia``

```
docker@ubuntu:~/cursoDocker/curso7$ kubectl delete pod postgres-0
pod "postgres-0" deleted from tarea-clase7 namespace
docker@ubuntu:~/cursoDocker/curso7$ kubectl get pods -w
NAME         READY   STATUS    RESTARTS   AGE
postgres-0   1/1     Running   0          7s
^Cdocker@ubuntu:~/cursoDocker/curso7$ kubectl exec -it postgres-0 -- psql -U admin -d mibasedatos -c "SELECT * FROM estudiantes;"
 id |    nombre    |        carrera
----+--------------+------------------------
  1 | Juan Perez   | Ingeniería de Sistemas
  2 | Maria Lopez  | Ingeniería de Sistemas
  3 | Carlos Gomez | Ingeniería de Sistemas
(3 rows)
```

c) Comandos de verificación:

kubectl get all -n tarea-clase7

```
NAME             READY   STATUS    RESTARTS   AGE
pod/postgres-0   1/1     Running   0          13m

NAME                        TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
service/postgres-headless   ClusterIP   None         <none>        5432/TCP   15m

NAME                        READY   AGE
statefulset.apps/postgres   1/1     14m
```

kubectl get pvc -n tarea-clase7

```
docker@ubuntu:~/cursoDocker/curso7$ kubectl get pvc -n tarea-clase7
NAME                          STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
postgres-storage-postgres-0   Bound    pvc-c319f2ec-dfe4-49af-9f76-70b620ca131b   1Gi        RWO            standard       <unset>                 14m

```
kubectl get configmap,secret -n tarea-clase7

```
docker@ubuntu:~/cursoDocker/curso7$ kubectl get configmap,secret -n tarea-clase7
NAME                         DATA   AGE
configmap/kube-root-ca.crt   1      20m
configmap/postgres-config    2      19m

NAME                     TYPE     DATA   AGE
secret/postgres-secret   Opaque   2      18m

```

d) Capturas de pantalla:

kubectl get all mostrando todos los recursos
kubectl get pvc mostrando el volumen BOUND
Datos en PostgreSQL (SELECT)
Prueba de persistencia (después de eliminar pod)
e) Comandos de limpieza:

kubectl delete namespace tarea-clase7
# Esto elimina todo: pods, services, configmaps, secrets, pvcs
Parte 8: Limpieza (5 puntos)
8.1 Eliminar todos los recursos
kubectl delete namespace tarea-clase7
8.2 Verificar
kubectl get namespaces | grep tarea-clase7
kubectl get pvc --all-namespaces | grep tarea-clase7
 
