## Nginx Ingress Controller
**Set up a Load Balancer**

***Get a node resource group of the cluster***
```shell
az aks show --name <aks_name> --resource-group <aks-resource-group> --query nodeResourceGroup -o tsv
```
***Create a public IP***
```shell
az network public-ip create --resource-group <cluster-resource-group> --name GraylogInphizIP --sku Standard --allocation-method static
```
***Get a Client ID of AKS cluster***
```shell
az aks show --name <aks_name> --resource-group <aks_resource_group> --query identity.principalId -o tsv
```
***Get a resource group of the cluster***
```shell
az group show --name <cluster-resource-group> --query id -o tsv
```
***Create a role assignment***
```shell
az role assignment create --assignee <client_id> --role "Network Contributor" --scope /subscriptions/<subscription_id>/resourceGroups/<cluster-resource-group>
```
**Add a Helm repository**
```shell
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```
**Install a Helm chart**
```shell
helm repo update
helm install ingress-nginx ingress-nginx/ingress-nginx --version 4.7.1 -f nginx/nginx-controller.yaml -n graylog
```
**Create Ingress rules**
```shell
kubectl apply -f nginx/routes.yaml -n graylog
```
### To delete
```shell
helm uninstall ingress-nginx -n graylog
```
```shell
kubectl delete -f routes.yaml -n graylog
```