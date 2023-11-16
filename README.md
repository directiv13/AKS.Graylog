# AKS.Graylog

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
## Elasticsearch
**Create a statefulset**
```shell
kubectl apply -f statefulset.yaml -n graylog
```
**Create a service for Elasticsearch cluster**
```shell
kubectl apply -f service.yaml -n graylog
```
### To delete
```shell
kubectl delete -f statefulset.yaml -n graylog
```
```shell
kubectl delete -f service.yaml -n graylog
```
## MongoDB Replica Set
**Create a statefulset MongoDB**
```shell
kubectl create -f mongodb.yaml
```
**Create a service for MongoDB instances**
```shell
kubectl create -f mongodb-service.yaml
```
**Connect to the first node in interactive mode**
```shell
kubectl exec -it mongo-0 mongosh
```
**Set up a replicaset**
```bash
rs.initiate()
```
```bash
var cfg = rs.conf()
```
```bash
cfg.members[0].host="mongo-0.mongo:27017"
```
```bash
rs.reconfig(cfg)
```
```bash
rs.add("mongo-1.mongo:27017")
```
```bash
rs.add("mongo-2.mongo:27017")
```

**Check status**
```bash
rs.status()
```

### Test
**Connect to a new MongoDB instance**
```shell
kubectl run mongo --rm -it --image mongo -- sh
```
**Connect to the created replica set**
```shell
mongosh mongodb://mongo-0.mongo,mongo-1.mongo/graylog?replicaSet=rs0
```

## Graylog
In progress