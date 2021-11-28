https://github.com/helm/charts/tree/master/stable/elasticsearch-exporter
https://github.com/helm/charts/tree/master/stable/elasticsearch-exporter

https://artifacthub.io/packages/helm/cloudnativeapp/elasticsearch-exporter



# README
https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus-elasticsearch-exporter





kubectl get secret elasticsearch-es-elastic-user -o=jsonpath='{.data.elastic}' | base64 --decode; echo


kubectl create secret generic  aura-elasticsearch-prometheus-exporter  --from-literal=ES_USER='<ES_USER>' --from-literal=ES_PASSWROD='<ES_PASSWROD>'





# exporter
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm repo update

helm upgrade --install  aura-elasticsearch-exporter prometheus-community/prometheus-elasticsearch-exporter -f values.yaml


# Helm 3
$ helm install [RELEASE_NAME] prometheus-community/prometheus-elasticsearch-exporter

# Helm 2
$ helm install --name [RELEASE_NAME] prometheus-community/prometheus-elasticsearch-exporter


helm list 



choerodon/elasticsearch-exporter

handle elastic user pass on value! env
It shoud be a config
I can change the helm to  create Secret and read pass and user from there
becuase if config changed i don't want  deploy again  because of password

