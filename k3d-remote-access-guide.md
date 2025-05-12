
# 🔐 Remote Access Commands for K3d Cluster Services

This guide provides a consistent way to access key services (Hubble UI, Grafana, Prometheus, ArgoCD) running on a remote K3d cluster using SSH port forwarding.

---

## 🛠️ Prerequisites

- Replace `<REMOTE_PUBLIC_IP>` with your actual server IP (e.g., `49.112.18.255`)
- Run `kubectl port-forward` on the **remote server**
- Run `ssh -N -L` on your **local machine**

---

## ✅ Access Commands

### 1. Hubble UI
**Remote:**
```bash
kubectl port-forward -n kube-system pod/$(kubectl get pod -n kube-system -l k8s-app=hubble-ui -o jsonpath='{.items[0].metadata.name}') 8081:8081
```

**Local:**
```bash
ssh -N -L 9000:127.0.0.1:8081 root@<REMOTE_PUBLIC_IP>
```

**Browser:** http://localhost:9000

---

### 2. Grafana
**Remote:**
```bash
kubectl port-forward -n monitoring svc/kube-prometheus-grafana 3000:80
```

**Local:**
```bash
ssh -N -L 9001:127.0.0.1:3000 root@<REMOTE_PUBLIC_IP>
```

**Browser:** http://localhost:9001

---

### 3. Prometheus
**Remote:**
```bash
kubectl port-forward -n monitoring svc/kube-prometheus-kube-prome-prometheus 9090:9090
```

**Local:**
```bash
ssh -N -L 9002:127.0.0.1:9090 root@<REMOTE_PUBLIC_IP>
```

**Browser:** http://localhost:9002

---

### 4. ArgoCD
**Remote:**
```bash
kubectl port-forward -n argocd svc/argocd-server 8080:443
```

**Local:**
```bash
ssh -N -L 9003:127.0.0.1:8080 root@<REMOTE_PUBLIC_IP>
```

**Browser:** http://localhost:9003

**Get Admin Password:**
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo
```

---

## 📦 Summary Table

| Service    | Namespace    | Remote Port | Local Port | Browser URL             |
|------------|--------------|-------------|------------|--------------------------|
| Hubble UI  | kube-system  | 8081        | 9000       | http://localhost:9000   |
| Grafana    | monitoring   | 3000        | 9001       | http://localhost:9001   |
| Prometheus | monitoring   | 9090        | 9002       | http://localhost:9002   |
| ArgoCD     | argocd       | 443 → 8080  | 9003       | http://localhost:9003   |
