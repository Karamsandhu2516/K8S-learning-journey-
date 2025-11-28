# Unpacking POD

A POD is a smallest and most basic deployable unit in K8S. A Pod represents a single instance of a running process in your cluster. 
It is designed to host containers that are tightly coupled and need to share resources.

## Features of POD:

All containers within a Pod share the same network namespace and storage volumes.

Pods are designed to be relatively short-lived. If a Node dies, the Pods running on it are terminated. 
The Kubernetes Control Plane (specifically the Controller Manager) then creates new Pods to replace them, usually on a different Node.

You never deploy individual containers to Kubernetes; you always deploy them wrapped inside a Pod.

## How does POD Works:

When a Pod is scheduled to a Worker Node, the kubelet agent on that Node is responsible for running it. 
The User send the POD specification request to API server. The API Server authenticates the user and validates the Pod's YAML format. 
The API Server immediately saves the complete Pod object configuration into etcd. The Scheduler takes the Pod and finds the best Worker Node for it to run on. 
The Scheduler then assigns a score (priority) to each of the remaining feasible nodes based on various factors to find the optimal placement. 
The Node with the highest score is selected. The Kubelet agent running on the selected Worker Node constantly watches the API Server. 
It detects that a Pod has been scheduled to its Node. The Kubelet communicates with the Container Runtime Interface (CRI) to set up the Pod's infrastructure.
As containers start, the Kubelet constantly reports the status, events, and health checks back to the API Server.


## POD Life-Cycle:

**Pending State:** The Pod has been accepted by the Kubernetes cluster, but one or more of its containers has not been created or is not running yet. 
This includes the time spent waiting for image pulls.

**Running:** The Pod has been bound to a Node, and all the containers within the Pod have been created. At least one container is running, 
or is in the process of starting or restarting.

**Succeeded:** All containers in the Pod have terminated successfully, and will not be restarted.

**Failed:** container terminated in failure.

## The Pod Family: Containers within a Pod

Main Conatiner: This is the primary container that runs your core application logic (e.g., your web server, API service, or batch job). 
A Pod is often named after its main container.

Init Container: To perform setup tasks for the main application, such as setting up permissions, cloning a Git repository, waiting for a dependent service,
or initializing configuration files. They run sequentially. 

Sidecar Containers: A Sidecar Container is a container that runs alongside the main application container and supplements its functionality.
To handle ancillary tasks that the main application shouldn't worry about, like logging, monitoring, network proxies. 
They start concurrently with the main application container and run for the entire life of the Pod.
