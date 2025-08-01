# Kubernetes Core Concepts Examples

This repository contains practical examples of Kubernetes core concepts, organized by component type.

## Directory Structure

- **namespaces/** - Examples of namespaces and resource isolation
- **pods/** - Pod configurations with various features
- **services/** - Different service types and configurations
- **deployments/** - Deployment strategies and examples
- **replicasets/** - ReplicaSet configurations and examples
- **configmaps/** - Configuration management examples
- **secrets/** - Secret management examples
- **volumes/** - Storage and volume examples
- **statefulsets/** - StatefulSet examples for stateful applications
- **jobs/** - Job examples for batch processing
- **cronjobs/** - CronJob examples for scheduled tasks
- **ingress/** - Ingress examples for HTTP/S routing
- **network-policies/** - Network policy examples
- **diagrams/** - Visual representations of Kubernetes concepts

## Examples by Component

### Namespaces

- **namespace-basic.yaml**: Simple namespace definition
- **namespace-quota.yaml**: Namespace with resource quotas
- **environment-namespaces.yaml**: Multi-environment namespace setup
- **namespace-limit-range.yaml**: Setting default resource limits

### Pods

- **pod-basic.yaml**: Basic pod configuration
- **multi-container-pod.yaml**: Pod with multiple containers
- **pod-with-resources.yaml**: Pod with resource requests and limits
- **pod-with-volume.yaml**: Pod with volume mounting
- **pod-with-health-checks.yaml**: Pod with liveness, readiness, and startup probes
- **pod-with-env-vars.yaml**: Pod with environment variables

### Services

- **clusterip-service.yaml**: Internal ClusterIP service
- **nodeport-service.yaml**: NodePort service exposed on node ports
- **loadbalancer-service.yaml**: LoadBalancer service for external access
- **externalname-service.yaml**: ExternalName service for external services
- **service-with-session-affinity.yaml**: Service with client IP session affinity

### Deployments

- **basic-deployment.yaml**: Standard deployment configuration
- **rolling-update-deployment.yaml**: Deployment with rolling update strategy
- **recreate-deployment.yaml**: Deployment with recreate strategy
- **canary-deployment.yaml**: Canary deployment with traffic splitting
- **blue-green-deployment.yaml**: Blue-green deployment setup

### ReplicaSets

- **basic-replicaset.yaml**: Standard ReplicaSet configuration
- **replicaset-with-node-selector.yaml**: ReplicaSet with node selector constraints
- **replicaset-with-match-expressions.yaml**: ReplicaSet with complex selectors
- **replication-controller.yaml**: Example of the older ReplicationController

### ConfigMaps

- **configmap-basic.yaml**: ConfigMap with multiple configuration formats
- **pod-with-configmap.yaml**: Pod using ConfigMap as volume and environment variables

### Secrets

- **secret-basic.yaml**: Basic Secret with sensitive data
- **pod-with-secrets.yaml**: Pod consuming Secrets as volumes and environment variables

### Volumes

- **persistent-volume.yaml**: PersistentVolume definition
- **persistent-volume-claim.yaml**: PersistentVolumeClaim definition
- **pod-with-persistent-volume.yaml**: Pod with mounted PersistentVolume
- **pod-with-multiple-volumes.yaml**: Pod with various volume types

### StatefulSets

- **basic-statefulset.yaml**: StatefulSet with persistent storage
- **headless-service.yaml**: Headless Service for StatefulSet

### Jobs

- **basic-job.yaml**: Job with completions, parallelism and timeout

### CronJobs

- **basic-cronjob.yaml**: Scheduled job with retention policies

### Ingress

- **basic-ingress.yaml**: Basic Ingress with host and path routing
- **advanced-ingress.yaml**: Advanced Ingress with annotations and path rewriting

### Network Policies

- **db-network-policy.yaml**: Network policy for database access control
- **deny-all-ingress.yaml**: Policy to block all incoming traffic

## Usage

Apply any example with:

```bash
kubectl apply -f <file-path.yaml>
```

For example:

```bash
kubectl apply -f namespaces/namespace-basic.yaml
```

To apply all examples in a directory:

```bash
kubectl apply -f namespaces/
```

## Key Commands Reference

### Namespace Commands
```bash
# Create namespace
kubectl create namespace my-app

# List namespaces
kubectl get namespaces

# Describe namespace
kubectl describe namespace my-app

# Delete namespace
kubectl delete namespace my-app
```

### Pod Commands
```bash
# Create pod
kubectl run nginx-pod --image=nginx:latest

# List pods
kubectl get pods

# Describe pod
kubectl describe pod nginx-pod

# View logs
kubectl logs nginx-pod

# Exec into pod
kubectl exec -it nginx-pod -- /bin/bash
```

### Service Commands
```bash
# Create service
kubectl expose pod nginx-pod --port=80 --type=ClusterIP

# List services
kubectl get services

# Describe service
kubectl describe service nginx-service

# Delete service
kubectl delete service nginx-service
```

### Deployment Commands
```bash
# Create deployment
kubectl create deployment nginx-deployment --image=nginx:latest --replicas=3

# List deployments
kubectl get deployments

# Update image
kubectl set image deployment/nginx-deployment nginx=nginx:1.19

# View rollout history
kubectl rollout history deployment/nginx-deployment

# Rollback to previous version
kubectl rollout undo deployment/nginx-deployment
```

### ReplicaSet Commands
```bash
# List ReplicaSets
kubectl get rs

# Scale ReplicaSet
kubectl scale rs nginx-replicaset --replicas=5

# Describe ReplicaSet
kubectl describe rs nginx-replicaset

# Delete ReplicaSet
kubectl delete rs nginx-replicaset
```

### ConfigMap & Secret Commands
```bash
# Create ConfigMap
kubectl create configmap app-config --from-file=config.properties

# Create Secret
kubectl create secret generic app-secrets --from-literal=username=admin --from-literal=password=secret

# Get ConfigMaps/Secrets
kubectl get configmaps
kubectl get secrets

# Describe ConfigMap/Secret
kubectl describe configmap app-config
kubectl describe secret app-secrets
```

### Volume Commands
```bash
# Create PersistentVolume
kubectl apply -f persistent-volume.yaml

# Create PersistentVolumeClaim
kubectl apply -f persistent-volume-claim.yaml

# List PVs and PVCs
kubectl get pv
kubectl get pvc

# Describe PV/PVC
kubectl describe pv pv-example
kubectl describe pvc pvc-example
```

### StatefulSet Commands
```bash
# Create StatefulSet
kubectl apply -f basic-statefulset.yaml

# List StatefulSets
kubectl get statefulsets

# Scale StatefulSet
kubectl scale statefulset web --replicas=5

# Delete StatefulSet without deleting volumes
kubectl delete statefulset web --cascade=orphan
```

### Job & CronJob Commands
```bash
# Create Job
kubectl apply -f basic-job.yaml

# Create CronJob
kubectl apply -f basic-cronjob.yaml

# List Jobs/CronJobs
kubectl get jobs
kubectl get cronjobs

# View CronJob schedule
kubectl get cronjob database-backup -o yaml

# Manually trigger a CronJob
kubectl create job --from=cronjob/database-backup manual-backup
```

### Ingress Commands
```bash
# Create Ingress
kubectl apply -f basic-ingress.yaml

# List Ingress
kubectl get ingress

# Describe Ingress
kubectl describe ingress basic-ingress

# Get Ingress address
kubectl get ingress basic-ingress -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

### Network Policy Commands
```bash
# Create Network Policy
kubectl apply -f db-network-policy.yaml

# List Network Policies
kubectl get networkpolicies

# Describe Network Policy
kubectl describe networkpolicy db-network-policy
```
