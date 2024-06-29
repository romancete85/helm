# DESAFÍO 15 - HELM CHART & GITOPS

En este desafío, trabajaré con un Cluster de Kubernetes con Microk8s instalado en una distribución Ubuntu 22.04 desde WSL. Adicionalmente, realizaré algunas pruebas en la página KillerKoda: [KillerKoda](https://killercoda.com/login).

## Link del Repositorio
[Repositorio GitHub](https://github.com/romancete85/eduit.git)

Ruta del desafío: `Kubernetes/k8s/Desafio15`

Link del desafío: [Desafío 15](https://drive.google.com/drive/folders/1WlyIx52iEO0rnW69mMm-OcRRWXxKTFNj?usp=drive_link)

## Introducción
En este desafío, utilizaremos herramientas de GitOps para gestionar cambios en archivos de manifiesto y automatizar el despliegue al primer cambio que se encuentre en el repositorio. Crearemos un paquete Helm chart a partir de los archivos de manifiesto utilizados en el desafío anterior y luego implementaremos una herramienta de GitOps en un entorno MicroK8s en WSL (Windows Subsystem for Linux).

## Requisitos Previos
- MicroK8s instalado en WSL
- Helm instalado
- ArgoCD instalado y configurado en el cluster MicroK8s
- Acceso a una cuenta de GitHub para almacenar los archivos

## Configuración del Entorno

### Instalación de MicroK8s
Previamente realizado en el desafío anterior.

### Instalación de Helm
Instalar Helm:

```sh
sudo snap install helm --classic
"Instalación de ArgoCD
"Instalar ArgoCD:


kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

Exponer el servidor de ArgoCD
Con un Load Balancer:

```sh
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
Con un Port forwarding:

```sh
sudo microk8s kubectl port-forward svc/argocd-server -n argocd 8080:443

Obtener la contraseña de ArgoCD

```sh
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

Cambio de Password
Accede a la interfaz web de ArgoCD usando la IP obtenida del servicio de ArgoCD y la contraseña obtenida.

Archivos de Manifiestos Utilizados
Deployment
Service
Helm Chart: Conversión a Paquete Helm
Crear una carpeta helmcharts y dentro de ella ejecutar el comando:

```sh
helm create alien-tetris-chart

#Estructura del Helm Chart

Helm/alien-tetris-chart
  ├── templates/
  │   ├── deployment.yaml
  │   ├── service.yaml
  │   ├── ingress.yaml
  ├── Chart.yaml
  ├── values.yaml

Chequeo del Helm Chart

```sh
sudo microk8s helm install --dry-run debug .

Instalación en el Namespace Helm

```sh
sudo microk8s helm install alien-tetris-chart --namespace default

Configuración en GitHub Pages
Configuración de nuevo repositorio, rama y habilitación de GitHub Pages según documentación oficial de Helm.

Configuración del Repo y Actualización Manual

```sh
helm repo index ./ --url https://romancete85.github.io/helm/

Configuración del Pipeline Workflow File

```yaml
name: Release Charts

on:
  push:
    branches:
      - main

jobs:
  release:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Helm
        uses: helm/chart-releaser-action@v1.6.0

      - name: Release Charts
        run: |
          cr upload --skip-existing

###ArgoCD
Configuración de ArgoCD
Configurar la CLI de Argo CD:

```sh
argocd login localhost:8080
Configuración de una Aplicación con Argo CD
Crear una app con Argo CD que apunte al Helm chart:

```sh
argocd app create alien-tetris-argo --repo https://romancete85.github.io/helm/ --helm-chart alien-tetris-chart --revision 1.0.0 --dest-namespace default --dest-server https://kubernetes.default.svc
Habilitación de Auto-sync y Self-healing

```sh
argocd app set alien-tetris-argo --sync-policy automated --self-heal
Sincronización de la Aplicación

```sh
argocd app sync alien-tetris-argo
Verificación de Estado

```sh
kubectl get applications -n argocd
Documentación y Problemas Encontrados
Problema con el Despliegue del Ingress
Solución: Revisar las configuraciones de las reglas de Ingress y los servicios asociados.

Error de Conexión a ArgoCD
Solución: Asegurarse de que los servicios de ArgoCD están expuestos correctamente y que la IP es accesible.

