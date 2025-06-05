# httpbin Helm Chart

## Overview

Helm chart for deploying the httpbin service:

- Deployment с topologySpreadConstraints
- HPA (autoscaling)
- ServiceAccount с restricted securityContext
- NetworkPolicy
- Ingress (traefik)
- Service (ClusterIP)
- Namespace with SRE / monitoring labels
- Standard Kubernetes labels (for observability and oncall)

---

## Requirements

- Kubernetes >= 1.21
- Helm 3.x

---

## Installation

### 1️⃣ Clone repository:

```bash
git clone https://github.com/sheltso/httpbin.git
cd httpbin
```

2️⃣ Install chart:

If the namespace does NOT exist yet:

```
helm install httpbin-release ./helm/httpbin -n httpbin --create-namespace
```

If the namespace already exists:

```
helm install httpbin-release ./helm/httpbin -n httpbin
```

Verification

```
helm list -n httpbin
kubectl get all -n httpbin
```

Expected output

```
NAME                                      READY   STATUS    RESTARTS   AGE
pod/httpbin-deployment-7997bd8b7b-7n29q   1/1     Running   0          30s
pod/httpbin-deployment-7997bd8b7b-m56zk   1/1     Running   0          30s
pod/httpbin-deployment-7997bd8b7b-vs9x9   1/1     Running   0          30s

```

Verify HTTP endpoint (via port-forward)

```
kubectl port-forward svc/httpbin-service -n httpbin 8081:80
curl http://localhost:8081/get
```

Expected output

```
{
  "args": {}, 
  "headers": {
    "Accept": "*/*", 
    "Host": "localhost:8081", 
    "User-Agent": "curl/8.5.0"
  }, 
  "origin": "127.0.0.1", 
  "url": "http://localhost:8081/get"
}
```

Upgrade

```
helm upgrade httpbin-release ./helm/httpbin -n httpbin
```

Uninstall

```
helm uninstall httpbin-release -n httpbin
```

Configuration

| Parameter                                  | Default value                 |
| ------------------------------------------ | ----------------------------- |
| `replicaCount`                             | `3`                           |
| `container.image`                          | `kennethreitz/httpbin:latest` |
| `svc.port`                                 | `80`                          |
| `autoscaling.minReplicas`                  | `3`                           |
| `autoscaling.maxReplicas`                  | `5`                           |
| `networkpolicyservice`                     | `enabled`                     |
| `ingreservice.host`                        | `httpbin.org`                 |
| `securityContext.allowPrivilegeEscalation` | `false`                       |
