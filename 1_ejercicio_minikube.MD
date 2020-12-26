## Steps

### Crear despliegue con nombre hello-minikube cargado una imagen del container registry de google llamada echoserver en su version 1.4

- kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.4
```
deployment.apps/hello-minikube created
```
### Crear un servicios que exponga la aplicacion por el puerto 8080

- kubectl expose deployment hello-minikube --type=NodePort --port=8080
```
service/hello-minikube exposed
```
## Ver el servicio creado

- kubectl get services hello-minikube
```
NAME             TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
hello-minikube   NodePort   10.108.26.176   <none>        8080:32216/TCP   29s
```
## Acceder en el browser con minikube
- minikube service hello-minikube

## Usar un portforward k8 para acceder al puerto del pod 8080 a tráves del nuestro 7080
- kubectl port-forward service/hello-minikube 7080:8080

## Crear otro despliegue de nombre balanced con la misma imagen

- kubectl create deployment balanced --image=k8s.gcr.io/echoserver:1.4  
````
deployment.apps/balanced created
````
## Crear un servicio de tipo loadbalancer

- kubectl expose deployment balanced --type=LoadBalancer --port=8080
````
service/balanced exposed
````


