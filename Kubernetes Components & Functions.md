Kubernetes is a open source platform design to scale, automate and manage containerized applicayions. Two manin components of K8S : Control plane (Master Node) and worker Node. Everything in kubernetes is a manifest file.
Control Plane Components and Functions:
APT Server : API server is the central management unit or entry point of K8S cluster. All requests must go through APT server
Function:
-Receive and validate all requests
-The API Server checks the credentials provided in the request like certificates and Autheticate the request.
-API server than checks for the authorization if your identity is bound to a Role that has the necessary permission the request is authorized.
Admission Controllers are code modules inside the API Server that intercept the request. The request is checked against cluster policies and best practices before it is stored.
Kube-Scheduler: The Scheduler is responsible for figuring out which Worker Node is the best fit to run a newly created Pod.
Function:
-Watches the API Server for new Pods that haven't been assigned a node.
-Narrows down the list of all available nodes to only those that meet the Pod's requirements.
-Scoring Assigns a score to the remaining nodes based on optimization goals. It then selects the node with the highest score and updates the Pod's specification in the API Server.
Controler Manager : The Controller Manager is a core control-plane component responsible for running all the built-in controllers that continuously regulate the state of the cluster. The Kubernetes Controller Manager runs a set of background processes called controllers. Each controller watches some part of the cluster’s state and makes changes to move the actual state toward the desired state defined in the API server.
Functions:
-Observe the current cluster state from the API server.
-Compare it with the desired state defined by the user.
-Act to fix differences (create pods, delete pods, update objects, etc.)
Key Controllers Inside the Controller Manager:
	1	Node Controller: Detects and responds when nodes go down Marks nodes as NotReady Evicts pods from unhealthy nodes
	2	Replication/ReplicaSet Controller: Ensures the correct number of pod replicas exist Creates or removes pods automatically
	3	Deployment Controller: Manages rolling updates and rollbacks
	4	StatefulSet Controller: Ensures ordered and stable pod identities Maintains unique network identifiers
	5	DaemonSet Controller: Ensures a pod runs on every (or selected) node
	6	Job & CronJob Controller: Manages batch jobs and scheduled jobs
	7	Service Account & Token Controllers: Create default service accounts Manage API tokens for pods
	8	EndpointSlice / Service Controller: Keeps Service → Pod mappings updated
ETCD: The brain and memory of the Kubernetes cluster. It holds the desired state of all Kubernetes objects.If you create a Deployment, Pod, Service that data is saved in etcd.
Functions:
-It guarantees that all nodes in the cluster see the same data, even in failures.
-Data is stored as simple keys and values.
-Kubernetes controllers use etcd for determining leaders
Worker Node componebts and functions: The Worker Node (also called a compute node) is where your application workloads—the Pods—actually run. It is managed by the Control Plane.
Kubectl:
The Kubelet is the main agent on the node. It is the intermediary that communicates with the Control Plane's API Server to ensure that the containers defined in the Pod specifications are running and healthy on the node.
Functions: -Registers the node with the Kubernetes cluster and reports its resource capacity.
-Watches the API Server for PodSpecs assigned to its node by the Scheduler. It then instructs the Container Runtime to start, stop, or update containers to match the desired state.
-Continuously monitors the health and status of its containers and reports this information back to the API Server.
-Manages pods defined by local manifest files on the node.
Container Runtime: The Container Runtime is the software responsible for executing the containers. The Kubelet uses the Container Runtime Interface (CRI) to communicate with it.
Functions:
-Pulls the required container images from a registry (like Docker Hub or a private registry).
-Starts and stops the containers based on instructions from the Kubelet.
-containerd (most common), CRI-O, and older versions of Docker Engine supported by it.
Kube-Proxy : The Kube-Proxy is a network proxy that runs on every node. It is responsible for making Kubernetes networking work by enabling communication between Pods and outside traffic.
Function:
-It watches the API Server for new Services and Endpoints.
-It programs and maintains network rules (usually using iptables or IPVS) on the node's operating system to direct traffic destined for a Service's virtual IP to the correct backend Pod.
-Provides simple, round-robin load balancing across all the Pods belonging to a particular Service.
