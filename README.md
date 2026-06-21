# k8s-platform

Small Kubernetes platform on Verda Cloud for a Junior / Mid-Level Platform Engineer take-home assignment.

## What was built

* Two CPU virtual machines on Verda Cloud
* Two-node k3s Kubernetes cluster
* Traefik ingress controller
* metrics-server
* cert-manager with Let's Encrypt TLS
* Sample application exposed externally over HTTPS
* Prometheus and Grafana monitoring stack
* Basic Prometheus alert for sample application availability
* Basic read-only RBAC example
* Backup and restore notes

## Cluster

| Node            | Role              | Size               | OS           |
| --------------- | ----------------- | ------------------ | ------------ |
| control-plane-1 | k3s control plane | 4 vCPU / 16 GB RAM | Ubuntu 24.04 |
| worker-1        | k3s worker        | 4 vCPU / 16 GB RAM | Ubuntu 24.04 |

## Repository layout

| Path                             | Purpose                                             |
| -------------------------------- | --------------------------------------------------- |
| `kubernetes/sample-app/`         | Sample application Deployment, Service, and Ingress |
| `kubernetes/cert-manager/`       | Let's Encrypt ClusterIssuers                        |
| `kubernetes/monitoring/`         | Monitoring Helm values                              |
| `kubernetes/monitoring/alerts/`  | Basic Prometheus alert rules                        |
| `kubernetes/rbac/`               | Basic read-only RBAC example                        |
| `scripts/`                       | Installation scripts                                |
| `docs/`                          | Architecture, operations notes, backup notes, and   |
|                                  | screenshots                                         |

## Deployment commands

Install cert-manager:

```bash
./scripts/install-cert-manager.sh
kubectl apply -f kubernetes/cert-manager/
```

Deploy the sample application:

```bash
kubectl apply -f kubernetes/sample-app/
```

Install monitoring:

```bash
./scripts/install-monitoring.sh
```

## Access

The sample application is exposed through HTTPS:

```text
https://hello.<control-plane-public-ip>.sslip.io
```

Grafana is accessed through port-forwarding and is not exposed publicly.

## Security note

Secrets, kubeconfig files, SSH private keys, k3s node tokens, Grafana passwords, and generated TLS Secrets are not committed to this repository.

