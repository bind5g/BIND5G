# OSM Network Services
OSM to instantiate Network Services must have onboarded in its dashboard the kubernetes-network-function-descriptor package and the network-service-descriptor package. These packages are generated from the OSM descriptor files.

## OSM descriptors

 - [ ] To start preparing the OSM descriptors for an application (helm-chart) create a folder with the application name e.g.:
`mkdir zookeeper_vicomtech`
 - [ ] Then, create two subfolders one called zookeeper_vicomtech_knf and another zookeeper_vicomtech_ns
`cd zookeeper_vicomtech`
`mkdir zookeeper_vicomtech_knf`
`mkdir zookeeper_vicomtech_ns`

 - [ ] The OSM descriptors are divided into two categories:

 - the Kubernetes network function descriptor (knfd.yaml), for example for the zookeeper application the descriptor file look like this:
````
	vnfd:
		description: KNF descriptor with zookeeper chart
		df:
		- id: mgmt-df
		ext-cpd:
		- id: mgmtnet
			k8s-cluster-net: mgmt
		id: zookeeper_vicomtech_knf
		k8s-cluster:
				nets:
				- id: mgmtnet
		kdu:
		- helm-chart: Helm-Repo-BIND5G/zookeeper
			name: zookeeper_vicomtech
		mgmt-cp: mgmt
		product-name: zookeeper_vicomtech_knf
		version: '1.0'
````

> NOTE: Helm-Repo-BIND5G is the public Helm repository for the BIND5G project and zookeeper is the name of the chart

 - the network-service-descriptor (nsd.yaml), for example for the zookeeper application the descriptor file look like this:

````
	nsd:
		nsd:
		- description: Network Service for zookeeper_vicomtech KNF
			designer: Vicomtech-BIND5G
			df:
			- id: mgmt-df
				vnf-profile:
				- id: '1'
					virtual-link-connectivity:
					- constituent-cpd-id:
						- constituent-base-element-id: '1'
							constituent-cpd-id: mgmtnet
						virtual-link-profile-id: mgmtnet
					vnfd-id: zookeeper_vicomtech_knf
			id: zookeeper_vicomtech_ns
			name: zookeeper_vicomtech_ns
			version: '1.0'
			virtual-link-desc:
			- id: mgmtnet
				mgmt-network: 'true'
				vim-network-name: mgmt
			vnfd-id:
			- zookeeper_vicomtech_knf
````

Each descriptor must always be followed with a checksums.txt file.
Checksums.txt for zookeeper_vicomtech_knf:
````
575f5fc9b81230169c9c096f538d1636 zookeeper_vicomtech_knfd.yaml
````
Checksums.txt for zookeeper_vicomtech_ns:
````
3621b68a9cc2233f860eb5ac5c4fd71a zookeeper_vicomtech_nsd.yaml
````
    
 - [ ] After the descriptors have been created, we can validate their content with tghe following command:
`osm package-validate folder_name_knf` and
`osm package-validate folder_name_ns`
e.g.:
`osm package-validate zookeeper_vicomtech_knf`
`osm package-validate zookeeper_vicomtech_ns`

## OSM packages

 - [ ] Build the OSM packages with the following command, resulting to the creation of the .tar.gz files
`osm package-build folder_name_knf`
`osm package-build folder_name_ns`
e.g.:
`osm package-build zookeeper_vicomtech_knf` -> zookeeper_vicomtech_knf.tar.gz
`osm package-build zookeeper_vicomtech_ns`  -> zookeeper_vicomtech_ns.tar.gz

## OSM package onboarding

 - [ ] To onboard the OSM packages into OSM dashboard use the following command:
`osm nfpkg-create folder_name_knf.tar.gz`
`osm nspkg-create folder_name_ns.tar.gz`
e.g.:
`osm nfpkg-create zookeeper_vicomtech_knf.tar.gz`
`osm nspkg-create zookeeper_vicomtech_ns.tar.gz`

> NOTE: Notice the difference of nfpkg-create for knf and nspkg-create for ns

Now the packages are on OSM and can be instantiaated either from OSM dashboard, OSM cli or the NaaS-API

