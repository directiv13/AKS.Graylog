# AKS.Graylog

## Nginx Ingress Controller
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
**Add Helm repository**
```shell
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```
**Install Helm chart**
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
**Create statefulset**
```shell
kubectl apply -f statefulset.yaml -n graylog
```
**Create service for Elasticsearch cluster**
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
**Create statefulset MongoDB**
```shell
kubectl create -f mongodb.yaml
```
**Create service for MongoDB instances**
```shell
kubectl create -f mongodb-service.yaml
```
**Connect to first node in interactive mode**
```shell
kubectl exec -it mongo-0 mongosh
```
**Set up replicaset**
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
rs.status()

### Test
**Connect to new MongoDB intance**
```shell
kubectl run mongo --rm -it --image mongo -- sh
```
**Connect to created replica set**
```shell
mongosh mongodb://mongo-0.mongo,mongo-1.mongo/graylog?replicaSet=rs0
```

## Graylog
In progress