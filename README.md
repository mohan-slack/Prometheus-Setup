# Prometheus-Setup
Prometheus Setup real-time project

DESCRIPTION
Prometheus is currently being installed together with the cluster provisioning by default.

The package includes:

From kube-prometheus (Deployed to monitoring namespace) - https://github.com/mohan-slack/kube-prometheus

The Prometheus Operator - https://github.com/mohan-slack/prometheus-operator

Highly available Prometheus - https://prometheus.io/

Highly available Alertmanager - https://github.com/mohan-slack/alertmanager

Prometheus node-exporter - https://github.com/mohan-slack/node_exporter

Prometheus Adapter for Kubernetes Metrics APIs (Only for Resource Metrics) - https://github.com/mohan-slack/prometheus-adapter

kube-state-metrics - https://github.com/mohan-slack/kube-state-metrics

Grafana - https://grafana.com/

From K8S-Prometheus-Adapter (Deployed to custom-metrics namespace)

Custom Metrics Adapter - https://github.com/kubernetes-sigs/prometheus-adapter/tree/master/deploy

This deployment of Prometheus is configured to search for all available ServiceMonitors in all namespaces, thus nothing special is required to be changed, in order to get Prometheus to scrape the metrics, except for setting up the ServiceMonitor.

SETUP
The deployment files can be found in the cluster-configuration Ansible Playbook Repo (link).

Once prometheus is deployed, the team needs to prepare a ServiceMonitor YAML manifest that will select the App's service, so that Prometheus will be able to scrape the metrics from the app through the /metrics endpoint.

  Example YAML

  kind: ServiceMonitor
apiVersion: monitoring.coreos.com/v1
metadata:
  name: sample-app
  labels:
    app: sample-app
spec:
  selector:
    matchLabels:
      app: sample-app
      team: rps-demo
  endpoints: 
  - port: http

===========
If you have deployed the Service Monitor, but noticed that Prometheus is still dropping the target labels, check that the labels are correct, and check that the name of the port that is defined in the endpoint block matches that of the App deployment. E.g. If the name of the port in the App deployment manifest is port: web , this example ServiceMonitor will drop the target labels and not scrape the metrics, even though the app labels are correct.

NOTE:

Prior to running the Playbook, one must generate a TLS cert and create a YAML file to store the cert as a Secret using the gencert.sh script found in the k8s-prometheus folder, and store the YAML file in the same folder.

As per the instructions in this README,

Create a secret called cm-adapter-serving-certs with two values: serving.crt and serving.key. These are the serving certificates used by the adapter for serving HTTPS traffic. For more information on how to generate these certificates, see the auth concepts documentation in the apiserver-builder repository. The kube-prometheus project published two scripts gencerts.sh and deploy.sh to create the cm-adapter-serving-certs secret.
