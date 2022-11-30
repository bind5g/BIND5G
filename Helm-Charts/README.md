# Helm-charts

## Creation of charts and packages

 - [ ] Create a helm-chart with the following command:
  `helm create chart-name`
  e.g.
  `helm create zookeeper`
 - [ ] Create helm packages to be used in the helm repository, resulting in .tgz files
  `helm package chart-name`
  e.g.
  `helm package zookeeper` -> zookeeper-0.1.0.tgz 
 - [ ] Move the newly created packages to the Helm repository folder -> Helm-Repo-BIND5G
 - [ ] Change directory to Helm-Repo-BIND5G
 - [ ] Update the index.yaml file:
 `helm repo index .`

> NOTE: The chart names must not contain  the underscroll symbol "_"

