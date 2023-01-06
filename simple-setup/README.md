# rshiny-k8s
R Shiny App on Kubernetes

Following setup in https://learn.microsoft.com/en-us/azure/aks/ingress-tls?tabs=azure-cli

```
NAMESPACE=ingress-basic

helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

helm install ingress-nginx ingress-nginx/ingress-nginx \
  --create-namespace \
  --namespace $NAMESPACE \
   --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="rshinyappseneros"  \
  --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-health-probe-request-path"=/healthz


helm upgrade ingress-nginx ingress-nginx/ingress-nginx \
  --namespace $NAMESPACE \
  --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="rshinyappseneros" \
 --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-health-probe-request-path"=/healthz


kubectl label namespace ingress-basic cert-manager.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

CERT_MANAGER_REGISTRY=quay.io
CERT_MANAGER_TAG=v1.8.0

# Install the cert-manager Helm chart
helm install cert-manager jetstack/cert-manager \
  --namespace ingress-basic \
  --set installCRDs=true \
  --set nodeSelector."kubernetes\.io/os"=linux \

  --version $CERT_MANAGER_TAG \
  --set image.repository=$CERT_MANAGER_REGISTRY/$CERT_MANAGER_IMAGE_CONTROLLER \
  --set image.tag=$CERT_MANAGER_TAG \
  --set webhook.image.repository=$CERT_MANAGER_REGISTRY/$CERT_MANAGER_IMAGE_WEBHOOK \
  --set webhook.image.tag=$CERT_MANAGER_TAG \
  --set cainjector.image.repository=$CERT_MANAGER_REGISTRY/$CERT_MANAGER_IMAGE_CAINJECTOR \
  --set cainjector.image.tag=$CERT_MANAGER_TAG

```
