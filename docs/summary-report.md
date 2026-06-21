# Summary Report

## What was built

A small Kubernetes platform was built on Verda Cloud using two CPU virtual machines.

The platform includes:

* Two-node k3s Kubernetes cluster
* Traefik ingress controller
* metrics-server
* cert-manager with Let's Encrypt TLS
* Externally reachable sample application
* Prometheus and Grafana monitoring stack
* Basic Prometheus alert for sample application availability
* Basic read-only RBAC example
* Backup and restore notes

## Architecture

The cluster uses one control-plane node and one worker node.

```text
User
  -> HTTPS
  -> Verda public IP
  -> Traefik Ingress
  -> hello-app Service
  -> hello-app Pods
```

The sample application is exposed through an Ingress resource using an `sslip.io` hostname. TLS certificates are issued automatically by cert-manager using Let's Encrypt.

## What worked

The core platform components were successfully installed and verified:

* Both Kubernetes nodes are Ready.
* The sample application is reachable externally over HTTPS.
* cert-manager issued a trusted production Let's Encrypt certificate.
* metrics-server provides node metrics through `kubectl top nodes`.
* Prometheus and Grafana were installed successfully.
* Grafana dashboards show cluster resource metrics.
* A sample application availability alert was tested by scaling the app to zero replicas. Prometheus showed the alert as firing, and the alert cleared after restoring the deployment.
* A read-only ServiceAccount was created for the `sample-app` namespace. It can list pods but cannot delete them.

## What did not work or limitations

A few issues and tradeoffs came up during the setup:

* The Verda console did not show a separate private network or cloud firewall/security-group option during setup. Because of this, the nodes were created with public IP addresses and host-level UFW firewall rules were used to restrict Kubernetes traffic between the two nodes.
* Helm initially could not reach the Kubernetes cluster and tried to connect to `localhost:8080`. This happened because Helm did not automatically use the k3s kubeconfig. The issue was fixed by setting `KUBECONFIG=/etc/rancher/k3s/k3s.yaml`, and the install scripts were updated to use that path by default.
* Let's Encrypt staging certificates are not trusted by browsers. This was expected, so staging was used only to validate the ACME HTTP-01 flow before switching to the production issuer.
* Grafana is not exposed publicly. It is accessed using `kubectl port-forward`, which is safer for this small assignment but less convenient than a properly secured production access method.

Current limitations:

* The cluster has only one control-plane node, so it is not highly available.
* Backup and restore are documented but not automated.
* SSH is open publicly during setup and should be restricted to trusted source IPs for a longer-running environment.

## Security and operational considerations

UFW is enabled on both nodes. Kubernetes internal ports are restricted between the control-plane and worker node where possible.

Secrets and credentials are not committed to the repository. This includes SSH private keys, kubeconfig files, k3s node tokens, Grafana passwords, Kubernetes Secret values, and generated TLS Secrets.

Grafana is accessed through port-forwarding instead of being exposed directly to the internet.

## What would be improved with more time

With more time, I would improve the platform by:

* Adding a private network between the nodes
* Restricting SSH access to trusted source IPs
* Adding Argo CD for GitOps deployment
* Implementing automated scheduled backups and testing full restore
* Expanding alerts with notification routing to email, Slack, or another channel
* Extending RBAC with separate admin, developer, and viewer roles
* Using multiple control-plane nodes for high availability

