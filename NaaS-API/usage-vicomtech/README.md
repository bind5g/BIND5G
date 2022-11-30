## Usage of NaaS-API

The following steps help you use the NaaS-API through the UI.
To navigate into the NaaS-API open your browser here:

    http://192.168.14.168:9191/v0/ui/#/ 

- [ ] Create a Tenant, by performing a **POST/tenant** request. Click on the `Try it out` button and fill the request body by giving a description and a name. The `description` value is a string and can be anything while describing in a few words the experiment to be performed. However, the `name` value has to be or contain one of the following:
 - If it contains the word `edge` or `Edge`, Edge, Application, and Telemetry Resources will be available for the Tenant.
 - If it contains the world `amarisoft` or `Amarisoft`, Radio Client, Radio Access Network, Core Network, and Telemetry Resources will be available for the Tenant.
 - If it contains the world `topology` or `Topology`, Radio Client, Radio Access Network, Core Network, and Telemetry Resources will be available for the Tenant.
 - If it contains the world fembms or FeMBMS, Radio Client, Radio Access Network, Core Network, and Telemetry Resources will be available for the Tenant.
 - If it contains `edge` or `Edge` and `amarisoft` or `Amarisoft` and `topology` or `Topology` and `fembms` or `FeMBMS`, then ALL the corresponding resources will be available to the Tenant.
 - If it contains `bind5g` Central-BIND5G-Thanos endpoint (URL) will be available in the GET/telemetry request
 
 The **POST/tenant** request results in the creation of the tenant with a `tenant_id`.
 
 - [ ] Create a mobile Core, by performing a **POST/core_network** request. Click on the `Try it out` button and fill the request body by giving first the corresponding `tenant_id` from the previous step. The `tenant_id` can be found either at the response of the **POST/tenant** request or by performing a **GET/tenant** request in the Tenant Resource section. Then, continue by filling in with the rest key-value pairs. Information about the key-value pairs can be found in the table below:
 
	| Name | Value type| Example| Comments
	|--|--|--|--|
	|  mode| *string* | "HoSA"| can be either "HoSA" or "Ho4g" or "5gSA" or "5gNSA" or "Slice"
	| type|*string*| "amarisoft" | must be "amarisoft"
	| extraParams|*{}*| - |extraParams is a json dictionary and is described in a seperate table (to be defined), can be left empty for the moment

The **POST/core_network** request results in the creation of the core network with a `core_id`.

 - [ ] Create a Radio Access Network (RAN), by performing a **POST/ran_infrastructure** request. Click on the `Try it out` button and fill the request body by giving first the corresponding `tenant_id` from the first step. Then add the `core_id` from the previous step. The `core_id` can be found either at the response of the **POST/core_network** request or by performing a **GET/core_network** request at the Core network Resource section. Then, continue by filling in with the rest key-value pairs. Information about the key-value pairs can be found in the table below:
	
	| Name | Value type| Example| Comments
	|--|--|--|--|
	|  mode| *string* | "HoSA"| can be either "HoSA" or "Ho4g" or "5gSA" or "5gNSA" or "Slice"
	| type|*string*| "amarisoft" | must be "amarisoft"
	| frequency| *integer*| 20| Units in MHz
	| bandwidth| *integer* |3900 | Units in Hz
	| extraParams|*{}*| - |extraParams is a json dictionary and is described in a seperate table (to be defined), can be left empty for the moment

The **POST/ran_infrastructure** request results in the creation of the gNB with a `ran_id`.

 - [ ] Create a Radio Client (UEs), by performing a **POST/client_infrastructure** request. Click on the `Try it out` button and fill the request body by giving first the corresponding `tenant_id` from the first step. Then, continue by filling in with the rest key-value pairs. Information about the key-value pairs can be found in the table below:

	| Name | Value type| Example| Comments
	|--|--|--|--|
	|  mode| *string* | "HoSA"| can be either "HoSA" or "Ho4g" or "5gSA" or "5gNSA" or "Slice"
	| type|*string*| "amarisoft" | must be "amarisoft"
	| frequency| *integer*| 20| Units in MHz
	| bandwidth| *integer* |3900 | Units in Hz
	| uenumber| *integer* |7| Total available number of UEs to be launched
	| extraParams|*{}*| - |extraParams is a json dictionary and is described in a seperate table (to be defined), can be left empty for the moment	

