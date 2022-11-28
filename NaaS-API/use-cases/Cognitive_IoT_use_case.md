# IoT Cognitive Maintenance use case – Vicomtech

## Deployment of the use-case through the Vicomtech NaaS-API with Kubernetes-based manifests

 - [ ] Browse in the url of the NaaS-API:
http://192.168.14.168:9191/v0/ui/#/
 - [ ] Create a tenant (POST/tenant request) with the following body request:
	````
	{
	  "description": "Cognitive Iot use case",
	  "name": "v3_usecase_amarisoft_edge_bind5g"
	}
	````
	Response:
	````
	[
	  "tenantID: a7547b24-699e-11ed-9496-0242ac1d000a, name: v3_usecase_amarisoft_edge_bind5g, description: Cognitive Iot use case"
	]
	````
 - [ ] Create a 5G core (Core Network Resource, POST/core_network request) with the following body request:
	````
	{
	  "core_network_description": {
	    "extraParams": {},
	    "mode": "5gSA",
	    "type": "amarisoft"
	  },
	  "tenant_id": "a7547b24-699e-11ed-9496-0242ac1d000a"
	}
	````

	Response:
    ````
    "An Amarisoft Core (core_network_id: miniscale) has been deployed with 5gSA mode"
	````

 - [ ] Create a 5G RAN (Radio Access Network Resource, POST/ran_infrastructure request) with the following body request:
	````
	{
	  "RAN_description": {
	    "bandwidth": 20,
	    "core_id": "miniscale",
	    "extraParams": {},
	    "frequency": 3900,
	    "mode": "5gSA",
	    "type": "amarisoft"
	  },
	  "tenant_id": "a7547b24-699e-11ed-9496-0242ac1d000a"
	}
	````
	Response:
	````
	"An Amarisoft RAN (ran_infrastructure_id: miniscale) has been deployed with 5gSA mode"
	````

 - [ ] Create Edge resources (POST/compute request) with the following body request:
	````
	{
	  "compute_description": {
	    "compute_data": {
	      "cpus": 1,
	      "extraParams": {},
	      "ram": 3000,
	      "storage": 1
	    },
	    "compute_type": "kubernetes"
	  },
	  "tenant_id": "a7547b24-699e-11ed-9496-0242ac1d000a"
	}
	````
	Response:
	````
	[
	  "computeID: 4a0b826c-699f-11ed-b7f8-0242ac1d000a, computeType: kubernetes, computeCPU: 1cores , computeRAM: 3000MB , computeStorage: 1GB , tenantID: a7547b24-699e-11ed-9496-0242ac1d000a"
	]
	````

 - [ ] Create applications (POST/application_instance request) such as Zookeeper, Kafka, Pushgateway, and specific applications for the purpose of the use-case.

 - [ ] Create Zookeeper application with the following body request:
````
{
		"application_description": {
			"application_name": "zoo1",
			"compute_id": "4a0b826c-699f-11ed-b7f8-0242ac1d000a",
			"extraParams": {
				"labels": {
					"app": "zookeeper-1"
					},
				"replicas" : 1,
				"service_type": "ClusterIP",
				"service_ports": [[
					{"port": 2181},
					{"targetPort": 2181},
					{"name": "leader"}
					]
				      ],
				"servicemonitor": {
					"enable": "False",
					"interval": ""
					},
				"env": [[{"name":"ZOOKEEPER_ID"},{"value":"1"}],[{"name":"ZOOKEEPER_SERVER_1"},{"value":"zoo1"}]],
				"helm_chart": {
					"enable": "False",
					"kns_name": "",
					"nsd_name": "",
					"vim_name": "",
					"k8s_namespace": ""
						}
			},
			"image_name": "digitalwonderland/zookeeper",
			"ports": [2181]
		},
		"tenant_id": "a7547b24-699e-11ed-9496-0242ac1d000a"
	}
````

Response:
````
[
  "applicationID: 6f72e8e6-69a7-11ed-b03a-0242ac1d000a, applicationName: zoo1, imageName: digitalwonderland/zookeeper , computeID: 4a0b826c-699f-11ed-b7f8-0242ac1d000a , port: [2181] , tenantID: a7547b24-699e-11ed-9496-0242ac1d000a, status: application deployed"
]
````

 - [ ] Create Kafka broker application with the following body request:
