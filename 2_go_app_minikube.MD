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