The **POST/client_infrastructure** request results in the creation of the client (aka UE) with a `client_id` and a `client_infrastructure_id`. However, the client(s) is offline. To make the client(s) active a **POST/client_infrastructure/{client_infrastructure_id}/action** request is required.

 - [ ] Update a Radio Client (UEs) such as change the offline mode to active running mode, by performing a **POST/client_infrastructure/{client_infrastructure_id}/action** request. Click on the `Try it out` button and fill first the `client_infrastructure_id` parameter. The `client_infrastructure_id` parameter can be found by performing a **GET/client_infrastructure** request with the corresponding `tenant_id` parameter of the first step. From the response, look for the world inside the parenthesis (). Then, continue by filling in with the rest key-value pairs. Information about the key-value pairs can be found in the table below:

	| Name | Value type| Example| Comments
	|--|--|--|--|
	|  actionParameters| {} | "ueID":"1"| activates the user with the id 1
	| actionType|*string*| "power_on" | "power_on" activates a UE and "power_off" deactivates a UE

 - [ ] Perform actions with the Radio Clients (UEs) such as downloading a file, checking the connection of an entity, transmitting IoT messages, and streaming videos by performing a **POST/client_infrastructure/{client_infrastructure_id}/action** request. Click on the `Try it out` button and fill first the `client_infrastructure_id` parameter. The `client_infrastructure_id` parameter can be found by performing a **GET/client_infrastructure** request with the corresponding `tenant_id` parameter of the first step. From the response, look for the world inside the parenthesis (). Then, continue by filling in with the rest key-value pairs. Information about the key-value pairs can be found in the table below:

	| Name | Value type| Example| Comments
	|--|--|--|--|
	|  actionParameters| {"ueID":"", params: "[]"} | "ueID":"1", params: "["ip:port"]"| `params` depends on the specific action
	| actionType|*string*| "start_traffic" | can be either "start_traffic" or "" or "" or "" (to be defined)

> NOTE: More information about the actionType can be found here.

 - [ ] Create Edge Resources, by performing a **POST/compute** request. Click on the `Try it out` button and fill the request body by giving first the corresponding `tenant_id` from the first step. Then, continue by filling in with the rest key-value pairs. Information about the key-value pairs can be found in the table below:

	| Name | Value type| Example| Comments
	|--|--|--|--|
	|  compute_type| *string* | "kubernetes"| can be any string, it is simply descriptive
	| cpus|*integer*| 1 | number of CPU cores
	| ram| *integer*| 100| Units in MB
	| storage| *integer* |1 | Units in GB
	| extraParams|*{}*| - |extraParams is a json dictionary and can be left empty.	

> NOTE: The Edge Resources create Kubernetes-based Namespaces in an already running Kubernetes cluster. The Infrastructure Provider can confirm it with the `kubectl get ns` command in the terminal of the MEC machine.

