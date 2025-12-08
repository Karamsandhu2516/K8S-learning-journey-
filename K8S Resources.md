# k8S resources:
## Admission Control and Security Concepts (Mutating/Validating)

### Downward API:
it is a way for the kubelet (the agent running on each node) to inject metadata about the Pod and the cluster into the running containers.
**The Downward API works by making Pod metadata available to the container in one of two ways:**

**As Environment Variables:**
Ideal for passing simple, small pieces of information that the application needs to read once at startup, like the Pod's name or namespace.

**As Volume Files:**
Suitable for exposing information that is either larger (like annotations or labels) or that the application might want to read periodically while running. The kubelet dynamically creates a read-only volume and then creates files within that volume. The content of each file is the value of the requested field.

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

### Label:
Labels are tags you put on any object like POD to identify it.
```metadata: labels``
    app: cht-appp``` means this POD belongs to chat-app

### Matchlabel:
 It is selection filter to find the POD that any perticular deployment should control.
 The labels you put on the Pods MUST match the matchLabels in the selector, or the Deployment won't manage them.
 For Example: You put the you put the sticker of subjects(label) on your notebooks(PODS). Student(selector) need book(POD) of peticular subject. he will check the sticker(label) is he got the correct one meet the need(matchlabel). 
```metadata: labels``
    app: chat-app
  matchLabels:
    app: chat-app```

A Deployment will use matchLabels: {app: my-web-app} to find and manage only the Pods that have the label app: my-web-app.

### Scheduling Gates:
Scheduling Gates are a way to temporarily tell the Kubernetes Scheduler to completely ignore a Pod until an external system says it is ready.

**Why Do We Need Them?**
 when you create a Pod, the  Scheduler is always tries to find the best available Node for the Pod to run on. This process is complex:

Filtering: Check if the Node has enough CPU/RAM.

Scoring: Pick the "best" Node based on rules like affinity (run next to another Pod).

However, some applications have external dependencies that Kubernetes knows nothing about:

Custom Storage: The Pod needs a specific type of network storage provisioned by an external tool.

Security Policy: A security scanner must approve the Pod's image before it can run.

 The Scheduler wastes time repeatedly trying to schedule it, only to fail.

 #### How it worked:
 You add a security gate while creating YML
 ```schedulingGates:``
     - name: "gate-keeper"```
The Scheduler sees the gate and puts the Pod into a special, frozen state called SchedulingGated
A custom controller (an external program you or a vendor built) watches for Pods with this specific gate. It performs its security check or provisions the storage.
When the external system is done and the Pod is safe/ready, that external system uses the Kubernetes API to remove the gate from the Pod's specification.
Once the schedulingGates list is empty, the Pod is instantly released into the normal queue, and the Kubernetes Scheduler finds a Node for it.



### Rolling Update:
A Rolling Update is a strategy used to update an application without causing any downtime for users. It achieves this by replacing the old version of the application with the new version gradually, one piece at a time.

**Why K8s Uses Rolling Updates (The Problem it Solves)**
Before K8s, updating a live application often involved one of two problematic strategies:

Recreate: Shut down all the old servers, and then start all the new servers. Result: Downtime. The application is completely unavailable while the new servers are starting up.

Ramp Up/Ramp Down (Simultaneous): Start all the new servers, and then shut down all the old servers. Result: Resource Hog. For a brief time, you need double the capacity (8 servers total), which can be very expensive

**How the Rolling Update Works (The "Roll")**
The process is managed by the Deployment controller in K8s, following these steps automatically:
Deployment Controller: Reads the new image (nginx:1.19) and creates a new Pod with the updated image.
New Pod Starts: The new Pod starts up and reports that it is Ready to receive traffic.
Old Pod Terminates: Only after the new Pod is ready, the Deployment controller terminates one of the old Pods (the one running nginx:1.16).
Repeat: It repeats this cycle (start new, terminate old) until all the old Pods are replaced by the new Pods.

```kubectl set image deployment/my-app nginx=nginx:1.20```
The command essentially tells Kubernetes: "Go to the my-app Deployment, find the container named nginx inside it, and update its image to nginx:1.20." This action triggers the Rolling Update.




### Limitrange: 
It ensures that POD is using the only allocated resouces. e.g CPU and Memory should not be overused by POD with LimitRange Tag.
### ResourceQuota: 
It set the total limit on resources on in a NS.
### NodeSelector: 
It forces a Pod to run only on a Node that has a specific label.
### NodeAffinity: 
### PODoversize: 
Pod that asks for resources larger than what any single Node can provide, often used to demonstrate a scheduling failure.
### Priority_Pod: 
It assign a priority to the POD the higher prioriry POD runs first
### podschedulerreadiness: 
This allows you to say that a Pod is not ready to be considered for scheduling until a specific external condition is met.
