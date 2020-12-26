### Comandos kubectl

- kubectl config use-context k8s-cluster-example (situarse en el contexto de un cluster en particular)
- kubectl delete pod nginx --grace-period=0 --force (forzar la eliminación de un pod sin considerar el periodo de gracia)
- kubectl explain pod.spec.containers.livenessProbe (usar explain para acceder a documentacion en la cli)
- kubectl get pods (listar pods)
- kubectl get secrets (listar secretos)
- kubectl get deployments
- kubectl get configmaps
- kubectl get nodes
- kubectl get services
