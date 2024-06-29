#Instalación de ArgoCD Instalar ArgoCD:

sh Copiar código kubectl create namespace argocd kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml 

#Exponer el servidor de ArgoCD:

#Con un Load Balancer:

sh Copiar código kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}' Con un Port forwarding:

sh Copiar código sudo microk8s kubectl port-forward svc/argocd-server -n argocd 8080:443 Obtener la contraseña de ArgoCD:

sh Copiar código kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password


kubectl port-forward svc/argocd-server -n argocd 8080:443

#Para modificar el service (asi no hay necesidad de inicializar el forward), y que pueda estar en NodePort:

kubectl patch svc argocd-server -n argocd -p "{\"spec\": {\"type\": \"NodePort\", \"ports\": [{\"name\": \"http\", \"nodePort\": 30080, \"port\": 80, \"protocol\": \"TCP\", \"targetPort\": 8080}, {\"name\": \"https\", \"nodePort\": 30443, \"port\": 443, \"protocol\": \"TCP\", \"targetPort\": 8080}]}}"

#Verificar la clave de admin:

argocd admin initial-password -n argocd

#Loguerase:
argocd login localhost:8080 o el puerto correspondiente si cambie el service a nodeport <30080>

#Crear proyecto, con archivos de manifiesto:

argocd app create pokedex-local-it --repo https://github.com/abelucci/KUBERNETES.git --path POKEDEX --dest-server https://kubernetes.default.svc --dest-namespace default

#Helm charts:

argocd app create poke-argo-it --repo https://abelucci.github.io/HELM/ --helm-chart poke-helm --revision 1.0.0 --dest-namespace default --dest-server https://kubernetes.default.svc

#Verificar proyectos creados:

kubectl get applications -n argocd



###LANZAR APP EN AZURE

Crear cluster en la nube para poder implementar el servicio.

Login en azure:

az login
Crear grupo de recursos y locación:

az group create --name <myResourceGroup> --location eastus
Crear clúster:

az aks create -n <nombredelcluster> -g <nombredelrecurso>
Verificar:

az aks list -o table
Conectarse al cluster mediante kubectl en localhost:

az aks get-credentials -n <name cluster> -g <name recurso> (obtengo credenciales de acceso mediante kubectl) asi se guarda en el archivo user/<user>/.kube/config
az aks get-credentials -n kube-it -g KUBE
Verificar los clusteres creados:

kubectl config get-contexts
1715779830400.png

Seleccionar el cluster donde vamos a trabajar:

kubectl config use-context <name cluster>
Por útimo, deployar APP de APPS en argo:

argocd app create pokedex-azure-it --repo https://github.com/abelucci/KUBERNETES.git --path POKEDEX-AZURE --dest-server https://kube-it-dns-mldc00n1.hcp.eastus.azmk8s.io:443 --dest-namespace default
Tener en cuenta que se modificó en el archivo de manifiesto services.yaml, el tipo de servicio a LoadBalancer.

Con Helm chart:

argocd app create poke-argo-azure --repo https://abelucci.github.io/HELM/ --helm-chart poke-helm-azure --revision 1.0.0 --dest-namespace default --dest-server https://kube-it-dns-mldc00n1.hcp.eastus.azmk8s.io:443
Tener en cuenta que se modificó en el archivo de manifiesto values.yaml, el tipo de servicio a LoadBalancer.

Verificar todos los servicios creados:

kubectl get all -n argocd (solo en ese namespace)
kubectl get all
FQDN EN AZURE
Para poder tener un nombre FQDN, se debe agregar la siguiente linea en el archivo de maniesto services.yml:

1715785677345.png

En Helm chart, agregar la etiqueda en el archivo de maniesto values.yaml para luego invocarlo en services.yaml:

1715873855224.png

Agregar las siguientes lineas en el archivo de manifiesto ./templates/services.yaml:

1715871372212.png

La dirección prefijada seria la siguiente:

<…>.eastus.cloudapp.azure.com
myserviceuniquelabel.eastus.cloudapp.azure.com

Dando como resultado:

my-pokedex-it.eastus.cloudapp.azure.com:<puertocorrespondiente>
mypokedexit.eastus.cloudapp.azure.com:<puerto correspondiente> <-- DNS en helm chart
1715785774095.png

1715785774095.png

En Helm chart, el nombre dns es "mypokedexit":

