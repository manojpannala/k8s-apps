# k8s-Observability — Kubernetes Observability & CI Stack

A small, self-contained set of Kubernetes manifests for spinning up an **observability + CI** sandbox:
- **Prometheus** for metrics
- **Grafana** for dashboards
- **Jaeger** for distributed tracing
- **Jenkins** for CI (with a sample `Jenkinsfile`)
- **Tracing demo** app to generate traces

> Folders included: `grafana/`, `prometheus/`, `jaeger/`, `jenkins/base/`, `tracing-demo/base/`, and a root `Jenkinsfile`.
> (See the repo tree.) 

## Prerequisites
- A Kubernetes cluster (k3s/kind/minikube)
- `kubectl` configured to talk to your cluster
- Optional: `helm` (if you prefer to manage addons separately)
- Optional: an ingress controller (Traefik/NGINX) — otherwise use NodePort

