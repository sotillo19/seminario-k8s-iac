## Instalación de Traefik y ArgoCD

Se da por sentado que la herramienta de Docker Desktop ha sido instalada previamente.

## Requisitos previos:

1. Habilitamos Kubernetes en docker desktop:
   
   1. Docker Desktop→Settings→Kubernetes→Enable kubernetes

2. Instalamos helm:
   
   1. Nos dirigiremos a la [siguiente página](https://helm.sh/es/docs/intro/install/) y realizaremos los pasos correspondientes a nuesto SO:

## Traefik

- **Enlaces a los ficheros**
  
  - [ingress.yml](traefik/ingress.yml)
  - [values.yml](traefik/values.yml)

- Ejecutamos los siguientes comandos para añadir traefik a nuestro cluster de kubernetes:
  
  - ```bash
    kubectl create ns traefik
    helm repo add traefik https://traefik.github.io/charts
    helm repo update
    helm install --namespace=traefik -f traefik/values.yml traefik traefik/traefik
    ```

- Creamos el ingress de Treafik dentro del cluster:
  
  - ```bash
    kubectl apply -f traefik/ingress.yml
    ```

## ArgoCD

- **Enlaces a los ficheros**
  
  - [ingress.yml](argocd/ingress.yml)
  
  - [configmap.yml](argocd/configmap.yml)
  
  - [masterapp-base.yml](argocd/masterapp-base.yml)

- Instalamos ArgoCD dentro del cluster por medio de los siguientes comandos:
  
  - ```bash
    kubectl create namespace argocd
    kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
    ```

- Creamos el Ingress de ArgoCD
  
  - ```bash
    kubectl apply -f argocd/ingress.yml
    ```

- Aplicamos la configuración
  
  - ```bash
    kubectl apply -n argocd -f argocd/configmap.yml
    ```
    
- Reiniciamos el servicio
  
  - ```bash
    kubectl rollout restart deployment argocd-server -n argocd
    kubectl rollout restart deployment argocd-dex-server -n argocd
    ```
    
- Obtenemos las credenciales
  
  - ```bash
    kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
    ```

- Generamos la masterapp de ArgoCD la cual será la encargada de crear las aplicaciones sin la necesidad de usar la cli de Kubernetes (kubectl)
  
  - ```bash
    kubectl apply -n argocd -f argocd/masterapp-base.yml
    ```
