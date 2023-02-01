# Helm-chart deployment with NaaS

## Create a MEC application based on a helm-chart

###  Create a tenant with a  POST/tenant request

###### Request body
```
{
  "description": "Zookeeper deployment as a helm chart",
  "name": "zookeeper_helmchart_edge_bind5g"
}
```

###### Response body

```
{
  "description": "Zookeeper deployment as a helm chart",
  "name": "zookeeper_helmchart_edge_bind5g",
  "tenantID": "d60d0c2e-a166-11ed-b246-0242ac1a000a"
}
```

### Create edge resources with a POST/compute request
###### Request body
```
{
  "compute_description": {
    "compute_data": {
      "cpus": 1,
      "extraParams": {},
      "ram": 1024,
      "storage": 1
    },
    "compute_type": "kubernetes"
  },
  "tenant_id": "d60d0c2e-a166-11ed-b246-0242ac1a000a"
}
```

###### Response body
```
{
  "computeCPU": "1 cores",
  "computeID": "c04edc9c-a16d-11ed-82d9-0242ac1c000a",
  "computeRAM": "1024 MB",
  "computeStorage": "1 GB",
  "computeType": "kubernetes",
  "tenantID": "d60d0c2e-a166-11ed-b246-0242ac1a000a"
}
```

### Create an MEC application based on a helm-chart with the POST/application_instance request

|Parameter | Description | Example|
|--------- |-------------|--------|
|application_name  | Name of application deployed for OSM | zookeeper_vicomtech|
|kns_name  | It must be the same as the *application_name* | zookeeper_vicomtech|
|nsd_name  | Name of the NS descriptor in OSM | zookeeper_vicomtech_ns|
|vim_name  |VIM name for BIND5G project in OSM | bind5g-vim |
|k8s_namespace  |Namespace to deploy helm chart and must be the same as *compute_id*| c04edc9c-a16d-11ed-82d9-0242ac1c000a |


###### Request body
```
{
	"application_description": {
		"application_name": "zookeeper-v3",
		"compute_id": "c04edc9c-a16d-11ed-82d9-0242ac1c000a",
		"extraParams": {
			"helm_chart": {
				"enable": "True",
				"kns_name": "zookeeper-v3",
				"nsd_name": "zookeeper_vicomtech_ns",
				"vim_name": "bind5g-vim",
				"k8s_namespace": "c04edc9c-a16d-11ed-82d9-0242ac1c000a"
					}
		},
		"image_name": "digitalwonderland/zookeeper",
		"ports": [2181]
	},
	"tenant_id": "d60d0c2e-a166-11ed-b246-0242ac1a000a"
}
```
###### Response body
```
{
  "applicationID": "d623157a-a16e-11ed-a7b4-0242ac1c000a",
  "applicationName": "zookeeper-v3d6231462-a16e-11ed-a57b-0242ac1c000a",
  "computeID": "c04edc9c-a16d-11ed-82d9-0242ac1c000a",
  "imageName": "digitalwonderland/zookeeper",
  "port": "[2181]",
  "status": "helm chart deployed",
  "tenantID": "d60d0c2e-a166-11ed-b246-0242ac1a000a"
}
```

> NOTE: The Infrastructure Provider can check if the manifests of the chart have been deployed with the `kubectl get pods -n compute_id`, `kubectl get deployment -n compute_id`, `kubectl get servicemonitor -n compute_id` commands. If the deployment exists but the container is failing to be created or to start, delete the application and the edge resources and create new edge resources with more RAM. The compute_id in the above exampe is c04edc9c-a16d-11ed-82d9-0242ac1c000a.


### Upgrade (DAY2 actions) a MEC application based on a helm-chart and its values.yaml file
