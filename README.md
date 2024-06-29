# DESAFÍO 15 - HELM CHART & GITOPS

En este desafío, trabajaré con un Cluster de Kubernetes utilizando Microk8s instalado en una distribución Ubuntu 22.04 desde WSL. Adicionalmente, realizaré algunas pruebas en la página KillerKoda.

- Link del repositorio: [https://github.com/romancete85/eduit.git](https://github.com/romancete85/eduit.git)
  - Carpeta: `Kubernetes/k8s/Desafio15`

- Link del desafío: [Google Drive](https://drive.google.com/drive/folders/1WlyIx52iEO0rnW69mMm-OcRRWXxKTFNj?usp=drive_link)

## Introducción

En este desafío utilizaremos herramientas de GitOps para gestionar cambios en archivos de manifiesto y automatizar despliegues desde un repositorio. Crearemos un paquete Helm chart a partir de los archivos de manifiesto utilizados en el desafío anterior e implementaremos una herramienta de GitOps en un entorno MicroK8s en WSL.

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
