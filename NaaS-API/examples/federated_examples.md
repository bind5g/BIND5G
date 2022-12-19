## Examples between two testbeds

**Connect a UE to a web-app running on the Federated Edge over a 5G SA mobile network**

 SITE 1 (Vicomtech)
 
 - [ ] Navigate to the NaaS-API UI with your preferred browser:
	 `http://192.168.14.168:9191/v0/ui`
 - [ ] Create a Tenant: POST/tenant request
	```
	{
	  "description": "Example E2E BIND5G experiment ",
	  "name": "federated_edge_amarisoft_bind5g"
	}
	```
 - [ ] Create  a Core Network: POST/core_network request with the following body request:
	```
		{
			"core_network_description": {
			"extraParams": {},
			"mode": "5gSA",
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
				"mode": "5gSA",
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
				"mode": "5gSA",
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
 
 SITE 2 (I2T)
 
 - [ ] Use the following base URL for subsequent requests:
	 `http://10.98.6.188:5000`

 - [ ] Create a Tenant in the new site: POST/tenant request
	```
	{
	  "description": "Example E2E BIND5G experiment ",
	  "name": "federated_edge_amarisoft_bind5g"
	}
	```

 - [ ] Create Edge Resources: POST/compute request with the following body request, choosing the compute data:
   ```
	{
		"tenant_id": "987654321",
		"compute_description": {
			"compute_type": "osm",
			"compute_data": {"cpus": "9", "ram": "10240"}
		}
	}
   ```

 - [ ] Create Application Instance: POST/application_instance with the following body request, using the corresponding Helm Chart name in image_name, and using compute_id received from the above POST/compute request. Also include helm value overrides in extra_params. resources.limits, resources.cpu, requests.limits, and requests.cpu are mandatory in helm chart.
    ```
   	{
		"application_description": {
			"compute_id": "3",
			"image_name":"bind5g/chartname",
			"application_name":"myapp",
			"extra_params": {
				"replicaCount": 2,
				"service":{"type":"NodePort"},
				"resources":{"limits":{"cpu":"100m","memory":"0.1Gi"},"requests":{"cpu":"100m","memory":"0.1Gi"}}
			}
		}
	}
   ```
