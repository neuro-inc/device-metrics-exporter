# Deploying AMD Device Metrics Exporter

This guide outlines the steps to deploy the **AMD Device Metrics Exporter** on a Kubernetes cluster. This setup integrates the exporter with Prometheus and Grafana for monitoring AMD GPU metrics.

---

## Prerequisites

- **Kubernetes Cluster**: Ensure your cluster is running and accessible.
- **Prometheus**: Installed and configured on your cluster.
- **Grafana**: Installed and connected to Prometheus as a data source.
- **Helm**: Ensure Helm is installed (`v3.2.0` or later).
- **GPU Nodes**: Nodes with AMD GPUs configured and labeled for scheduling.

---

## Deployment Steps

### 1. Clone the Repository

Clone the **AMD Device Metrics Exporter** repository:

```bash
git clone git@github.com:neuro-inc/device-metrics-exporter.git
cd device-metrics-exporter
```

### 2. Apply the ConfigMap

The ConfigMap defines the metrics and labels to be exported by the Device Metrics Exporter. Apply it to your cluster:

```bash
kubectl apply -f example/configmap.yaml
```

### 3. Deploy the Exporter Using Helm

Use Helm to install the Device Metrics Exporter. This command deploys the exporter as a DaemonSet on GPU-enabled nodes:

```bash
helm install exporter ./helm-charts -n platform
```

Notes:
- Replace platform with your desired namespace.
- Ensure the namespace exists or use the --create-namespace flag.


### 4. Apply the ServiceMonitor

If you're using Prometheus Operator, apply the ServiceMonitor resource to enable Prometheus to scrape metrics from the Device Metrics Exporter:

```bash
kubectl apply -f example/service-monitor.yaml
```

### 5. Verify Metrics in Prometheus

Forward the Prometheus service to your local machine for testing:

```bash
kubectl port-forward svc/prometheus-prometheus 9090:9090 -n platform
```

- Open Prometheus in your browser: http://localhost:9090.
- Run the following PromQL queries to verify metrics:

```
gpu_power_usage
gpu_edge_temperature
gpu_used_vram
```

Ensure metrics are being collected from the exporter pods.


### 6. Import Grafana Dashboards

The repository includes pre-built Grafana dashboards. Import these into your Grafana instance:

- Open Grafana and log in.
- Navigate to Dashboards → + Import.
- Upload one of the JSON files from the grafana/ directory:
    - dashboard_gpu.json: GPU-specific metrics.
    - dashboard_node.json: Node-level GPU metrics.
    - dashboard_overview.json: Overview of GPU metrics.
    - dashboard_job.json: Job-specific metrics (if using Slurm integration).
- Select the Prometheus data source and click Import.

### Summary

By completing these steps, you will have:

- Deployed the AMD Device Metrics Exporter on GPU-enabled nodes.
- Integrated the exporter with Prometheus for metric collection.
- Visualized GPU metrics in Grafana using pre-built dashboards.

Resources
- Repository: [AMD Device Metrics Exporter](https://github.com/ROCm/device-metrics-exporter)
- Documentation: [Official Docs](https://dcgpu.docs.amd.com/projects/device-metrics-exporter/en/latest/installation/kubernetes-helm.html)


