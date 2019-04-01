Para utilizar el comando: *kubectl top node* se debe implementar el servicio "metrics-server".

#### Implementar
git clone https://github.com/kubernetes-incubator/metrics-server.git

```
# Kubernetes 1.7
$ kubectl create -f metrics-server/deploy/1.7/

# Kubernetes > 1.8
$ kubectl create -f metrics-server/deploy/1.8+/
```


#### Errores:
1. Error from server (NotFound): the server could not find the requested resource (get services http:heapster:)
    * se debe realizar deploy de metrics server
    * revisar api metrics con: kubectl get apiservice | grep metrics

    En caso de error al hacer kubectl top node -> metrics not found
    agregar las siguientes opciones en archivo metrics-server-deployment.yaml por debajo de imagepullpolicy
    ```
     command:
     - /metrics-server
     - --kubelet-insecure-tls
     - --kubelet-preferred-address-types=InternalIP
     ```

     Verificar si pod resuelve nodos, caso que no modificar archivo de deployment con hostnames
     https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/


2.  Error: https://10.96.0.1:443/api/v1/namespaces/kube-system/configmaps/extension-apiserver-authentication: dial tcp 10.96.0.1:443: connect: no route to host y pod en CrashLoopBackOff 

* ejecutar: kubectl drain *node* 

* limpiar iptables:
```
systemctl stop kubelet
systemctl stop docker
iptables --flush
iptables -tnat --flush
systemctl start kubelet
systemctl start docker

kubectl uncordon *node*
```