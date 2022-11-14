# Thanos in BIND5G Project

## What is Thanos
Thanos calls itself an open-source, highly available Prometheus setup with long-term storage capabilities (https://thanos.io/).
Simply, it is an open-source extension to Prometheus.

## Why Thanos in BIND5G project
To seamlessly transform existing Prometheus deployments in Vicomtech, I2T, and Tecnalia clusters into a unified monitoring system with unbounded historical data storage.

## Thanos main components
-   **Thanos Sidecar:** The main component of Thanos that runs alongside Prometheus, reading and archiving data on the object store. 
-   **Thanos Store:** An API gateway that sits on top of historical Prometheus data in an object storage bucket.
-   **Thanos Query:** An aggregator for query results from multiple sources, i.e. multiple Sidecar instances or other Thanos implementations

## Configuration to connect a cluster-based Prometheus server to a Thanos deployment
In order to connect a Prometheus Server to a Thanos implementation, Thanos Sidecar must be installed in each Kubernetes cluster-based Prometheus setup. Thanos Sidecar is the point of reference for Thanos Query.
Also, Thanos Sidecar must be configured appropriately to connect to an object store bucket, like MINIO, to read and archive data.
The following steps show the configuration that needs to be done in a helm-chart based Prometheus setup in a Kubernetes cluster.

 - [ ] Create an object-store-configuration.yaml file:
	````
	type: S3
	config:
	  bucket: thanos
	  endpoint: 192.168.14.191:10904
	  access_key: BIND5GKEY
	  secret_key: BIND5GSECRET
	  insecure: true
	```` 
	

> **Note**: Endpoint is the IP of the machine that Thanos is running. Thanos will be running in Vicomtech´s network. Therefore, this IP will be accessible only after the infrastructure federation.

 - [ ] Create a Kubernetes secret with the object storage account information (type, access_key and secret_key)
 `kubectl -n monitoring create secret generic thanos-objstore-config-bind5g --from-file=thanos.yaml=object-store-configuration.yaml`

> **Note**: In case there is not a namespace called `monitoring`, create one with `kubectl create ns monitoring`

 Deploy Prometheus Kube Stack with Thanos sidecar container, Thanos sidecar Service type as NodePort, and with the object store configuration from the previous steps:

 - [ ] Get Helm Repository Info:
 `helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update`

 - [ ] Create prometheus-with-sidecar-thanos-values.yaml file:
	 ````
	 grafana:
	  # add persistant volumes to grafana dashboards (optional)
	  persistence:
	    enabled: true
	    size: 10Gi
	  service:
	    type: NodePort
	prometheus:
	  service:
	    type: NodePort 
	  prometheusSpec: 
	    externalLabels:
	      edge: mec-k8s-vicomtech 
	      cluster: vicomtech-mec
	      project: bind5g
	    # enable thanos side-car container
	    thanos: 
	      image: "quay.io/thanos/thanos:v0.24.0"
	      objectStorageConfig: 
	        key: thanos.yaml
	        name: thanos-objstore-config-bind5g
	      version: v0.24.0
	  thanosService: 
	    enabled: true
	    type: NodePort
	    clusterIP: ""
	    thanosServiceMonitor:
	      enabled: true
	````

> NOTE: externalLabels are attached to a Prometheus server in order to be used later on by Thanos to filter the undergo Prometheus servers.

 - [ ] Change directory to the one where the prometheus-with-sidecar-thanos-values.yaml file is `cd path/prometheus-with-sidecar-thanos-values.yaml`
 
 - [ ] Install Helm Chart:
 `helm install [RELEASE_NAME] prometheus-community/kube-prometheus-stack -n monitoring -f prometheus-with-sidecar-thanos-values.yaml`
In case Prometheus is already running on the cluster and has been installed by the kube-prometheus-stack helm chart, it can be upgraded with the `helm upgrade` command and the prometheus-with-sidecar-thanos-values.yaml file. 
 `helm upgrade [RELEASE_NAME] prometheus-community/kube-prometheus-stack -n monitoring -f prometheus-with-sidecar-thanos-values.yaml`
Note, that any Grafana dashboard will be deleted if no persistance has been enabled. 
 - [ ] Finally, share the new Thanos Sidecar Service nodeIP/nodePort in order to be used later for the Thanos Query: 
 ` kubectl get service -n monitoring | grep thanos-discovery`

 

