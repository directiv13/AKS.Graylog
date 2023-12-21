## Nginx Ingress Controller
**Set up a Load Balancer**

***Get a node resource group of the cluster***
```shell
az aks show --name <aks_name> --resource-group <aks-resource-group> --query nodeResourceGroup -o tsv
```
***Create a public IP***
```shell
az network public-ip create --resource-group <cluster-resource-group> --name <public-ip-name> --sku Standard --allocation-method static
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
```
**Create configmap**
_Configmap is indeed to route TCP traffic_
```shell
kubectl apply -f configmap.yaml
```
**Deploy ingress controller and related services**
```shell
helm install ingress-nginx ingress-nginx/ingress-nginx --namespace graylog --values values.yaml
```
**Edit deployment**

I used VS Code as editor, by default it was Notepad. To set VS Code as editor for kubectl, run the next command:
```shell
$env:KUBE_EDITOR="code --wait"
```

_To edit deployment:_
```shell
kubectl edit deployment -n graylog ingress-nginx-controller
```

In the editor add --tcp-services-configmap=$(POD_NAMESPACE)/tcp-services to spec.template.spec.args

**Edit the service**
```shell
kubectl edit svc ingress-nginx-controller -n graylog
```
Add next code to spec.ports:
```YAML
- name: graylog
    port: 12201
    protocol: TCP
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