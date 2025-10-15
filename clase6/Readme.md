# Mi Aplicación en Kubernetes

**Curso:** Docker & Kubernetes - Clase 6
**Estudiante:** Luis Abdón Flores

**Breve descripción** :
Desplegar una aplicación web en un cluster de Kubernetes para demostrar cómo funciona la orquestación de contenedores en producción

## Tecnologìa
```
 **Capa  de Aplicación**

┌─────────────────┐
│   Nginx         │  ← Servidor web (contenedor)
│   Alpine Linux  │  ← Sistema operativo minimalista
└─────────────────┘
**Plataforma de Contenerización**
 
┌─────────────────┐
│   Docker        │  ← Runtime de contenedores
│   Docker Engine │  ← Motor de contenedores
└─────────────────┘
**Orquestación y Cluster**
 
┌─────────────────┐
│   Kubernetes    │  ← Orquestador de contenedores
│   Minikube      │  ← Cluster local de 1 nodo
│   kubectl       │  ← CLI para gestionar Kubernetes
└─────────────────┘
**Configuración e Infraestructura**
 
┌─────────────────┐
│   YAML          │  ← Lenguaje de configuración
│   Git           │  ← Control de versiones
│   Bash/Shell    │  ← Scripting y comandos
└─────────────────┘
**Componentes Kubernetes Específicos**
API Objects Usados:
yaml
**Apps API**
- Deployment (apps/v1)

**Core API**
- Pod (v1)
- Service (v1)
- Node (v1)
**Recursos Configurados:**
 
Deployment → ReplicaSet → Pods → Containers
     ↓
 Service (NodePort) → Balanceo de carga 
 
**Especificaciones Técnicas**
 
Kubernetes: 1.28+  
Docker:     20.10+
Minikube:   v1.30+
kubectl:    Compatible con cluster
Puertos y Red:
 
Container Port:  80 (nginx)
Service Port:    80
NodePort:       30200 (rango 30000-32767)
Protocol:       TCP
Network:        ClusterIP + NodePort 
```
## Conceptos Aplicados

### 1. Ver todos los recursos (para captura de pantalla)
kubectl get all
```bash
docker@ubuntu:~/cursoDocker/curso6$ kubectl get all
NAME                                    READY   STATUS    RESTARTS   AGE
pod/webapp-deployment-8f786896f-4k2jm   1/1     Running   0          7m51s
pod/webapp-deployment-8f786896f-qqkns   1/1     Running   0          7m51s
pod/webapp-deployment-8f786896f-zkbsm   1/1     Running   0          7m51s

NAME                     TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
service/kubernetes       ClusterIP   10.96.0.1        <none>        443/TCP        28h
service/webapp-service   NodePort    10.105.172.246   <none>        80:30200/TCP   115m

NAME                                READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-deployment   3/3     3            3           7m51s

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-deployment-8f786896f   3         3         3       7m51s

```
#### 2. Ver detalles del deployment
kubectl describe deployment webapp-deployment
```bash
docker@ubuntu:~/cursoDocker/curso6$ kubectl describe deployment webapp-deployment
Name:                   webapp-deployment
Namespace:              default
CreationTimestamp:      Tue, 14 Oct 2025 18:55:19 -0700
Labels:                 app=webapp
                        env=homework
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=webapp
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=webapp
           env=homework
  Containers:
   webapp:
    Image:         nginx:alpine
    Port:          80/TCP
    Host Port:     0/TCP
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   webapp-deployment-8f786896f (3/3 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  7m58s  deployment-controller  Scaled up replica set webapp-deployment-8f786896f from 0 to 3
```

#### 3. Ver logs de uno de los pods
kubectl logs webapp-deployment-8f786896f-4k2jm

```bash
docker@ubuntu:~/cursoDocker/curso6$ kubectl logs webapp-deployment-8f786896f-4k2jm
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2025/10/15 01:55:31 [notice] 1#1: using the "epoll" event method
2025/10/15 01:55:31 [notice] 1#1: nginx/1.29.2
2025/10/15 01:55:31 [notice] 1#1: built by gcc 14.2.0 (Alpine 14.2.0)
2025/10/15 01:55:31 [notice] 1#1: OS: Linux 5.15.0-139-generic
2025/10/15 01:55:31 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2025/10/15 01:55:31 [notice] 1#1: start worker processes
2025/10/15 01:55:31 [notice] 1#1: start worker process 30
2025/10/15 01:55:31 [notice] 1#1: start worker process 31
2025/10/15 01:55:31 [notice] 1#1: start worker process 32
2025/10/15 01:55:31 [notice] 1#1: start worker process 33
```
### 4. Escalar a 5 réplicas
kubectl scale deployment webapp-deployment --replicas=5
```bash
docker@ubuntu:~/cursoDocker/curso6$ kubectl scale deployment webapp-deployment --replicas=5
deployment.apps/webapp-deployment scaled
```
kubectl get pods
```bash
ocker@ubuntu:~/cursoDocker/curso6$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
webapp-deployment-8f786896f-4k2jm   1/1     Running   0          8m24s
webapp-deployment-8f786896f-4t7rd   1/1     Running   0          7s
webapp-deployment-8f786896f-bjnxt   1/1     Running   0          7s
webapp-deployment-8f786896f-qqkns   1/1     Running   0          8m24s
webapp-deployment-8f786896f-zkbsm   1/1     Running   0          8m24s
```

### 5. Probar auto-healing (eliminar un pod)
kubectl delete pod webapp-deployment-8f786896f-4k2jm
```bash
docker@ubuntu:~/cursoDocker/curso6$ kubectl delete pod webapp-deployment-8f786896f-4k2jm
pod "webapp-deployment-8f786896f-4k2jm" deleted from default namespace

```
kubectl get pods -w
```bash
docker@ubuntu:~/cursoDocker/curso6$ kubectl get pods -w
NAME                                READY   STATUS    RESTARTS   AGE
webapp-deployment-8f786896f-4t7rd   1/1     Running   0          28s
webapp-deployment-8f786896f-bjnxt   1/1     Running   0          28s
webapp-deployment-8f786896f-qqkns   1/1     Running   0          8m45s
webapp-deployment-8f786896f-zj47q   1/1     Running   0          9s
webapp-deployment-8f786896f-zkbsm   1/1     Running   0          8m45s
```
