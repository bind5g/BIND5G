# NaaS-API (Network as a Service API)

## Description

Network as a Service (NaaS) API, follows the OpenAPI specifications, is built by the swagger editor, and runs in a Python-FLASK type of server. The NaaS-API deploys remotely and automatically an end-to-end mobile and edge network with containerized applications, as well as performs Day-2 actions on the implemented infrastructure. 

Specifically, the NaaS-API deploys a 5G Core, a gNB, UEs, Virtual machines, a Prometheus server, and Kubernetes-related resources. To achieve this, the NaaS-API interacts transparently with backend APIs, such as the Amarisoft-API, the Topology-API, the FeMBMS-API, and the Edge-API to deploy Amarisoft mobile network, Virtual machine, 5G-broadcast, and Kubernetes resources, respectively. In addition, the NaaS-API is connected with an SQL database with two-fold roles, one to act as an inventory for specific GET requests and another as the actual database to hold the data for every time an experiment is executed.

More specifically, the experiments that can be conducted with the NaaS-API are divided into Tenants. Each Tenant or experiment with the tenant_id field is aligned with the aforementioned mobile network and edge resources.

 - Tenant Resources: Every experiment is associated with a Tenant. The Tenant information is saved in the database. The tenant_id is aligned with all the rest resources such as the Radio Client, the Radio Access Network, the Core network, the Edge, and the Application. When a Tenant DELETE request is made, first resources from the Radio client, the Radio Access Network, the Core Network, the Edge, and the Application part are released (deleted), and then finally the Tenant is deleted.
 - Radio Client Resources: The Radio Client resources are only Amarisoft UESIMBOX-based simulated UEs. If an experimenter wants to connect a 5G mobile phone, the experimenter has to do this manually.
 - Radio Access Network (RAN) - gNB resources: The Radio Access Network resources are physical Amarisoft gNB elements.
 - 5G Core Resources: The 5G Core Resources are physical Amarisoft core and open5Gs elements.
 - Edge Resources: The Edge resources are Kubernetes-based Namespaces in an already deployed and running Kubernetes cluster.
 - Application Resources: The Application resources are Kubernetes-based resources such as Pods, Deployments, Service, and ServiceMonitors. The deployed containers are being pulled from either public or private online container registries. 
 - Telemetry Resources: The Telemetry resources are a Thanos server, Prometheus Servers, and a Grafana.

The list of experiments that can be executed through the NaaS API are:

 - Virtual machine topologies
 - Handover between two (2) cells of a 5G network
 - Video streaming over a 5G network
 - IoT messaging over a 5G network
 - Container deployment & scaling, and access over a 5G network
 - Monitor and visualize an edge infrastructure and its applications
 - Monitor Amarisoft mobile network metrics such as Radio and Core metrics



