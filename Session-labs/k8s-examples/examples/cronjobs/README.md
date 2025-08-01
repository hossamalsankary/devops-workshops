# Kubernetes Scheduled Tasks Examples

This directory contains examples demonstrating various configurations for running scheduled tasks in Kubernetes.

## Files in this directory:

### 1. `basic-cronjob.yaml`
A standard Kubernetes CronJob example that runs on a schedule using cron syntax.

**Note about CronJob scheduling:**
- Kubernetes CronJobs use the standard 5-field cron format (`minute hour day-of-month month day-of-week`)
- The minimum interval is 1 minute (not seconds)
- To schedule a job to run "every minute", use `* * * * *`

### 2. `thirty-second-job.yaml`
An example showing how to run a task every 30 seconds using a continuous Job with an internal sleep loop.

### 3. `thirty-second-deployment.yaml`
Another approach to running tasks every 30 seconds using a Deployment with a container that runs in a continuous loop.

## Scheduling Options Comparison:

| Feature | CronJob | Job with Loop | Deployment with Loop |
|---------|---------|---------------|----------------------|
| Minimum interval | 1 minute | Any (seconds) | Any (seconds) |
| Automatic restart on failure | Yes | Limited | Yes |
| Resource overhead | Low (job runs only when scheduled) | Medium | Medium |
| Ideal for | Scheduled tasks that run periodically | Short-term tasks needing sub-minute frequency | Long-running tasks needing sub-minute frequency |

## When to use each approach:

1. **Use CronJob when:**
   - You need a task to run at specific times (hourly, daily, etc.)
   - 1-minute resolution is sufficient
   - You want automatic cleanup of completed jobs

2. **Use Job with Loop when:**
   - You need sub-minute intervals (seconds)
   - The task has a defined end condition
   - You want to manually control when the job stops

3. **Use Deployment with Loop when:**
   - You need sub-minute intervals (seconds)
   - The task should run indefinitely
   - You want automatic restarts if the process crashes
