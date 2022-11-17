## Examples between two testbeds

**Connect a UE to a web-app running on the Federated Edge over a 5G SA mobile network**

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
 
  - [ ] Navigate to the NaaS-API UI with your preferred browser:
	 `http://testbed2_ip:testbed2_port/v0/ui`
- [ ] Create Edge Resources: POST/compute request with the following body request:
