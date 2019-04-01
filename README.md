# k8s-tshoot

### Cluster:
```
kubectl cluster-info dump 
kubectl cluster-info dump --all-namespaces --output-directory=*path*
versiones de api soportadas: kubectl api-versions 
```

### Nodos:
Obtener nodos:
```
kubectl get nodes 
kubectl get nodes -o yaml 
```

Schedule en nodos master:
```
kubectl taint nodes --all node-role.kubernetes.io/master-
```

No schedulear 
kubectl taint nodes *nombre* node-role.kubernetes.io/master="":NoSchedule

Agregar label:  Esto es para los casos que no figura el rol del nodo
```
worker: kubectl label node *nombrenodo* node-role.kubernetes.io/worker=worker
master: kubectl label node *nombrenodo* node-role.kubernetes.io/master=master
```

Drenar nodo para mantenimiento:
```
kubectl drain *nodo* 
reiniciar
kubectl uncordon *nodo*
```

Trooubleshooting Cluster:
https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/cluster.md 


### Logs
Logs dentro del cluster:
* https://kubernetes.io/docs/tasks/debug-application-cluster/debug-cluster/#looking-at-logs

```
kubectl logs *pods*
kubectl logs *nombredeploy*
Herramienta kail: https://github.com/boz/kail 
```

### Eventos:
```
kubectl get events
kubectl get events -n *namespace*
kubectl get events --all-namespaces
```


### Detalle de recursos:
* pods: kubectl describe pod *nombre de pod*
* nodos: kubectl describe node *nombre de nodo*

### kubectl config
Configurar kubectl en master (control-plane)
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### Networking:
Instalación de CNI: https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/#pod-network

Troubleshoot: 
https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/network.md

### Cambiar output
 agregar "-o *formato*
 * -o wide
 * -o json
 * -o yaml

