###### Deploy de dashboard:
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/aio/deploy/recommended/kubernetes-dashboard.yaml

kubectl proxy

Ingresar: http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/

Mas info: https://github.com/kubernetes/dashboard 

###### Login al Dashboard
Crear usuario rbac: https://github.com/kubernetes/dashboard/wiki/Creating-sample-user#create-service-account 

Token de autenticación:
kubectl -n kube-system describe secret ((kubectl -n kube-system get secret  | sls admin-user) -split "\s+")[0] 

