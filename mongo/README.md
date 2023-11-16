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