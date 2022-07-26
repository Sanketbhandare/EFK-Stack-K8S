## EFK-Stack-K8S
--------------------------------------------------------------------------------------------------------------------------------------------------------------------
EFK Monitoring stack installation on Minikube Cluster 


## Monitoring in Kubernetes Cluster
--------------------------------------------------------------------------------------------------------------------------------------------------------------------
When running multiple applications and services on a Kubernetes cluster, it is important to stream your application and Kubernetes cluster logs to one centralized logging infrastructure for easy log analysis.


## What is EFK Stack?
--------------------------------------------------------------------------------------------------------------------------------------------------------------------
**EFK** stands for Elasticsearch, Fluentd, and Kibana. EFK is open-source choice for the Kubernetes log aggregation and analysis.

**Elasticsearch** is a distributed & scalable search engine. Its primary work is to store logs and retrive logs from fluentd.

**Fluentd** is a log collection agent which support multiple data sources and output formats. It is unifying layer between systems that genrate log data and systems that store log data. It is managed by CNCF, so used widely in K8S environments.

**Kibana** is UI tool for querying, data visualization and dashboards. It is a query engine which allows you to explore your log data, build visualizations / dashboards, query-specific logs or indexed data to filter out information for analysing issues. Kibana Query Language (KQL) is used for querying elasticsearch data.


## Demonstration
--------------------------------------------------------------------------------------------------------------------------------------------------------------------
Clone the below repository in your workspace

  `git clone https://github.com/Sanketbhandare/EFK-Stack-K8S.git


## EFK Architecture
--------------------------------------------------------------------------------------------------------------------------------------------------------------------
![image](https://user-images.githubusercontent.com/13814979/180921222-ecffd05f-cdd3-4950-ba19-0c5c75c08e6f.png)

              Image Source: https://medium.datadriveninvestor.com/centralised-log-management-clm-using-efk-stack-and-docker-32b693df9432


## Setup in Kubernetes:
--------------------------------------------------------------------------------------------------------------------------------------------------------------------

  ### ElasticSearch 
  Components used --> StatefulSet, Headless Service, Storage Class, Persistent Volume 
  Usage --> To persist the log data and ElasticSearch Headless service is exposed for Fluentd & Kibana to connect it. 

  ### Fluentd
  Components used --> DaemonSet, ClusterRole, ClusterRoleBinding, ServiceAccount
  Usage --> Runs on all nodes in K8S Cluster to collect the container logs. It connects to ElasticSearch Service endpoint to forward the logs.

  ### Kibana
  Components used --> Deployment, Service
  Usage --> Connect to ElasticSearch service endpoint.


## Steps for rolling out EFK Stack
--------------------------------------------------------------------------------------------------------------------------------------------------------------------

### I. Setup ElasticSearch

  1. Create StorageClass for ElasticSearch Statefulset 
  2. Create Persistent Volume for ElasticSearch Statefulset 
  3. Create Headless Service for ElasticSearch Statefulset 
  4. Create ElasticSearch Statefulset 
  5. Verify the ElasticSearch Statefulset from master server

    kubectl port-forward es-cluster-0 9200:9200
    curl http://localhost:9200/_cluster/health/?pretty

### II. Setup Kibana

  1. Create Deployment for Kibana
  2. Create NodePort Service for Kibana to access the dashboard
  3. Verify Kibana deployment from master server

    kubectl port-forward <kibana-pod-name> 5601:5601
    curl http://localhost:5601/app/kibana

### III. Setup Fluentd 

  1. Create Service Account for Fluentd Daemonset
  2. Create ClusterRole for Fluentd Daemonset
  3. Create ClusterRoleBinding for Fluentd Daemonset
  4. Create Fluentd Daemonset ( using hostPath volumes)
  5. Create test job to verify Fluentd Setup from master server


## BEST EFK Practises 
--------------------------------------------------------------------------------------------------------------------------------------------------------------------
1. Elasticsearch uses heap memory extensively. So plan your setup accordingly.
2. Elasticsearch search indices fillup quickly. Make sure proper housekeeping automation is there for cleaning up old indices.
3. Frequently accessible indices can be placed in different nodes having better compute resources.
4. Indices can be archived to NFS, aws-s3 and restore when you need it.
5. Dedicated node for dedicated functionality is good for High Availability and fault tolerance.


## Reference Sources:
--------------------------------------------------------------------------------------------------------------------------------------------------------------------
1. https://devopscube.com/setup-efk-stack-on-kubernetes/
2. https://kubernetes.io/docs/concepts/storage/storage-classes/
3. https://kubernetes.io/docs/concepts/storage/persistent-volumes/
4. https://docs.fluentd.org/container-deployment/kubernetes
5. https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html
6. https://www.elastic.co/guide/en/kibana/current/introduction.html
7. https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/
8. https://www.linuxtechi.com/configure-nfs-persistent-volume-kubernetes/
