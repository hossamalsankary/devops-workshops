# Kubernetes Health Checks Examples

This directory contains examples demonstrating various configurations and best practices for implementing health checks in Kubernetes.

## Files in this directory:

### 1. `all-probes-explained.yaml`
A comprehensive example showcasing all three probe types (liveness, readiness, and startup) with detailed explanations of their purpose, configuration options, and use cases.

### 2. `multi-container-probes.yaml`
Demonstrates how to configure different health check strategies for multiple containers within the same pod, including web servers, API services, and databases.

### 3. `probe-methods-deployment.yaml`
Shows a deployment that implements all three probe methods:
- HTTP GET probes (ideal for web applications)
- TCP Socket probes (ideal for network services)
- Exec Command probes (for custom scripts and checks)

### 4. `probe-failure-handling.yaml`
Explains how Kubernetes handles probe failures and demonstrates realistic configuration settings for handling different failure scenarios.

## Key Concepts:

### Liveness Probe
- **Purpose**: Determines if a container is alive and running properly
- **Effect**: If it fails, the container will be RESTARTED
- **Use case**: Detect application deadlocks, memory leaks, or infinite loops

### Readiness Probe
- **Purpose**: Determines if container is ready to accept traffic
- **Effect**: If it fails, the pod is REMOVED from service endpoints
- **Use case**: Check if application can process requests, dependencies are ready

### Startup Probe
- **Purpose**: Determines when application has fully started
- **Effect**: Disables liveness and readiness probes until it succeeds
- **Use case**: Applications with slow/variable startup time, prevents premature restarts

## Best Practices:

1. **Set appropriate timeouts**: Configure `timeoutSeconds` to be less than `periodSeconds`
2. **Use initialDelaySeconds**: Allow enough time for your application to start
3. **Configure failure thresholds**: Prevent flapping with appropriate thresholds
4. **Choose the right probe type**: HTTP for web apps, TCP for network services, Exec for custom checks
5. **Keep probes lightweight**: Probes should have minimal impact on application performance
6. **Set resource limits**: Always define resource requests/limits for containers with probes
