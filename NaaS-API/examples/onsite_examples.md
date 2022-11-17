## Examples in one testbed

**Deploy Edge & Containerized Applications (Tenant + Edge + Application Resources)**

	
	 - [ ] Navigate to the NaaS-API UI with your preferred browser:
	 `http://192.168.14.168:9191/v0/ui`
	 - [ ] Create a Tenant: POST/tenant request
	 - [ ] Create Edge Resources: POST/compute request with the following body request:
		```
		{	
			"compute_description": {
				"compute_data": {
					"cpus": 1,
					"extraParams": {},
					"ram": 200,
					"storage": 1
				},
				"compute_type": "kubernetes"
			},
			"tenant_id": "123456789"
		}
		```

 - [ ] Create a web application in the Edge Resources: POST/application_instance request with the following body:

		```
		{
			"application_description": {
				"application_name": "nginx-test",
				"compute_id": "asdfghjkl",
				"extraParams": {
					"labels": {
						"app":"nginx-test"
						},
					"replicas" : 1,
					"service_type": "ClusterIP",
					"service_ports": [
						{"port": 8080},
						{"targetPort": 8080},
						{"name": "external"},
						]
					],
					"servicemonitor": {
						"enable": "False",
						"interval": ""
						},
					"env": [],
					"helm_chart": {
 						"enable": "False",
 						"kns_name": "",
 						"nsd_name": "",
 						"vim_name": "",
 						"k8s_namespace": ""
 							}
				},
				"image_name": "nginx",
				"ports": [8080]
			},
			"tenant_id": "123456789"
		}
		```

 **Monitor and scale containers running on the Edge (Tenant + Edge + Application + Telemetry Resources)**

 - [ ] First, follow the steps of the Deploy Edge & Containerized Applications example above. If you have already done the above steps, you can continue with the same `tenant_id`, `compute_id`, and `application_id`. If you are going to perform now the steps of the above test, a new `tenant_id`, `compute_id`, and `application_id` will be generated. Assuming we continue from the Deploy Edge & Containerized Applications Test.
 - [ ] Check that the application exists: GET/application_instance , GET/application_instance/{application_instance_id}
 - [ ] Monitor the application on a Prometheus Server and visualize container-related metrics in Grafana: GET/telemetry. Visit the Prometheus server at `http://192.168.14.211:30090` and Grafana at `http://192.168.14.211:23456` with the credentials from the request´s reply.

> NOTE: 192.168.14.211 is the IP of the running Kubernetes cluster that is associated with the NaaS-API.

 - [ ] Scale to four (4) replicas the web application: POST/application_instance/{application_instance_id}/scale with the following body request:
	 ```
	{
		"new_cpu": "",
		"new_memory": "",
		"replicas": 4,
		"scaletype": "Horizontal"
	}
	```
	

  **Connect a UE to a web-app running on the Edge over a 5G mobile network**

	 - [ ] Navigate to the NaaS-API UI with your preferred browser:
	 `http://192.168.14.168:9191/v0/ui`
	 - [ ] Create a Tenant: POST/tenant request
	 - [ ] Create  a Core Network: POST/core_network request with the following body request:
		```
		{
			"core_network_description": {
			"extraParams": {},
			"mode": "HoSA",
			"type": "amarisoft"
			},
			"tenant_id": ""
		}
		```

	- [ ] Create a Radio Access Network: POST/radio_infrastructure request with the following body request:
		 ```
		{
			"RAN_description": { 
				"bandwidth": 20,
				"core_id": "5b63089158f568073093f70d"
				"extraParams": {},
				"frequency": 3900,
				"mode": "HoSA",
				"type": "amarisoft"
				}
			"tenant_id": "123456789"
		}
		```

	 - [ ] Create a Radio Client: POST/client_infrastructure request with the following body request:
		```
		{
			"client_description": { 
				"bandwidth": 20,
				"extraParams": {},
				"frequency": 3900,
				"mode": "HoSA",
				"type": "amarisoft"
				"uenumber": 7
				}
			"tenant_id": "123456789"
		}
		```

	 - [ ] Add (activate) a UE from the Radio Client infrastructure: POST/client_infrastructure/{uesimbox}/action request with the following body request:
		 ```
		 {
			 "actionParameters": {"ueID":"1"},
			 "actionType": "power_on"
		}
		```

	 - [ ] Create Edge Resources: POST/compute request with the following body request:
		```
		{	
			"compute_description": {
				"compute_data": {
					"cpus": 1,
					"extraParams": {},
					"ram": 100,
					"storage": 1
				},
				"compute_type": "kubernetes"
			},
			"tenant_id": "123456789"
		}
		```

	 - [ ] Create a web application in the Edge Resources: POST/application_instance request with the following body:

		```
		{
			"application_description": {
				"application_name": "hello-kubernetes",
				"compute_id": "qwertyuiop",
				"extraParams": {
					"labels": {
						"app":"hello-kubernetes"
						},
					"replicas" : 1,
					"service_type": "NodePort",
					"service_ports": [
						{"port": 8080},
						{"targetPort": 8080},
						{"name": "external"},
						{ "nodePort": 26379 }
						]
					],
					"servicemonitor": {
						"enable": "False",
						"interval": ""
						},
					"env": [],
					"helm_chart": {
 						"enable": "False",
 						"kns_name": "",
 						"nsd_name": "",
 						"vim_name": "",
 						"k8s_namespace": ""
 							}
				},
				"image_name": "gcr.io/google-samples/node-hello:1.0",
				"ports": [8080]
			},
			"tenant_id": "123456789"
		}
		```

	 - [ ] Read the information of the created application and look for the IP and port (nodePort) to access it from your browser at `http://192.168.14.211:26379` or generate traffic outside of the cluster: GET/application_instance/{application_instance_id}, GET/compute requests.

	 - [ ] Test connection between UE and web-app by performing a wget request from the UE: POST/client_infrastructure/{uesimbox}/action request with the following body request:
		 ```
		{
				 "actionParameters": {"ueID":"1", "params": "['192.168.14.211:26379']", "type":"WebData"},
				 "actionType": "start_traffic"
		}
		```


