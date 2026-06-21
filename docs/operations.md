# Operations

## Check cluster status

```bash
kubectl get nodes -o wide
kubectl get pods -A
kubectl top nodes
```

## Check sample application

```bash
kubectl get pods -n sample-app -o wide
kubectl get svc -n sample-app
kubectl get ingress -n sample-app
```

Test external access:

```bash
curl https://hello.<control-plane-public-ip>.sslip.io
```

## Check TLS certificate

```bash
kubectl get certificate -n sample-app
kubectl describe certificate hello-app-production-tls -n sample-app
```

Check the served certificate:

```bash
openssl s_client \
  -connect hello.<control-plane-public-ip>.sslip.io:443 \
  -servername hello.<control-plane-public-ip>.sslip.io \
  </dev/null 2>/dev/null | openssl x509 -noout -issuer -subject -dates
```

## Check monitoring

```bash
kubectl get pods -n monitoring
kubectl top nodes
```

## Access Grafana

Get the Grafana admin password:

```bash
kubectl get secret \
  --namespace monitoring \
  kube-prometheus-stack-grafana \
  -o jsonpath="{.data.admin-password}" | base64 --decode
echo
```

Start port-forwarding:

```bash
kubectl port-forward \
  --namespace monitoring \
  service/kube-prometheus-stack-grafana \
  3000:80
```

Open locally:

```text
http://localhost:3000
```

Login:

```text
Username: admin
Password: from Kubernetes Secret
```

## Firewall notes

UFW is enabled on both nodes.

The control-plane node allows:

* SSH
* HTTP and HTTPS
* Kubernetes API access from the worker node
* Flannel and kubelet traffic from the worker node

The worker node allows:

* SSH
* Flannel and kubelet traffic from the control-plane node

## Sensitive files

Do not commit:

* SSH private keys
* kubeconfig files
* k3s node token
* Grafana password
* Kubernetes Secret values
* Generated TLS Secrets