The **POST/compute** request results in the creation of the Edge resources with a `compute_id`. 

 - [ ] Create Application Resources, by performing a **POST/application_instance** request. Click on the `Try it out` button and fill the request body by giving first the corresponding `tenant_id` from the first step. Second, add the `compute_id` that has been generated from the previous step either by looking at the POST/compute response or by requesting a GET/compute specifying the tenant_id in the parameters section. Then, continue by filling in with the rest key-value pairs. Information about the key-value pairs can be found in the table below:

	| Name | Value type| Example| Comments
	|--|--|--|--|
	|  application_name| *string* | "hello-world"| must be a lowercase string giving the name of the application to be deployed
	| image_name|*string*| gcr.io/google-samples/node-hello:1.0 | image name from an online public container registry 
	| ports| *list with integers*| [80]| the container´s port to be exposed
	| extraParams|*{}*| - |extraParams is a json dictionary and is described in a seperate table below	

	Below you can find the `extraParams` dictionary with more details:

	| Name  |Value type | Example | Comments
	|--|--|--|--|
	| labels | *{}*  | {"app":"hello-world"}| more than one pain can be defined if necessary
	| replicas| *integer* | 1 | number of container replicas
	| env| *list*| [[{"name":"DEMO_GREETING"},{"value":"Hello World"}]]| environment variable for a container
	| service_type| *string*| "NodePort"| must be either ClusterIP or NodePort
	| service_ports| *list*| [[{"port": 80}, {"targetPort": 80}, {"name": "external"}, {"nodePort": 26379 }]] | Kubernetes-based Service ports. If service_type is ClusterID the "nodePort" pair is not needed. If service_type is NodePort and more than one ports are defined, not exposed ports must have "nodePort" : 0. If one of the ports need to be used in a servicemonitor manifest then it must be the first of the port list.
	| container_cpu_limit| *string* | "80m" | units in millicores
	| container_ram_limit| *string* | "50Mi" | units in Mebibytes
	| container_cpu_request| *string* | "40m" | units in millicores
	| container_ram_request|  *string* | "25Mi" | units in Mebibytes
	| servicemonitor | *{}*| {"enable": "True", "interval": "5s"} | for Prometheus exporter or Pushgateways to connect the metrics to a Prometheus server. parameter "labels" must have a key-value pair as follows: "release": "prometheus-bind5g-vicomtech"
	| helm_chart | *{}*| {"enable": "False", "kns_name": "", "nsd_name": "zookeeper_vicomtech_ns", "vim_name": "bind5g-vim", "k8s_namespace": ""} | Deployment of Helm-Charts, If "enable" is "True". kns_name value must be the same as the application_name parameters, and k8s_namespace value must be the same as the compute_id parameter. The nsd_name is the name of the network service package in the OSM
	| volumes | *{}*| {"volume_name": "kafka_metrics", "container_path": "/prometheus", "host_path": "/home/ubuntu/v3-cognitive-iot/prometheus-metrics-for-kafka/"} | Deployment of volumes, the folder to be mount in the container must exist in the Vicomtech´s Kubernetes Node machine

> NOTE: The application can contain only one container. Pairs of the extraParams that are not necessary can be deleted.
> NOTE: The Infrastructure Provider can confirm that the application has been deployed in the MEC machine with the `kubectl get pods -n compute_id` ,`kubectl get deploy -n compute_id`, `kubectl get service -n compute_id`, `kubectl get servicemonitor -n compute_id` commands in the terminal of the MEC machine.

The **POST/application_instance** request results in the creation of the application with an `application_id`. 

 - [ ] Get a list with all the applications associated with a specific Tenant, by performing a **GET/application_instance** request with the corresponding `tenant_id`. On the other hand, to read more information, from a specific application, perform a **GET/application_instance/{application_instance_id}**
request, by specifying in the parameters section the `tenant_id` and the `application_id`. The `application_id` can be found either from the response of the **POST/application_instance** or from the **GET/application_instance** request.

> NOTE: If you expose an application outside of the cluster and you want to navigate to it, use the Edge node IP and the application´s nodePort. The Edge node IP can be found in the response of a **GET/compute** request, by specifying only the `tenant_id`.

 - [ ] Update an Application such as scale the container either horizontal ( create more or less containers) or vertical (resize the container in terms of CPU and/or RAM), by performing a **POST/application_instance/{application_instance_id}/scale** request. Fill in the parameter application_instance_id, which can be found in the previous step, and the request body. Information about the key-value pairs of the body request can be found in the table below:
	| Name | Value type| Example| Comments
	|--|--|--|--|
	|  scaletype| *string* | "Horizontal"| must be either "Horizontal" or "Vertical"
	| replicas|*integer*| 4 |  number of container replicas 
	| new_cpu| *string*| "100m"| units in millicores
	| new_memory| *string* | "50Mi" | units in Mebibytes
	

> NOTE: If the type of the scaling is Horizontal the `new_cpu` and `new_memory` values can be left as empty strings like `""`. If the type of scaling is Vertical leave the `replicas` with the current number of containers the application has. Nevertheless, any other number you insert in the `replicas` will not affect the Vertical type of scaling. 

 - [ ] Get the Telemetry Resources such as monitoring and visualization tools, by performing a GET/ telemetry request. No parameters or body requests are needed for that. The response will give you the IP and the port the telemetry systems are running.
