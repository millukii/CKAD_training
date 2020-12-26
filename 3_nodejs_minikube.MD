### Start minikube con docker
- minikube start --vm-driver=hyperkit --insecure-registry=localhost:5000

### Setear contexto k8
- kubectl config use-context minikube

### Ver información del cluster
- kubectl cluster-info
````
Kubernetes master is running at https://192.168.64.2:8443
KubeDNS is running at https://192.168.64.2:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
````
### Iniciar proyecto
- npm init -y

### Crear archivo server.js

```````
var http = require('http');

var handleRequest = function(request, response) {
    console.log('Received request for URL: ' + request.url);
    response.writeHead(200);
    response.end('Jojojojojojojo');
};

var server = http.createServer(handleRequest);
server.listen(8080);
````````
### Ejecutar
- npm run start
- ir a http://localhost:8080/

### Habilitar que minikube use el docker del host
- eval $(minikube -p minikube docker-env)

### Crear Dockerfile
`````
from node:6.9.2
EXPOSE 8080
COPY server.js .
CMD node server.js
`````
### Build
- docker build . -t jojonode:v1

### Desplegar  con la imagen que construimos forzando que se busque en el registry local
- kubectl run app-node --image=jojonode:v1 --port=8080 --image-pull-policy=Never
````
deployment.apps/app-node created
`````
### Ver pods
- kubectl get pods
````
NAME                               READY   STATUS    RESTARTS   AGE
app-node-865f955dc9-6jzz7   1/1     Running   0          27m
````
### Ver despliegues 
- kubectl get deployments
`````
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
app-node   1/1     1            1           27m
`````
### Ver eventos
- kubectl get events
`````
LAST SEEN   TYPE     REASON              OBJECT                                  MESSAGE
31m         Normal   Scheduled           pod/app-node-865f955dc9-6jzz7    Successfully assigned default/contenedor-node-865f955dc9-6jzz7 to minikube
31m         Normal   Pulled              pod/app-node-865f955dc9-6jzz7    Container image "jojonode:v1" already present on machine
31m         Normal   Created             pod/app-node-865f955dc9-6jzz7    Created container app-node
31m         Normal   Started             pod/app-node-865f955dc9-6jzz7    Started container app-node
31m         Normal   SuccessfulCreate    replicaset/app-node-865f955dc9   Created pod: app-node-865f955dc9-6jzz7
31m         Normal   ScalingReplicaSet   deployment/app-node              Scaled up replica set app-node-865f955dc9 to 1

### Crear servicio
- kubectl expose deployment app-node --type=LoadBalancer
`````
service/app-node exposed
`````
### Ver servicios
- kubectl get services
`````
NAME              TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
app-node          LoadBalancer   10.104.136.47   <pending>     8080:32045/TCP   4m50s
kubernetes        ClusterIP      10.96.0.1       <none>        443/TCP          90m
`````
### Exponer por minikube
- mikikube service app-node
