# HA Elastic Cloud on Kubernetes

With Elastic Cloud on Kubernetes (ECK) you can extend the basic Kubernetes orchestration capabilities to easily deploy, secure, upgrade your Elasticsearch cluster, and much more.

Built on the Kubernetes Operator pattern, Elastic Cloud on Kubernetes (ECK) extends the basic Kubernetes orchestration capabilities to support the setup and management of Elasticsearch, Kibana, APM Server, Enterprise Search, Beats, Elastic Agent, and Elastic Maps Server on Kubernetes.

With Elastic Cloud on Kubernetes you can streamline critical operations, such as:

- Managing and monitoring multiple clusters
- Scaling cluster capacity and storage
- Performing safe configuration changes through rolling upgrades
- Securing clusters with TLS certificates
- Setting up hot-warm-cold architectures with availability zone awareness

### Test prerequisites
---
Follow these instructions to prepare minikube for Elasticsearch cluster installation with sufficient resources to run.

```bash
$ minikube start --cpus 5 --memory 9384 --driver docker --nodes 3 --network-plugin=cni --cni=calico 
```
you can use other cni(flannel, calico) and drivers(kvm2, vmware)

minikube addons:
```bash
$ minikube addons enable ingress
$ minikube addons enable metrics-server
```
ingress-nginx for kubernets ingress and layer7 loadbalancing and webserver

Check cluster status:
```bash
$ kubectl cluster-info
```
Kubernetes dashboard:
```bash
$ Minikube dashboard:
```


### Install Elastic Cloud CRDs and Operator on Kubernetes
---
Install custom resource definitions and the operator with its RBAC rules:
```bash
$ kubectl create -f https://download.elastic.co/downloads/eck/1.8.0/crds.yaml
$ kubectl apply -f https://download.elastic.co/downloads/eck/1.8.0/operator.yaml
```

Check crds:
```bash
$ kubectl get crds
$ kubectl get pods -n elastic-system

```

Monitor the operator logs:
```bash
$ kubectl -n elastic-system logs -f statefulset.apps/elastic-operator
```


### Deploy HA Elastic Cloud on Kubernetes
---
How to Deploy HA Elasticsearch cluster:
```bash
$ kubectl create secret generic aws-s3-keys --from-literal=access-key-id='<YOUR-ACCESS-KEY-ID>' --from-literal=access-secret-key='<YOUR-ACCESS-KEY-SECRET>'
$ kubectl create -f elasticsearch.yaml
```

The operator automatically creates and manages Kubernetes resources to achieve the desired state of the Elasticsearch cluster. It may take up to a few minutes until all the resources are created and the cluster is ready for use.

Check elasticsearch:
```bash
$ kubectl get elasticsearch
$ kubectl get pods --selector='elasticsearch.k8s.elastic.co/cluster-name=elasticsearch'
$ kubectl logs -f elasticsearch-es-default-0
$ kubectl get service elasticsearch-es-http
```
A default user named elastic is automatically created with the password stored in a Kubernetes secret:
```bash
$ PASSWORD=$(kubectl get secret quickstart-es-elastic-user -o go-template='{{.data.elastic | base64decode}}')
```
Health Check, From inside the Kubernetes cluster:
```bash
$ curl -u "elastic:$PASSWORD" -k "https://elasticsearch-es-http:9200"
```

From your local workstation, use the following command in a separate terminal:
```bash
$ kubectl port-forward service/quickstart-es-http 9200
$ curl -u "elastic:$PASSWORD" -k "https://localhost:9200" # --address='0.0.0.0'
```


How to deploy HA Kibana:
```bash
$ kubectl create -f kibana.yaml
```

Check kibana health:
```bash
$ kubectl get kibana
$ kubectl get pod --selector='kibana.k8s.elastic.co/name=elasticsearch'
```

A ClusterIP Service is automatically created for Kibana:
```bash
$ kubectl get service elasticsearch-kb-http
```

Use kubectl port-forward to access Kibana from your local workstation:
```bash
$ kubectl port-forward service/elasticsearch-kb-http 5601 # --address='0.0.0.0'
$ kubectl get secret elasticsearch-es-elastic-user -o=jsonpath='{.data.elastic}' | base64 --decode
```

Check Elasticsearc metadata and status:
```bash
$ curl -X GET -I -u "elastic:$PASSWORD" -k "https://localhost:9200/_cluster/health?wait_for_status=yellow&timeout=50s&pretty" -vvv
$ curl -X GET -u "elastic:$PASSWORD" -k "https://localhost:9200/_cluster/health?wait_for_status=yellow&timeout=50s&pretty"
```


