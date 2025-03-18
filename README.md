### Deployment Guide

#### Kube Prometheus Stack

1. Make sure helm is installed on your machine ([Helm Installation](https://helm.sh/docs/intro/install/)) and your machine has access to the target kubernetes cluster.
2. Add "prometheus-community" helm repository.

   ```bash
   helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
   helm repo update
   ```

3. Install the `kube-prometheus-stack` chart first. This will bootstrap the below components;
   1. Prometheus Operator (1 Replica)
   2. Prometheus (3 Replicas)
   3. AlertManager (2 Replicas)
   4. Grafana (1 Replica)
   5. Node Exporter (as Daemonset)

    ```bash
    helm install [RELEASE_NAME] prometheus-community/kube-prometheus-stack -f kube-prometheus/kube-prometheus.override.yaml --namespace [NAMESPACE] --create-namespace
    ```

#### Blackbox Exporter

1. Before installing the Blackbox Exporter, ensure that any domain requiring a local host entry for resolution—especially those intended as Blackbox targets—is added as an override.

    ```yaml
    ## File: `./blackbox-exporter/blackbox-hostalias.override.yaml`
    hostAliases:
      - ip: "10.10.10.10"
        hostnames:
          - "example.com"
    ```

2. Install Blackbox exporter.

   ```bash
    helm install [RELEASE_NAME] prometheus-community/prometheus-blackbox-exporter \
        --namespace [NAMESPACE] \
        -f ./blackbox-exporter/blackbox-exporter.override.yaml \
        -f ./blackbox-exporter/blackbox-hostalias.override.yaml ## Only needed if you have added the file as part of step 01
   ```

#### Configure Openstack Targets

