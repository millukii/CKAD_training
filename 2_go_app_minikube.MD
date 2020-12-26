### Iniciar minikube permitiendo un registry inseguro
- minikube start --vm-driver=virtualbox --insecure-registry=localhost:5000
### Inicializar modulo

- go mod init go-k8s-app 

### Crear sencillo archivo main.go

`````
package main

import "fmt"

func main() {

	fmt.Print("jojo")
}
`````
### Agregar Dockerfile
`````
FROM golang:alpine AS build
WORKDIR /go/src/gok8stest
COPY . .
RUN go build -o /go/bin/gok8stest main.go

FROM scratch
COPY --from=build /go/bin/gok8stest /go/bin/gok8stest
ENTRYPOINT ["/go/bin/gok8stest"]

`````
### Habilitar el entorno docker dentro de minikube
- minikube docker-env

### Apuntar minikube al daemon docker
- eval $(minikube docker-env) (si nos salimos de la terminal deberemos ejecutar el comando nuevamente)

### Siempre usar una de estas dos opciones en minikube para el deployment. Para que k8s use si o sí las imagenes locales
````
imagePullPolicy:IfNotPresent or imagePullPolicy:Never
````
### Si queremos que una imagen siempre se cargue con los nuevos clúster. Usamos la caché de minikube
- minikube cache add alpine:latest
- minikube cache reload (updatear la imagen después de algún cambio)

### Construir imagen del código fuente

- docker build --tag $(minikube ip):5000/gok8stest:1.0.0 .

### Crear pod
- kubectl run --generator=run-pod/v1 gok8stest --image $(minikube ip):5000/gok8stest:1.0.0  --dry-run

### Exportar pod como yaml
- kubectl run --generator=run-pod/v1 gok8stest --image $(minikube ip):5000/gok8stest:1.0.0 --dry-run -o yaml > pod.yaml

### Yaml pod

`````
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: gok8stest
  name: gok8stest
spec:
  containers:
  - image: 192.168.99.107:5000/gok8stest:1.0.0
    name: gok8stest
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

`````
### Crear deployment
- kubectl create deployment gok8stest --image $(minikube ip):5000/gok8stest:1.0.0 --dry-run

### Crear deployment y salvar yml
- kubectl create deployment gok8stest --image $(minikube ip):5000/gok8stest:1.0.0 --dry-run -o yaml > deployment.yaml
`````
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: gok8stest
  name: gok8stest
spec:
  replicas: 1
  selector:
    matchLabels:
      app: gok8stest
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: gok8stest
    spec:
      containers:
      - image: 192.168.99.107:5000/gok8stest:1.0.0
        name: gok8stest
        resources: {}
status: {}

`````
## Aprovechar kubectl create deployment para crear un yaml con el replica set
- kubectl create deployment gok8stest --image $(minikube ip):5000/gok8stest:1.0.0 --dry-run -o yaml > replicaset.yaml
- Eliminar status object y strategy
- Cambiar kind Deployment to ReplicaSet
`````
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  creationTimestamp: null
  labels:
    app: gok8stest
  name: gok8stest
spec:
  replicas: 1
  selector:
    matchLabels:
      app: gok8stest
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: gok8stest
    spec:
      containers:
      - image: 192.168.99.107:5000/gok8stest:1.0.0
        name: gok8stest
        resources: {}
`````
### Configmap dry-run
- kubectl create configmap gok8stest-config --from-literal key=value --dry-run
### Guardar yaml configmap
- kubectl create configmap gok8stest-config --from-literal key=value --dry-run -o yaml > configmap.yaml

`````
apiVersion: v1
data:
  key: value
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: gok8stest-config
`````
### Consultar especificacion de los contenedores
- kubectl explain pod.spec.containers

### Consultar especificacion del enviroment con filtro
- kubectl explain pod.spec.containers|grep env
- kubectl explain pod.spec.containers.envFrom --recursive

### Agregar confimap al deployment
`````
        envFrom:
        - configMapRef:
            name: gok8stest-config
`````
### Actualizar deployment
- kubectl apply  -f deployment.yaml 

### Crear servicio  y  guardar yaml
- kubectl expose deployment gok8stest --type=LoadBalancer --port=8080 --dry-run -o yaml > service.yaml
- kubectl apply  -f service.yaml 

### Ver servicio
-  kubectl get svc gok8stest
`````
NAME        TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
gok8stest   LoadBalancer   10.111.69.81   <pending>     8080:31098/TCP   72s
``````
### Un servicio representa un backends de pods que estan disponibles  como endpoints
- kubectl describe svc gok8stest
````
Name:                     gok8stest
Namespace:                default
Labels:                   app=gok8stest
Annotations:              kubectl.kubernetes.io/last-applied-configuration:
                            {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"creationTimestamp":null,"labels":{"app":"gok8stest"},"name":"gok8stest",...
Selector:                 app=gok8stest
Type:                     LoadBalancer
IP:                       10.111.69.81
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  31098/TCP
Endpoints:
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
````
### Ver endpoints de un servicio
- kubectl get ep gok8stest
`````
NAME        ENDPOINTS   AGE
gok8stest               7m10s
`````


