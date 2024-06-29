Instalación de ArgoCD
Instalar ArgoCD:

sh
Copiar código
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
Exponer el servidor de ArgoCD:

Con un Load Balancer:

sh
Copiar código
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
Con un Port forwarding:

sh
Copiar código
sudo microk8s kubectl port-forward svc/argocd-server -n argocd 8080:443
Obtener la contraseña de ArgoCD:

sh
Copiar código
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password
