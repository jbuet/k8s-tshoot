# K8 on prem con kubeadm
### T-shoot kubeadm
https://kubernetes.io/docs/setup/independent/troubleshooting-kubeadm/

### Instalar cluster kubeadm
Documentos:
https://www.howtoforge.com/tutorial/centos-kubernetes-docker-cluster/
https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/

Pasos:
1. renombrar servidor en caso de ser necesario. Configurar IPv4 estatica
    * cada server debe resolver por DNS a los demas servidores (modificar archivo host)
2. Instalar docker engine  https://get.docker.com/
3.  Instalar kubeadm https://kubernetes.io/docs/setup/independent/install-kubeadm/ 
4.  Abrir puertos necesarios master/node: https://kubernetes.io/docs/setup/independent/install-kubeadm/#master-node-s
5. deshabilitar swap
    sudo swapon -s
    sudo swapoff -a
    y comentar en sudo vim /etc/fstab linea de swap
    luego reiniciar

6. revisar cgroup de kubernetes y docker sea el mismo
    * Identificar ubicacion de 10-kubeadm.conf (Archivo de configuracion de servicio kubelet)
        * find / -type f -name 10-kubeadm.conf

7. kubeadm init
https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/#initializing-your-master
Tener en cuenta el atributo --pod-network-cidr por el CNI a instalar

8. Implementar CNI
https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/#pod-network 

9. Join de nodos:
Los nodos deben repetir los pasos 1-6 y luego ejecutar comando *kubeadm join* con el token que se genero del cluster.

### Errores
ERROR en kubeadm init:
1. [ERROR FileContent--proc-sys-net-ipv4-ip_forward]: /proc/sys/net/ipv4/ip_forward contents are not set to 1
echo '1' > /proc/sys/net/ipv4/ip_forward

2. Luego de instalar, los nodos estan en estado "notready". Se debe instalar un CNI
https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/#pod-network 

3. los pod coredns en estado "crashloopbackoff".
revisar logs con kubectl logs "nombre de pod" -n kube-system
verificar que los puertos en los workers esten abiertos
https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/pod.md

Eliminar nodo:
kubectl delete node nombre nodo
en el nodo: 
kubeadm reset
systemctl stop kubelet
systemctl stop docker
rm -rf /var/lib/cni/
rm -rf /var/lib/kubelet/*
rm -rf /etc/cni/
ifconfig cni0 down
ifconfig flannel.1 down
ifconfig docker0 down
ip link delete cni0
ip link delete flannel.1
