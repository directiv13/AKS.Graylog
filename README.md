# AKS.Graylog

## Nginx Ingress Controller
**Add Helm repository**
```shell
helm repo add ingress-nginx <https://kubernetes.github.io/ingress-nginx>
```
**Install Helm chart**
```shell
helm install ingress-nginx ingress-nginx/ingress-nginx --version 4.7.1 -f nginx-controller.yaml -n graylog
```
**Create Ingress rules**
```shell
kubectl apply -f routes.yaml -n graylog
```
**Set up Load Balancer**
***Get node resource group of the cluster***
```shell
az aks show --name <aks_name> --resource-group <aks-resource-group> --query nodeResourceGroup -o tsv
```
***Create public IP***
```shell
az network public-ip create --resource-group <cluster-resource-group> --name GraylogInphizIP --sku Standard --allocation-method static
```
***Get Client ID of AKS cluster***
```shell
az aks show --name <aks_name> --resource-group <aks_resource_group> --query identity.principalId -o tsv
```
***Get resource group of the cluster***
```shell
az group show --name <cluster-resource-group> --query id -o tsv
```
***Create role assignment***
```shell
az role assignment create --assignee <client_id> --role "Network Contributor" --scope /subscriptions/<subscription_id>/resourceGroups/<cluster-resource-group>
```
**To delete**
```shell
helm uninstall ingress-nginx -n graylog
```
```shell
kubectl delete -f routes.yaml -n graylog
```

