# Examples in one testbed

## Deploy Edge & Containerized Applications (Tenant + Edge + Application Resources)

	
- Navigate to the NaaS-API UI with your preferred browser:

	 `http://192.168.14.168:9191/v0/ui`
	 
### Create a Tenant

####  POST/tenant request with the following body request

###### Request body
```
{
  "description": "Example Edge BIND5G experiment",
  "name": "test_edge_bind5g"
}
```
###### Response body

```bash
{
  "description": "Example Edge BIND5G experiment",
  "name": "test_edge_bind5g",
  "tenantID": "3ca51152-9725-11ed-afe7-0242ac1b000a"
}
```


### Create Edge Resources

#### POST/compute request with tenant_id from the response of the previous step and with the following body request:

###### Request body

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
	"tenant_id": "3ca51152-9725-11ed-afe7-0242ac1b000a"
}
```

###### Response body 

```bash
[
  {
    "computeCPU": "1 cores",
    "computeID": "8ada298a-9726-11ed-a802-0242ac1b000a",
    "computeRAM": "200 MB",
    "computeStorage": "1 GB",
    "computeType": "kubernetes",
    "tenantID": "3ca51152-9725-11ed-afe7-0242ac1b000a"
  }
]
```

### Create a web application in the Edge Resources

#### POST/application_instance 

The request with tenant_id from the response of the first step, compute_id from the repsonse of the POST/compute step, and with the following body:

###### Request body 
```
{
	"application_description": {
		"application_name": "nginx-test",
		"compute_id": "8ada298a-9726-11ed-a802-0242ac1b000a",
		"extraParams": {
			"labels": {
				"app":"nginx-test"
				},
			"replicas" : 1,
			"service_type": "ClusterIP",
			"service_ports": [[
				{"port": 8080},
				{"targetPort": 8080},
				{"name": "external"}
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
	"tenant_id": "3ca51152-9725-11ed-afe7-0242ac1b000a"
}
```

###### Response body 

```bash
[
  {
    "applicationID": "0e2f1008-9727-11ed-a939-0242ac1b000a",
    "applicationName": "nginx-test",
    "computeID": "8ada298a-9726-11ed-a802-0242ac1b000a",
    "imageName": "nginx",
    "port": "[8080]",
    "status": "application deployed",
    "tenantID": "3ca51152-9725-11ed-afe7-0242ac1b000a"
  }
]
```

### Monitor and scale containers running on the Edge (Tenant + Edge + Application + Telemetry Resources)

 - First, follow the steps of the Deploy Edge & Containerized Applications example above. If you have already done the above steps, you can continue with the same `tenant_id`, `compute_id`, and `application_id`. If you are going to perform now the steps of the above test, a new `tenant_id`, `compute_id`, and `application_id` will be generated. Assuming we continue from the Deploy Edge & Containerized Applications Test.
 - Check that the application exists:
 

#####  GET/application_instance 

Get information of all the applications deployed with the same tenant_id

###### Parameter

- tenant_id: The identification of the tenant used in this experiment 
```
tenant_id: 3ca51152-9725-11ed-afe7-0242ac1b000a
```
###### Response example

```
[
  {
    "Application Name": "nginx-test",
    "ApplicationID": "0e2f1008-9727-11ed-a939-0242ac1b000a",
    "Image Name": "nginx",
    "computeID": "8ada298a-9726-11ed-a802-0242ac1b000a",
    "port": "[8080]",
    "status": "application deployed",
    "tenantID": "3ca51152-9725-11ed-afe7-0242ac1b000a"
  }
]
```


##### GET/application_instance/{application_instance_id}

Get information about an specific application deployed

###### Parameter

- tenant_id: The identification of the tenant used in this experiment
- application_instance_id: The identification of the application deployed from which you want take the information

```
tenant_id: 3ca51152-9725-11ed-afe7-0242ac1b000a
application_instance_id: 0e2f1008-9727-11ed-a939-0242ac1b000a
```

###### Response example

```
{
  "Deployment_info": {
    "apiVersion": "apps/v1",
    "kind": "Deployment",
    "metadata": {
      "annotations": {
        "deployment.kubernetes.io/revision": "1"
      },
      "creationTimestamp": "2023-01-23T10:37:47+00:00",
      "generation": 1,
      "labels": {
        "app": "nginx-test"
      },
      "managedFields": [
        {
          "apiVersion": "apps/v1",
          "fieldsType": "FieldsV1",
          "fieldsV1": {
            "f:metadata": {
              "f:labels": {
                ".": {},
                "f:app": {}
              }
            },
            "f:spec": {
              "f:progressDeadlineSeconds": {},
              "f:replicas": {},
              "f:revisionHistoryLimit": {},
              "f:selector": {},
              "f:strategy": {
                "f:rollingUpdate": {
                  ".": {},
                  "f:maxSurge": {},
                  "f:maxUnavailable": {}
                },
                "f:type": {}
              },
              "f:template": {
                "f:metadata": {
                  "f:labels": {
                    ".": {},
                    "f:app": {}
                  }
                },
                "f:spec": {
                  "f:containers": {
                    "k:{\"name\":\"nginx-test\"}": {
                      ".": {},
                      "f:image": {},
                      "f:imagePullPolicy": {},
                      "f:name": {},
                      "f:ports": {
                        ".": {},
                        "k:{\"containerPort\":8080,\"protocol\":\"TCP\"}": {
                          ".": {},
                          "f:containerPort": {},
                          "f:protocol": {}
                        }
                      },
                      "f:resources": {
                        ".": {},
                        "f:limits": {
                          ".": {},
                          "f:cpu": {},
                          "f:memory": {}
                        },
                        "f:requests": {
                          ".": {},
                          "f:cpu": {},
                          "f:memory": {}
                        }
                      },
                      "f:terminationMessagePath": {},
                      "f:terminationMessagePolicy": {}
                    }
                  },
                  "f:dnsPolicy": {},
                  "f:restartPolicy": {},
                  "f:schedulerName": {},
                  "f:securityContext": {},
                  "f:terminationGracePeriodSeconds": {}
                }
              }
            }
          },
          "manager": "OpenAPI-Generator",
          "operation": "Update",
          "time": "2023-01-23T10:37:47+00:00"
        },
        {
          "apiVersion": "apps/v1",
          "fieldsType": "FieldsV1",
          "fieldsV1": {
            "f:metadata": {
              "f:annotations": {
                ".": {},
                "f:deployment.kubernetes.io/revision": {}
              }
            },
            "f:status": {
              "f:availableReplicas": {},
              "f:conditions": {
                ".": {},
                "k:{\"type\":\"Available\"}": {
                  ".": {},
                  "f:lastTransitionTime": {},
                  "f:lastUpdateTime": {},
                  "f:message": {},
                  "f:reason": {},
                  "f:status": {},
                  "f:type": {}
                },
                "k:{\"type\":\"Progressing\"}": {
                  ".": {},
                  "f:lastTransitionTime": {},
                  "f:lastUpdateTime": {},
                  "f:message": {},
                  "f:reason": {},
                  "f:status": {},
                  "f:type": {}
                }
              },
              "f:observedGeneration": {},
              "f:readyReplicas": {},
              "f:replicas": {},
              "f:updatedReplicas": {}
            }
          },
          "manager": "kube-controller-manager",
          "operation": "Update",
          "time": "2023-01-23T10:37:55+00:00"
        }
      ],
      "name": "nginx-test",
      "namespace": "bbe4cf24-9b09-11ed-996e-0242ac13000a",
      "resourceVersion": "24524887",
      "uid": "bd2a7dcb-af6c-439d-bf58-933aec1f07f0"
    },
    "spec": {
      "progressDeadlineSeconds": 600,
      "replicas": 1,
      "revisionHistoryLimit": 10,
      "selector": {
        "matchLabels": {
          "app": "nginx-test"
        }
      },
      "strategy": {
        "rollingUpdate": {
          "maxSurge": "25%",
          "maxUnavailable": "25%"
        },
        "type": "RollingUpdate"
      },
      "template": {
        "metadata": {
          "labels": {
            "app": "nginx-test"
          }
        },
        "spec": {
          "containers": [
            {
              "image": "nginx",
              "imagePullPolicy": "Always",
              "name": "nginx-test",
              "ports": [
                {
                  "containerPort": 8080,
                  "protocol": "TCP"
                }
              ],
              "resources": {
                "limits": {
                  "cpu": "0",
                  "memory": "0"
                },
                "requests": {
                  "cpu": "0",
                  "memory": "0"
                }
              },
              "terminationMessagePath": "/dev/termination-log",
              "terminationMessagePolicy": "File"
            }
          ],
          "dnsPolicy": "ClusterFirst",
          "restartPolicy": "Always",
          "schedulerName": "default-scheduler",
          "securityContext": {},
          "terminationGracePeriodSeconds": 30
        }
      }
    },
    "status": {
      "availableReplicas": 1,
      "conditions": [
        {
          "lastTransitionTime": "2023-01-23T10:37:55+00:00",
          "lastUpdateTime": "2023-01-23T10:37:55+00:00",
          "message": "Deployment has minimum availability.",
          "reason": "MinimumReplicasAvailable",
          "status": "True",
          "type": "Available"
        },
        {
          "lastTransitionTime": "2023-01-23T10:37:47+00:00",
          "lastUpdateTime": "2023-01-23T10:37:55+00:00",
          "message": "ReplicaSet \"nginx-test-9c474cd65\" has successfully progressed.",
          "reason": "NewReplicaSetAvailable",
          "status": "True",
          "type": "Progressing"
        }
      ],
      "observedGeneration": 1,
      "readyReplicas": 1,
      "replicas": 1,
      "updatedReplicas": 1
    }
  },
  "ServiceMonitor_info": "Application ServiceMonitor resources do not exist",
  "Service_info": {
    "apiVersion": "v1",
    "kind": "Service",
    "metadata": {
      "creationTimestamp": "2023-01-23T10:37:47+00:00",
      "labels": {
        "app": "nginx-test"
      },
      "managedFields": [
        {
          "apiVersion": "v1",
          "fieldsType": "FieldsV1",
          "fieldsV1": {
            "f:metadata": {
              "f:labels": {
                ".": {},
                "f:app": {}
              }
            },
            "f:spec": {
              "f:ports": {
                ".": {},
                "k:{\"port\":8080,\"protocol\":\"TCP\"}": {
                  ".": {},
                  "f:name": {},
                  "f:port": {},
                  "f:protocol": {},
                  "f:targetPort": {}
                }
              },
              "f:selector": {
                ".": {},
                "f:app": {}
              },
              "f:sessionAffinity": {},
              "f:type": {}
            }
          },
          "manager": "OpenAPI-Generator",
          "operation": "Update",
          "time": "2023-01-23T10:37:47+00:00"
        }
      ],
      "name": "nginx-test",
      "namespace": "bbe4cf24-9b09-11ed-996e-0242ac13000a",
      "resourceVersion": "24524853",
      "uid": "34f15eef-29ea-4efe-adbd-b1cdab8fe6ee"
    },
    "spec": {
      "clusterIP": "10.102.105.163",
      "clusterIPs": [
        "10.102.105.163"
      ],
      "ports": [
        {
          "name": "external",
          "port": 8080,
          "protocol": "TCP",
          "targetPort": 8080
        }
      ],
      "selector": {
        "app": "nginx-test"
      },
      "sessionAffinity": "None",
      "type": "ClusterIP"
    },
    "status": {
      "loadBalancer": {}
    }
  }
}

```

 - Monitor the application on a Prometheus Server and visualize container-related metrics in Grafana: GET/telemetry. Visit the Prometheus server at `http://192.168.14.211:30090` and Grafana at `http://192.168.14.211:3000` with the credentials from the request´s reply.
 
 > NOTE: 192.168.14.211 is the IP of the running Kubernetes cluster that is associated with the NaaS-API.
 
###### Parameter

```
tenant_id: 3ca51152-9725-11ed-afe7-0242ac1b000a
```
###### Response

```
{
  "Grafana_info": {
    "Credentials": {
      "password": "prom-operator",
      "username": "admin"
    },
    "Grafana_ip": "192.168.14.211",
    "Grafana_name": "prometheus-bind5g-vicomtech-grafana",
    "Grafana_port": 3000,
    "URL": "http://192.168.14.211:3000"
  },
  "Prometheus_info": {
    "Prometheus_ip": "192.168.14.211",
    "Prometheus_name": "prometheus-bind5g-vicomtec-prometheus",
    "Prometheus_port": 30090,
    "URL": "http://192.168.14.211:30090"
  },
  "Thanos_info": {
    "Thanos_ip": "192.168.14.191",
    "Thanos_port": "10904",
    "URL": "http://192.168.14.191:10904"
  }
}
```
 
 -  Scale to four (4) replicas the web application: 
 
 POST/application_instance/{application_instance_id}/scale with the following body request:
	 ```
	{
		"new_cpu": "",
		"new_memory": "",
		"replicas": 4,
		"scaletype": "Horizontal"
	}
	```
	or

 - Scale CPU and memory the web application: 
 
 POST/application_instance/{application_instance_id}/scale with the following body request:
	```
	{
		"new_cpu": "10m",
		"new_memory": "5Mi",
		"replicas": 0,
		"scaletype": "Vertical"
	}
	```
	

### Connect a UE to a web-app running on the Edge over a 5G SA mobile network**

 - [ ] Navigate to the NaaS-API UI with your preferred browser:
	 `http://192.168.14.168:9191/v0/ui`
	 
####  Create a Tenant
##### POST/tenant 

###### Request body
```
{
  "description": "Example E2E BIND5G experiment ",
  "name": "edge_amarisoft_bind5g"
}
```
###### Response body
```
{
  "description": "Example E2E BIND5G experiment",
  "name": "test_edge_amarisoft_bind5g",
  "tenantID": "61d975f0-9b1e-11ed-8421-0242ac13000a"
}
```
#### Create  a Core Network

##### POST/core_network request with the tenant_id of the response of the previous step and with the following body request

###### Request body
```
{
	"core_network_description": {
	"extraParams": {},
	"mode": "5gSA",
	"type": "amarisoft"
	},
	"tenant_id": "61d975f0-9b1e-11ed-8421-0242ac13000a"
}
```
###### Response body
```
{
  "core_network_id": "smallscale",
  "info": "An Amarisof Core has been deployed",
  "mode": "5gSA"
}
```
#### Create a Radio Access Network
##### POST/radio_infrastructure request with the tenant_id of the first step and with the following body request:

###### Request body
```
{
	"RAN_description": { 
		"bandwidth": 20,
		"core_id": "smallscale",
		"extraParams": {},
		"frequency": 3900,
		"mode": "5gSA",
		"type": "amarisoft"
		},
	"tenant_id": "61d975f0-9b1e-11ed-8421-0242ac13000a"
}
```
###### Response body
```
{
  "info": "An Amarisoft RAN has been deployed",
  "mode": "5gSA",
  "ran_infrastructure_id": "smallscale"
}

```


#### Create a Radio Client
##### POST/client_infrastructure request with the tenant_id of the first step and with the following body request:

###### Request body
```
{
	"client_description": { 
		"bandwidth": 20,
		"extraParams": {},
		"frequency": 3900,
		"mode": "5gSA",
		"type": "amarisoft",
		"uenumber": 7
		},
	"tenant_id": "61d975f0-9b1e-11ed-8421-0242ac13000a"
}
```

###### Response body
```
{
  "Client_mode": "5gSA",
  "client_infrastructure_id": "uesimbox",
  "info": "An Amarisoft UE has been deployed"
}
```

#### Add (activate) a UE from the Radio Client infrastructure
##### POST/client_infrastructure/{uesimbox}/action request with the following body request:
###### Request body
```
 {
	 "actionParameters": {"ueID":"1"},
	 "actionType": "power_on"
}
```
###### Response body 
```
{
  "info": "UE power on",
  "ueID": "1"
}
```
 
#### Create Edge Resources
##### POST/compute request with the tenant_id of the first step and with the following body request
###### Request body
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
	"tenant_id": "61d975f0-9b1e-11ed-8421-0242ac13000a"
}
```

###### Response body
```
[
  {
    "computeCPU": "1 cores",
    "computeID": "17d4ff80-9b2a-11ed-9b01-0242ac130009",
    "computeRAM": "100 MB",
    "computeStorage": "1 GB",
    "computeType": "kubernetes",
    "tenantID": "61d975f0-9b1e-11ed-8421-0242ac13000a"
  }
]
```

#### Create a web application in the Edge Resources
##### POST/application_instance request with the compute_id of the previous step, tenant_id from the first step and with the following body:
###### Request body
```
{
	"application_description": {
		"application_name": "hello-kubernetes",
		"compute_id": "17d4ff80-9b2a-11ed-9b01-0242ac130009",
		"extraParams": {
			"labels": {
				"app":"hello-kubernetes"
				},
			"replicas" : 1,
			"service_type": "NodePort",
			"service_ports": [[
				{"port": 8080},
				{"targetPort": 8080},
				{"name": "external"},
				{"nodePort": 26378 }
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
	"tenant_id": "61d975f0-9b1e-11ed-8421-0242ac13000a"
}
```
###### Response body
```
[
  {
    "applicationID": "2b914962-9b2e-11ed-bbe1-0242ac130009",
    "applicationName": "hello-kubernetes",
    "computeID": "17d4ff80-9b2a-11ed-9b01-0242ac130009",
    "imageName": "gcr.io/google-samples/node-hello:1.0",
    "port": "[8080]",
    "status": "application deployed",
    "tenantID": "61d975f0-9b1e-11ed-8421-0242ac13000a"
  }
]
```
####  Read the information of the created application 
##### GET/application_instance/{application_instance_id}, GET/compute requests.

Look for the IP and port (nodePort) to access it from your browser at `http://192.168.14.211:26379` 


####  Test connection between UE and web-app by performing a wget request from the UE
##### POST/client_infrastructure/{uesimbox}/action request with the following body request
###### Request body
```
{
	 "actionParameters": {"ueID":"1", "params": "['192.168.14.211:26378']", "type":"WebData"},
	 "actionType": "start_traffic"
}
```
###### Response body
```
{
  "info": "UE starts transmitting traffic",
  "trafficID": "78240379",
  "trafficType": "WebData"
}
```


