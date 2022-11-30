# Helm-chart deployment with NaaS

## Create a MEC application based on a helm-chart
 - [ ] Create a tenant with a  POST/tenant request
````
{
  "description": "Zookeeper deployment as a helm chart",
  "name": "zookeeper_helmchart_edge_bind5g"
}
````

Response:

````
[
  "tenantID: 24f05cec-6cc4-11ed-8693-0242ac190009, name: zookeeper_helmchart_edge_bind5g, description: Zookeeper deployment as a helm chart"
]
````

 - [ ] Create edge resources with a POST/compute request
````
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
  "tenant_id": "24f05cec-6cc4-11ed-8693-0242ac190009"
}
````

Response:
````
[
  "computeID: 506f18a8-6cc4-11ed-ad34-0242ac190009, computeType: kubernetes, computeCPU: 1cores , computeRAM: 512MB , computeStorage: 1GB , tenantID: 24f05cec-6cc4-11ed-8693-0242ac190009"
]
````

 - [ ] Create an MEC application based on a helm-chart with the POST/application_instance request:
````
{
		"application_description": {
			"application_name": "zookeeper-v3",
			"compute_id": "506f18a8-6cc4-11ed-ad34-0242ac190009",
			"extraParams": {
				"helm_chart": {
					"enable": "True",
					"kns_name": "zookeeper-v3",
					"nsd_name": "zookeeper_vicomtech_ns",
					"vim_name": "bind5g-vim",
					"k8s_namespace": "506f18a8-6cc4-11ed-ad34-0242ac190009"
						}
			},
			"image_name": "digitalwonderland/zookeeper",
			"ports": [2181]
		},
		"tenant_id": "24f05cec-6cc4-11ed-8693-0242ac190009"
	}
````

> NOTE: kns_name must be the same as the application name, nsd_name is the name of the NS descriptor in the OSM, vim_name is the VIM name for the BIND5G project in the OSM vim-list, which for the OSM-Vicomtech is bind5g-vim. k8s_namespace is the namespace to deploy the helm-chart and must be the same as the compute_id

Response:
````
[
  "applicationID: e8ad77c2-6ccb-11ed-8958-0242ac19000a, applicationName: zookeeper-v3, imageName: zookeeper_image , computeID: 506f18a8-6cc4-11ed-ad34-0242ac190009 , port: [8080] , tenantID: 24f05cec-6cc4-11ed-8693-0242ac190009, status: helm chart deployed"
]
````
> NOTE: The Infrastructure Provider can check if the manifests of the chart have been deployed with the `kubectl get pods -n compute_id`, `kubectl get deployment -n compute_id`, `kubectl get servicemonitor -n compute_id` commands. If the deployment exists but the container is failing to be created or to start, delete the application and the edge resources and create new edge resources with more RAM. The compute_id in the above exampe is 506f18a8-6cc4-11ed-ad34-0242ac190009 .


## Upgrade (DAY2 actions) a MEC application based on a helm-chart and its values.yaml file
