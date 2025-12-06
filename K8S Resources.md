# k8S resources:
## Admission Control and Security Concepts (Mutating/Validating)
### Mutating : 
An external service registered with the API Server via a MutatingWebhookConfiguration resource.

**Function:** It is invoked first. Its purpose is to modify (mutate) the incoming request object. For example, it can automatically inject a sidecar container (like a logging agent), set default resource limits, or add specific Labels and Annotations to Pods before they are created.

### Validate Admission Webhook
An external service registered via a ValidatingWebhookConfiguration resource.

**Function:** It is invoked after all Mutating Webhooks have run. Its purpose is to validate the final state of the request object against custom policies. If the object violates a policy (e.g., trying to run a container as the root user), the webhook will reject the request, and the API Server will return an error

## Node Maintenance Operations (Cordon/Drain/Uncordon)

### Cordon: 
An action that sets a node to an "Unschedulable" state.

**Function:** It prevents the Kubernetes Scheduler from placing new Pods onto that node. However, all existing Pods on the node continue to run normally. This is the first step when preparing a node for maintenance.

```kubectl cordon <node-name>```

### Drain:
Moving all running POds safely from one node to another for node maintainace purpose.

**Function:**
It terminates the running Pods, respects any Pod Disruption Budgets (PDBs), and allows the controllers (like Deployment) to reschedule those Pods onto other healthy nodes. This is done to completely empty a node before a disruptive maintenance operation

```kubectl drain <node-name>```
### Uncordon

An action that reverts a node from the Unschedulable state back to Schedulable.

**Functin:** It tells the Scheduler that the node is ready to accept new workloads again. This is the final step after maintenance is complete.
```kubectl uncordon <node-name>```

## Workload Controllers:

### StatefulSet:
It manages Pods that require stable, unique identities

### DaemonSet:
A workload controller that ensures a copy of a specific Pod runs on every or a selection of Node(s) in the cluster.

### Job:
A resource that creates one or more Pods and ensures that a specified number of them successfully terminate. Used for batch processing or one-time tasks (e.g., running a script, database migration). If a Pod fails, the Job controller automatically creates a new one until the task is complete.

### CronJob:
A resource that manages Jobs based on a schedule. it used for tasks that need to run priodically like backups

### ConfigMap;
An API object used to store non-sensitive configuration data in key-value pairs. 

### Secret:
An API object used to store sensitive configuration data (passwords, tokens, keys). base64 encoded.















### Limitrange: It ensures that POD is using the only allocated resouces. e.g CPU and Memory should not be overused by POD with LimitRange Tag.
### ResourceQuota: It set the total limit on resources on in a NS.
### NodeSelector: It forces a Pod to run only on a Node that has a specific label.
### NodeAffinity: 
### PODoversize: Pod that asks for resources larger than what any single Node can provide, often used to demonstrate a scheduling failure.
### Priority_Pod: It assign a priority to the POD the higher prioriry POD runs first
### podschedulerreadiness: This allows you to say that a Pod is not ready to be considered for scheduling until a specific external condition is met.
