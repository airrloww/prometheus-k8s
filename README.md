## monitor a k8s cluster with prometheus

### create a k8s cluster:
    killercoda.com/playgrounds

### create monitoring namespace:
    kubectl create namespace monitoring

### install kube-prometheus-stack:
    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm repo update
    helm install prometheus prometheus-community/kube-prometheus-stack -n monitoring
    source: https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack 

### route the traffic of service/prometheus-kube-prometheus-prometheus to access prometheus UI
#### change the service clusterIP to NodePort:
    k edit service/prometheus-kube-prometheus-prometheus -n monitoring
- change type from ClusterIP to NodePort 
- add nodePort: 30111 under - name: http-web
- then open Traffic Port Accessor and go to port 30111

note: this will monitor the cluster itself and not the apps deployed.


## monitor an app running on the cluster
there's a Prometheus library available for most programming languages. The library will provide a /metrics endpoint, which Prometheus will scrape to collect the metrics.

### deploy an app to the cluster:
    kubectl -n default apply -f app.yaml

### configure a ServiceMonitor to enable prometheus to scrape the target:
    kubectl -n default apply -f servicemonitor.yaml

note 1: prometheus will look for label "release: prometheus", so add this label to all service monitors.

note 2: selector on ServiceMonitor should match the labels on the app service file, this tells ServiceMonitor exactly which service to scrape and which port if needed.

### route the traffic of service/g to access grafana UI
    k edit service/prometheus-grafana -n monitoring
- Change type from ClusterIP to NodePort 
- add nodePort: 30112 under - name: http-web
- then open Traffic Port Accessor and go to port 30112
- username: "admin" password: "prom-operator"