````
{
		"application_description": {
			"application_name": "kafka-broker2",
			"compute_id": "4a0b826c-699f-11ed-b7f8-0242ac1d000a",
			"extraParams": {
				"labels": {
					"app": "kafka",
                                        "id": "0",
                                        "name": "kafka",
                                        "release": "prometheus-bind5g-vicomtech"
					},
				"replicas": 1,
                                "container_cpu_request": "250m",
                                "container_cpu_limit": "500m",
                                "container_ram_request": "1024Mi",
                                "container_ram_limit": "2048Mi",
				"service_type": "NodePort",
				"service_ports": [
                                        [
					{"port": 1234},
					{"targetPort": 1234},
					{"name": "metrics"},
                                        {"nodePort": 0}
				       ],
                                       [
					{"port": 9092},
					{"targetPort": 9092},
					{"name": "kafka-port-internal"},
                                        {"nodePort": 0}
				       ],
                                       [
                                        {"port": 29093},
					{"targetPort": 29093},
					{"name": "kafka-port-external"},
                                        {"nodePort": 29093}
                                       ]
				      ],
				"servicemonitor": {
					"enable": "True",
					"interval": "10s"
					},
                                "volumes": {
					"volume_name": "prometheus-metrics",
					"container_path": "/prometheus",
                                        "host_path": "/home/ubuntu/v3-cognitive-iot/prometheus-metrics-for-kafka"
					},
				"env": [
                                        [{"name":"KAFKA_LISTENERS"},{"value":"INTERNAL://:9092,mec://:29093"}],
                                        [{"name":"KAFKA_ADVERTISED_LISTENERS"},{"value":"INTERNAL://kafka-broker2:9092,mec://192.168.14.211:29093"}],
                                        [{"name":"KAFKA_LISTENER_SECURITY_PROTOCOL_MAP"},{"value":"INTERNAL:PLAINTEXT,mec:PLAINTEXT"}],
                                        [{"name":"KAFKA_INTER_BROKER_LISTENER_NAME"},{"value":"INTERNAL"}],
                                        [{"name":"KAFKA_ZOOKEEPER_CONNECT"},{"value":"zoo1:2181"}],
                                        [{"name":"KAFKA_BROKER_ID"},{"value":"0"}],
                                        [{"name":"KAFKA_CREATE_TOPICS"},{"value":"upsampling:4:1,f-extraction:4:1,ml:1:1"}],
                                        [{"name":"EXTRA_ARGS"},{"value":"-javaagent:/prometheus/jmx_prometheus_javaagent-0.15.0.jar=1234:/prometheus/kafka-broker.yml"}]
                                       ],
				"helm_chart": {
					"enable": "False",
					"kns_name": "",
					"nsd_name": "",
					"vim_name": "",
					"k8s_namespace": ""
						}
			},
			"image_name": "wurstmeister/kafka",
			"ports": [9092, 29093]
		},
		"tenant_id": "a7547b24-699e-11ed-9496-0242ac1d000a"
	}
````

Response:
````
[
  "applicationID: cabb16b8-69ac-11ed-b7ce-0242ac1d000a, applicationName: kafka-broker2, imageName: wurstmeister/kafka , computeID: 4a0b826c-699f-11ed-b7f8-0242ac1d000a , port: [9092, 29093] , tenantID: a7547b24-699e-11ed-9496-0242ac1d000a, status: application deployed"
]
````

 - [ ] Create Upsampling application with the following body request:
````
{
		"application_description": {
			"application_name": "upsampling",
			"compute_id": "4a0b826c-699f-11ed-b7f8-0242ac1d000a",
			"extraParams": {
				"labels": {
					"app": "upsampling"
					},
				"replicas" : 1,
				"service_type": "ClusterIP",
				"service_ports": [[
					{"port": 8080},
					{"targetPort": 8080},
					{"name": "upsampling"}
					]
				      ],
				"servicemonitor": {
					"enable": "False",
					"interval": ""
					},
				"env": [[{"name":"ENV"},{"value":"Docker"}]],
				"helm_chart": {
					"enable": "False",
					"kns_name": "",
					"nsd_name": "",
					"vim_name": "",
					"k8s_namespace": ""
						}
			},
			"image_name": "bind5g/vicomtech:upsampling_V1",
			"ports": [8080]
		},
		"tenant_id": "a7547b24-699e-11ed-9496-0242ac1d000a"
	}
````

Response:
````
[
  "applicationID: 77c66f76-69a8-11ed-860c-0242ac1d000a, applicationName: upsampling, imageName: bind5g/vicomtech:upsampling_V1 , computeID: 4a0b826c-699f-11ed-b7f8-0242ac1d000a , port: [8080] , tenantID: a7547b24-699e-11ed-9496-0242ac1d000a, status: application deployed"
]
````

 - [ ] Create Feature Extraction application with the following body request:
