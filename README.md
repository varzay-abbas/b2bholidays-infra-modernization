# B2B Infrastructure Modernization - DevOps Approach

## 📌 Overview

### Architectural Diagram
![Diagram](screenshots/Modern-Architectur-N-Existing.png)

This repository contains the complete implementation of a secure and observable microservices platform for B2BHolidays, built as part of the Senior DevOps Engineer technical assessment. It showcases a service mesh architecture deployed using lightweight Kubernetes (K3s), Cilium with WireGuard encryption, and a fully containerized multi-node simulation.

---

## 🔧 Technologies & Tools

* **Kubernetes Distribution:** K3s (via k3d)
* **Service Mesh:** Cilium + Envoy
* **Encryption:** WireGuard
* **Observability:** Hubble (Relay + UI)
* **Container Runtime:** containerd
* **Ingress:** Traefik (future-ready)
* **CI/CD (Optional Extension):** GitHub Actions / ArgoCD-ready

---

## 🧩 Architecture Summary

### VLAN-Based Node Simulation:

| VLAN Zone     | Node (Container) | Purpose                     |
| ------------- | ---------------- | --------------------------- |
| Application   | sender-node      | API request handler         |
| Application   | fetcher-node     | External data fetch (x2)    |
| Application   | processor-node   | Data transformation         |
| Edge          | ingress-node     | Load balancer & entrypoint  |
| Legacy / Edge | legacy-node      | PHP legacy web application  |
| Database      | db-node          | Simulated data storage node |
| Management    | mgmt-node        | Hubble UI + admin tools     |
| Control Plane | k3s-master       | K3s master node             |

---

## ✅ Implemented Features

* [x] **K3s cluster** deployed with k3d (3 nodes)
* [x] **Cilium** installed with Helm and WireGuard encryption
* [x] **Kube Proxy Replacement** enabled for optimized routing
* [x] **mTLS** enforced via Envoy sidecars and WireGuard tunnels
* [x] **Hubble Relay + UI** installed and reachable
* [x] **Cilium Network Policies** applied (default deny + allow rules)
* [x] **Mock Services** deployed per required components
* [x] **Namespace Segmentation** into app, db, edge, legacy

---

## 🚀 Getting Started

### 1. Start the Cluster

```bash
k3d cluster create b2bholidays-cluster --agents 2 --servers 1
```

### 2. Install Cilium with WireGuard

```bash
helm install cilium cilium/cilium \
  --version 1.17.3 \
  --namespace kube-system \
  --create-namespace \
  --set kubeProxyReplacement=true \
  --set enableK8sEndpointSlice=true \
  --set encryption.enabled=true \
  --set encryption.type=wireguard \
  --set hubble.enabled=true \
  --set hubble.relay.enabled=true \
  --set hubble.ui.enabled=true \
  --set operator.replicas=1
```

### 3. Deploy Mock Services & Policies

```bash
kubectl apply -f k3s/
```

### 4. Access Hubble UI

```bash
kubectl -n kube-system port-forward svc/hubble-ui 12000:80
# Then visit http://localhost:12000
```

### 5. Access Hubble Relay (Separate Terminal)

```bash
kubectl -n kube-system port-forward svc/hubble-relay 4245:80
```

---

## 🔐 Enterprise Security Alignment

* **Encrypted Service Communication:** All internal communication between services is encrypted using WireGuard tunnels.
* **Zero Trust Architecture:** Cilium policies restrict east-west traffic by default and only allow explicit service-to-service communication.
* **Observability:** Full flow visibility using Hubble Relay and UI.
* **Compliance Ready:** Network segmentation, audit-friendly service visibility, and mTLS enforcement create a production-ready baseline.

---

## 📁 Directory Structure

```
k3s/
├── ingress/
│   └── ingress-rules.yaml
├── namespaces/
│   ├── app-namespace.yaml          # sender, fetcher, processor
│   ├── db-namespace.yaml           # redis, databases
│   ├── edge-namespace.yaml         # ingress-related
│   └── legacy-namespace.yaml       # php legacy
├── network-policies/
│   ├── restrict-traffic.yaml       # default deny all
│   ├── allow-fetcher-processor.yaml
│   ├── allow-processor-redis.yaml
│   └── allow-sender-fetcher.yaml
├── services/
│   ├── fetcher/
│   │   ├── deployment.yaml
│   │   └── service.yaml
│   ├── legacy/
│   │   ├── deployment.yaml
│   │   └── service-nodeport.yaml
│   ├── processor/
│   │   ├── deployment.yaml
│   │   └── service.yaml
│   ├── redis/
│   │   ├── deployment.yaml
│   │   └── service.yaml
│   └── sender/
│       ├── deployment.yaml
│       └── service.yaml
└── b2bholidays-mock-services.yaml  # Optional single-file deployment
```

---

## 📷 Screenshots / Evidence

## 📷 Screenshots

### 1. K3s Node Status
![K3s Nodes](screenshots/Nodes.png)

### 2. Cilium Status with WireGuard
![Cilium Status](Screenshots/cilium-status.png)

### 3. Hubble UI - Live Service Graph
![Hubble UI](screenshots/hubble-ui-network-flow-kube-system-identity-1239.png)

### 4. ArgoCD GitOps Deployment View
![ArgoCD App Tree](screenshots/AgroCD-insight-app.png)

### 5. All Services
![ArgoCD Health](Screenshots/All-services.png)

Attach screenshots of:

* `kubectl get nodes`
* `cilium status` with WireGuard enabled
* Hubble UI with live flows
* Network policy enforcement test

---

## 👨‍💻 Author

**Abbas Sheikh**
Senior DevOps & Cloud Architect

---

## 📞 Contact

Please reach out if further walkthrough or live demo is required.
