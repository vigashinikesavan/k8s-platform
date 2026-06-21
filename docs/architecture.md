# Architecture

## Overview

The platform runs on two Verda Cloud CPU virtual machines.

* `control-plane-1`: k3s server and Kubernetes control plane
* `worker-1`: k3s worker node

Both nodes run Ubuntu 24.04 minimal images.

## Request flow

```text
User
  -> HTTPS
  -> Verda public IP
  -> Traefik Ingress
  -> hello-app Service
  -> hello-app Pods
```

## Kubernetes setup

k3s was used as the Kubernetes distribution. The cluster has one control-plane node and one worker node.

Traefik is used as the ingress controller. It was installed automatically by k3s.

The default k3s networking stack is used. Pod networking uses the `10.42.0.0/16` range and Service networking uses the `10.43.0.0/16` range.

## TLS

cert-manager is installed using Helm.

Let's Encrypt is used for TLS certificates. The staging issuer was tested first, then the application was switched to a production Let's Encrypt certificate.

The HTTP-01 challenge is solved through Traefik.

## Monitoring

Monitoring is installed using `kube-prometheus-stack`.

It provides:

* Prometheus
* Grafana
* Alertmanager
* kube-state-metrics
* node-exporter
* Prometheus Operator

Grafana is accessed using port-forwarding instead of being exposed to the public internet.

## Main choices

k3s was used because it is lightweight, quick to install, and suitable for a small two-node assignment cluster. It supports standard Kubernetes APIs while keeping setup overhead low.

Traefik was used because it is included with k3s and works well for simple HTTP and HTTPS ingress.

cert-manager was used to automate TLS certificate management with Let's Encrypt.

Prometheus and Grafana were installed through kube-prometheus-stack because it provides a practical monitoring setup with dashboards, metrics collection, and alerting components.

## Limitations

* The cluster has a single control-plane node, so it is not highly available.
* The nodes use public IP addresses because no separate private network option was visible during setup.
* SSH is initially open publicly and should be restricted to trusted source IPs for a longer-running environment.
* Backup and restore are documented only at a high level.

