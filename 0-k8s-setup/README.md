# Manual installations

## Traefik

```bash
kubectl create ns traefik
helm repo add traefik https://traefik.github.io/charts
helm repo update
helm install --namespace=traefik -f traefik/values.yml traefik traefik/traefik
```

```bash
kubectl apply -f traefik/ingress.yml
```

## ArgoCD

```bash
kubectl create namespace argocd

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

kubectl apply -f argocd/ingress.yml
kubectl apply -n argocd -f argocd/configmap.yml

kubectl rollout restart deployment argocd-server -n argocd
kubectl rollout restart deployment argocd-dex-server -n argocd

# --- #

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Configure first argocd masterapp:
```bash
kubectl apply -n argocd -f argocd/masterapp-base.yml
```