### Upgrade Elastic search and kibana:
---
You can add and modify most elements of the original cluster specification provided that they translate to valid transformations of the underlying Kubernetes resources (e.g., existing volume claims cannot be downsized). The operator will attempt to apply your changes with minimal disruption to the existing cluster. You should ensure that the Kubernetes cluster has sufficient resources to accommodate the changes (extra storage space, sufficient memory and CPU resources to temporarily spin up new pods etc.).

### Elasticsearch and Kibana Stack Monitoring:
---
To visualize monitoring data from across the Elastic Stack. You can view health and performance data for Elasticsearch, Logstash, and Beats in real time, as well as analyze past performance.
To monitor Kibana itself and route that data to the monitoring cluster.

Deploys Metricbeat configured for Elasticsearch and Kibana Stack Monitoring and Filebeat using autodiscover. Deploys one monitored Elasticsearch cluster and one monitoring Elasticsearch cluster. You can access the Stack Monitoring app in the monitoring cluster’s Kibana.


You need other elasticsearch and kibana for monitoring data:
```bash
$ kubectl create -f monitoring/elasticsearch-monitoring.yaml
$ kubectl create -f monitoring/kibana-monitoring.yaml
```
How to deploy Metricbeat:
Collect metrics from Elasticsearch and Kibana. Metricbeat is a lightweight way to send system and service statistics.
```bash
$ kubectl create -f monitoring/metricbeat.yaml
```
How to deploy Filebeat:
In an ELK-based logging pipeline, Filebeat plays the role of the logging agent—installed on the machine generating the log files, tailing them, and forwarding the data to directly into Elasticsearch for indexing.
```
$ kubectl create -f monitoring/filebeat.yaml
```
How to deploy Heartbeat:
Monitor Elasticsearch and Kibana for their availability with active probing. Given a list of URLs, Heartbeat asks the simple question: Are you alive? Heartbeat ships this information and response time to the rest of the Elastic Stack for further analysis.
```bash
$ kubectl create -f monitoring/heartbeat.yaml
```

### Prometheus Exporter for Elasticsearch:
---
Prometheus exporter for various metrics about ElasticSearch, written in Go.
This chart creates an [Elasticsearch-Exporter](https://github.com/prometheus-community/elasticsearch_exporter) deployment on a Kubernetes cluster using the [Helm chart](https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus-elasticsearch-exporter).

Create  elsticsearch promethues exporter secret:
```bash
$ kubectl create secret generic elasticsearch-prometheus-exporter  --from-literal=ES_USER='<ES_USER>' --from-literal=ES_PASSWROD='<ES_PASSWROD>'
```
How to Deploy:
```bash
$ helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
$ helm repo update
$ helm install [RELEASE_NAME] prometheus-community/prometheus-elasticsearch-exporter

# Helm 3
$ helm install [RELEASE_NAME] prometheus-community/prometheus-el
# Helm 2
$ helm install --name [RELEASE_NAME] prometheus-community/prometheus-elasticsearch-exporter
```
### Trabelshooting and benchmarking
---
ue can use [Elasticsearch Stress](https://github.com/logzio/elasticsearch-stress-test) Test for benchmarking your cluster. And use [Elasticsearch For Beginners](https://github.com/oliver006/elasticsearch-hn) for sample client.
And you can find toolbox in the trabelshooting directory for network test and api-call.


### Roll-back and BackUp
---
Snapshot and Restore lets you back up a running Elasticsearch cluster using data and state snapshots. Snapshots are important because they provide a copy of your data in case something goes wrong. If you need to roll back to an older version of your data, you can restore a snapshot from the repository. you can find more data [here](https://www.elastic.co/guide/en/kibana/current/snapshot-repositories.html).

To set up automated snapshots for Elasticsearch on Kubernetes you have to:

- Ensure you have the necessary Elasticsearch storage plugin installed.
- Add snapshot repository credentials to the Elasticsearch keystore.
- Register the snapshot repository with the Elasticsearch API.
Set up a CronJob to take snapshots on a schedule.

you can take snapshot using backup cronjob in backup directory.
Check times and cridentianls, and run:
```bash
kubectl create backup/snaphotter.yaml
```

The only way to downgrade is to start up a new cluster and restore a snapshot taken before the upgrade. Elasticsearch does not support downgrading.

#### Feedback and Contributing
---
You are more then welcome! Please open a PR or issues here.
Or email me at armanaxh[!at]gmail.com
