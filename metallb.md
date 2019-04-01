# Implementar MetalLB

Documentación de instalación: https://metallb.universe.tf/installation/


1. Instalar
´´´
kubectl apply -f https://raw.githubusercontent.com/google/metallb/v0.7.3/manifests/metallb.yaml
´´´

* The metallb-system/controller deployment. This is the cluster-wide controller that handles IP address assignments.
* The metallb-system/speaker daemonset. This is the component that speaks the protocol(s) of your choice to make the services reachable. Se deploya un pod por nodo.


2. Configuración
Documentación: https://metallb.universe.tf/configuration/

Se debe aplicar un configMap


#### Tipos de politicas:
https://metallb.universe.tf/usage/


#### CNI Compatibles:
Flannel y Weave son compatibles.
https://metallb.universe.tf/installation/network-addons/