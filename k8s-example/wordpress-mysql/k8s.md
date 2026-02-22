### Install k8s
```bash
curl -sfL https://get.k3s.io | sh - 
# Check for Ready node, takes ~30 seconds 
sudo k3s kubectl get node 
```

### config k3s
```bash
sudo chmod  777 /etc/rancher/k3s/k3s.yaml
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml

```

### deploy wordpress and mysql
```bash
kubectl apply -f ns.yaml
kubectl apply -f db.yaml
kubectl apply -f wordpress.yaml
```