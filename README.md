# AKS.Graylog

This repository contains resources to deploy a multi-node Graylog cluster. I strongly don't recommend to use this configuration for production environments due to security issues. 

### Prerequisites
Before using this repository you should have an existing AKS cluster.
| Tool | Version |
| ---- | :----: |
| Helm | >=3 |
| kubectl | >=1.27 |

### Recommended order for deploying

You can deploy these resources in any order, except Graylog. Graylog have to be the last deployed item, because it uses some configuration from previously deployed resources:

* GRAYLOG_MONGODB_URI
* GRAYLOG_ELASTICSEARCH_HOSTS
* External IP of the Load Balancer

### Result

In result you will get cluster that contains:

* 1 Nginx Ingress Controller
* 2 Graylog nodes
* 2 Elasticsearch nodes
* 2 MongoDB nodes in Replica Set