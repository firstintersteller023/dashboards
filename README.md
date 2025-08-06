
Steps below to create the ConfigMaps to mount dashboards.

---

## ✅ Step-by-Step Using Your Dashboard Paths

### 1. 📁 Create `calico-felix` ConfigMap (Folder: `Calico Felix`)

```bash
kubectl create configmap calico-felix-dashboard \
  --from-file=calico-felix.json=_config/dashboards/calico-felix.json \
  -n observability \
  --dry-run=client -o yaml > calico-felix-dashboard-cm.yaml
```

Now edit `calico-felix-dashboard-cm.yaml` and add labels and annotations:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: calico-felix-dashboard
  namespace: observability
  labels:
    grafana_dashboard: "1"
  annotations:
    k8s-sidecar-target-folder: "Calico Felix"
data:
  calico-felix.json: |-
    # (paste the full JSON content from _config/dashboards/calico-felix.json here)
```

Then apply it:

```bash
kubectl apply -f calico-felix-dashboard-cm.yaml
```

---

### 2. 📁 Create `calico` ConfigMap (Folder: `Calico`)

```bash
kubectl create configmap calico-dashboard \
  --from-file=calico.json=_config/dashboards/calico.json \
  -n observability \
  --dry-run=client -o yaml > calico-dashboard-cm.yaml
```

Edit `calico-dashboard-cm.yaml` and set:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: calico-dashboard
  namespace: observability
  labels:
    grafana_dashboard: "1"
  annotations:
    k8s-sidecar-target-folder: "Calico"
data:
  calico.json: |-
    # (paste the full JSON content from _config/dashboards/calico.json here)
```

Apply it:

```bash
kubectl apply -f calico-dashboard-cm.yaml
```

---

### 3. 🔄 Restart Grafana to Pick Up Dashboards

```bash
kubectl rollout restart deployment grafana -n observability
```

---

### ✅ Final Check

Run:

```bash
kubectl get configmap -n observability -l grafana_dashboard=1
```

You should see:

```
NAME                    DATA   AGE
calico-dashboard        1      <...>
calico-felix-dashboard  1      <...>
```

And both should now appear in Grafana under folders:

* 📁 Calico
* 📁 Calico Felix

Let me know once done or if you want me to generate the YAMLs directly for you.
