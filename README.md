# k8s-tshoot

### Cluster:
```
kubectl cluster-info dump 
kubectl cluster-info dump --all-namespaces --output-directory=*path*
versiones de api soportadas: kubectl api-versions 
```

Healthcheck:
kubectl get cs
kubectl get events
kubectl get nodes
kubectl get apiservices

tshoot: https://kubernetes.io/docs/tasks/debug-application-cluster/debug-cluster/ 


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

No schedulear:
```
kubectl taint nodes *nombre* node-role.kubernetes.io/master="":NoSchedule
```

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

### Pods, container e imagenes.
Configurar pull imagen de Registry:
*  https://docs.cloud.oracle.com/iaas/Content/Registry/Tasks/registrypullingimagesfromocir.htm 
* https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

Tshoot pods: https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/ 

### registry
* https://hub.docker.com/_/registry
* Harbor: https://goharbor.io/ 

### Logs
Logs dentro del cluster:
* https://kubernetes.io/docs/tasks/debug-application-cluster/debug-cluster/#looking-at-logs

```
kubectl logs *pods*
kubectl logs *nombredeploy*
kubectl logs --previous
```
* Herramienta kail: https://github.com/boz/kail 
* Herramienta Stern: https://github.com/wercker/stern 
* Deploy de DS para enviar logs a logly

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
https://kubernetes.io/docs/concepts/cluster-administration/addons/

Troubleshoot: 
https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/network.md

Flannel: cat /run/flannel/subnet.env
* Limpiar ip tables:
```
systemctl stop kubelet
systemctl stop docker
iptables --flush
iptables -tnat --flush
systemctl start kubelet
systemctl start docker
```


Endpoints:
```
kubectl get endpoints
```

Services:
```
kubectl get svc
```
El servicio *kuberntes* tiene el endpoing del APiServer (Master) y con el puerto que se definio al iniciar cluster (default 6443).

##### SELINUX 
SELINUX puede generar problemas al estar activado, para ver estado
Ver estado:
```
/usr/sbin/getenforce o /usr/sbin/sestatus
```
Deshabilitar:
1.
```
    Set SELINUX=disabled in file /etc/selinux/config (persistent even after reboot)
```
2.
```
   setenforce 0
   reboot
```

### Cambiar output
 agregar "-o *formato*
 * -o wide
 * -o json
 * -o yaml


### Migración
* https://github.com/yaron2/azure-kube-cli
* https://github.com/mhausenblas/reshifter
* https://github.com/heptio/velero


Tips eviction:
Eviction: https://kubernetes.io/docs/tasks/administer-cluster/out-of-resource/ 
# delete all evicted pods from all namespaces
```
kubectl get pods --all-namespaces | grep Evicted | awk '{print $2 " --namespace=" $1}' | xargs kubectl delete pod


kubectl get pods --all-namespaces --field-selector 'status.phase==Failed' -o json | kubectl delete -f -
```

# delete all containers in ImagePullBackOff state from all namespaces

```
kubectl get pods --all-namespaces | grep 'ImagePullBackOff' | awk '{print $2 " --namespace=" $1}' | xargs kubectl delete pod
```


# delete all containers in ImagePullBackOff or ErrImagePull or Evicted state from all namespaces
```
kubectl get pods --all-namespaces | grep -E 'ImagePullBackOff|ErrImagePull|Evicted' | awk '{print $2 " --namespace=" $1}' | xargs kubectl delete pod
```

# Eliminar un pod en terminating de manera forzada
```
kubectl delete pod --grace-period=0 --force --namespace <NAMESPACE> <PODNAME> 
```


# Proxy:
https://docs.oracle.com/cd/E52668_01/E88884/html/kube_admin_config_proxy.html