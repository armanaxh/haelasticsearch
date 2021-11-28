
##### monitoring

# prometheus exporter


# elastic metricbeat


##   install
    curl -L -O https://raw.githubusercontent.com/elastic/beats/7.15/deploy/kubernetes/metricbeat-kubernetes.yaml



### proemtheus module on metricbeat
    https://www.elastic.co/guide/en/beats/metricbeat/6.5/metricbeat-module-prometheus.html




    https://www.elastic.co/guide/en/beats/metricbeat/master/metricbeat-metricset-prometheus-collector.html
    https://www.elastic.co/guide/en/beats/metricbeat/current/metricbeat-module-prometheus.html






# elastic beat
https://www.elastic.co/guide/en/beats/metricbeat/current/running-on-kubernetes.html



I think  in large scale k8s deamonSet for metrics beat is not good. we can handle some things with affinity. topology.


# change namespace
MetricBeat for elastic search not cluster

<!-- Load Kibana dashboardsedit -->
For deeper observability into your infrastructure, you can use the Metrics app and the Logs app in Kibana. For more details, see Metrics monitoring and Log monitoring.

https://www.elastic.co/guide/en/beats/metricbeat/current/load-kibana-dashboards.html


https://www.elastic.co/guide/en/cloud-on-k8s/1.8/k8s-beat-configuration-examples.html