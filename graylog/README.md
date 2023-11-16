## Graylog Cluster
There are two nodes in the cluster: 1 master node and 1 slave node. If you want, you can configure more slave nodes using the same pattern.
**To install**
```shell
kubectl create -f . -n graylog
```
**Check logs**
_Master_
```shell
kubectl logs graylog-master-0 -n graylog -c graylog-master
```
_Slave_
```shell
kubectl logs graylog-slave-0 -n graylog -c graylog-slave
```
### To delete
```shell
kubectl delete -f . -n graylog
```