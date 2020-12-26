### Comandos kubectl

- kubectl config view (ver las entradas que se han configurado para kubctl)
- kubectl config use-context k8s-cluster-example (situarse en el contexto de un cluster en particular)
- kubectl delete pod nginx --grace-period=0 --force (forzar la eliminación de un pod sin considerar el periodo de gracia)
- kubectl explain pod.spec.containers.livenessProbe (usar explain para acceder a documentacion en la cli)
- kubectl get pods (listar pods)
- kubectl get secrets (listar secretos)
- kubectl get deployments
- kubectl get configmaps
- kubectl get nodes
- kubectl get services

### Comandos minicube
- minicube delete (elimina todas las configuraciones previas)
- minikube start --vm-driver=virtualbox (inicia usando el driver vb)
- kubectl get po -A (acceder al nuevo cluster)
- minikube kubectl -- get pods -A (descargar la version apropiada para kubectl)

```
NAMESPACE     NAME                               READY   STATUS    RESTARTS   AGE
kube-system   coredns-74ff55c5b-d6nrs            1/1     Running   0          6m37s
kube-system   etcd-minikube                      1/1     Running   0          6m51s
kube-system   kube-apiserver-minikube            1/1     Running   0          6m51s
kube-system   kube-controller-manager-minikube   1/1     Running   0          6m51s
kube-system   kube-proxy-l9nb5                   1/1     Running   0          6m37s
kube-system   kube-scheduler-minikube            1/1     Running   0          6m51s
kube-system   storage-provisioner                1/1     Running   1          6m51s
```
- minikube dashboard (habilita un dashboard de k8 para acceder desde el browser)
- minikube pause (Pause Kubernetes without impacting deployed applications)
- minikube stop (Halt the cluster)
- minikube config set memory 16384 (Increase the default memory limit (requires a restart))
- minikube addons list (Browse the catalog of easily installed Kubernetes services)
- minikube start -p aged --kubernetes-version=v1.16.1 (Create a second cluster running an older Kubernetes release)
- minikube delete --all (Delete all of the minikube clusters)