````
{
		"application_description": {
			"application_name": "feature-extraction",
			"compute_id": "4a0b826c-699f-11ed-b7f8-0242ac1d000a",
			"extraParams": {
				"labels": {
					"app": "feature-extraction"
					},
				"replicas" : 1,
				"service_type": "ClusterIP",
				"service_ports": [[
					{"port": 8080},
					{"targetPort": 8080},
					{"name": "feature-extraction"}
					]
				      ],
				"servicemonitor": {
					"enable": "False",
					"interval": ""
					},
				"env": [[{"name":"ENV"},{"value":"Docker"}]],
				"helm_chart": {
					"enable": "False",
					"kns_name": "",
					"nsd_name": "",
					"vim_name": "",
					"k8s_namespace": ""
						}
			},
			"image_name": "bind5g/vicomtech:feat_extraction_V1",
			"ports": [8080]
		},
		"tenant_id": "a7547b24-699e-11ed-9496-0242ac1d000a"
	}
````

Response:
````
[
  "applicationID: 8f0de990-69a8-11ed-9faa-0242ac1d000a, applicationName: feature-extraction, imageName: bind5g/vicomtech:feat_extraction_V1 , computeID: 4a0b826c-699f-11ed-b7f8-0242ac1d000a , port: [8080] , tenantID: a7547b24-699e-11ed-9496-0242ac1d000a, status: application deployed"
]
````

 - [ ] Create Machine Learning application with the following body request:
````
{
		"application_description": {
			"application_name": "mlmodel",
			"compute_id": "4a0b826c-699f-11ed-b7f8-0242ac1d000a",
			"extraParams": {
				"labels": {
					"app": "mlmodel"
					},
				"replicas" : 1,
				"service_type": "ClusterIP",
				"service_ports": [[
					{"port": 8080},
					{"targetPort": 8080},
					{"name": "mlmodel"}
					]
				      ],
				"servicemonitor": {
					"enable": "False",
					"interval": ""
					},
				"env": [[{"name":"ENV"},{"value":"Docker"}]],
				"helm_chart": {
					"enable": "False",
					"kns_name": "",
					"nsd_name": "",
					"vim_name": "",
					"k8s_namespace": ""
						}
			},
			"image_name": "bind5g/vicomtech:ml_V1",
			"ports": [8080]
		},
		"tenant_id": "a7547b24-699e-11ed-9496-0242ac1d000a"
	}
````

Response:
````
[
  "applicationID: a246869a-69a8-11ed-811b-0242ac1d000a, applicationName: mlmodel, imageName: bind5g/vicomtech:ml_V1 , computeID: 4a0b826c-699f-11ed-b7f8-0242ac1d000a , port: [8080] , tenantID: a7547b24-699e-11ed-9496-0242ac1d000a, status: application deployed"
]
````

 - [ ] Create Pushgateway application with the following body request:
````
{
		"application_description": {
			"application_name": "pushgateway",
			"compute_id": "4a0b826c-699f-11ed-b7f8-0242ac1d000a",
			"extraParams": {
				"labels": {
					"app": "pushgateway",
                                        "release": "prometheus-bind5g-vicomtech"
					},
				"replicas" : 1,
				"service_type": "ClusterIP",
				"service_ports": [[
					{"port": 9091},
					{"targetPort": 9091},
					{"name": "metrics"}
					]
				      ],
				"servicemonitor": {
					"enable": "True",
					"interval": "0s200ms"
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
			"image_name": "prom/pushgateway:v1.4.2",
			"ports": [9091]
		},
		"tenant_id": "a7547b24-699e-11ed-9496-0242ac1d000a"
	}
````

Response:
````
[
  "applicationID: fea2ddd0-69af-11ed-b503-0242ac1d000a, applicationName: pushgateway, imageName: prom/pushgateway:v1.4.2 , computeID: 4a0b826c-699f-11ed-b7f8-0242ac1d000a , port: [9091] , tenantID: a7547b24-699e-11ed-9496-0242ac1d000a, status: application deployed"
]
````

 - [ ] Get Telemetry resources (GET/telemetry request) with parameter: tenant_id: a7547b24-699e-11ed-9496-0242ac1d000a

Response:
````
{
  "Grafana_info": {
    "Credentials": {
      "password": "prom-operator",
      "username": "admin"
    },
    "Grafana_ip": "192.168.14.211",
    "Grafana_name": "prometheus-bind5g-vicomtech-grafana",
    "Grafana_port": 10591,
    "URL": "http://192.168.14.211:10591"
  },
  "Prometheus_info": {
    "Prometheus_ip": "192.168.14.211",
    "Prometheus_name": "prometheus-bind5g-vicomtec-prometheus",
    "Prometheus_port": 30090,
    "URL": "http://192.168.14.211:30090"
  }
}
````
