### Create NS dev
```kubectl create ns dev```

**Output**

namespace/dev created
controlplane:~$ kubectl get ns
NAME                 STATUS   AGE
default              Active   18d
dev                  Active   13s
kube-node-lease      Active   18d
kube-public          Active   18d
kube-system          Active   18d
local-path-storage   Active   18d



### Create NS K8S-class

controlplane:~$ ```kubectl create ns k8s-class```

**OutPut**
namespace/k8s-class created
controlplane:~$ kubectl get ns
NAME                 STATUS   AGE
default              Active   18d
dev                  Active   3m17s
k8s-class            Active   6s
kube-node-lease      Active   18d
kube-public          Active   18d
kube-system          Active   18d
local-path-storage   Active   18d

### Create Deployment

```kubectl create deploy demo -n k8s-class --image=nginx```

**Output**
deployment.apps/demo created
controlplane:~$ kubectl create deploy demo -n dev --image=nginx
deployment.apps/demo created
controlplane:~$ kubectl get deploy -n dev
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
demo   1/1     1            1           2m6s
controlplane:~$ kubectl get deploy -n k8s-class
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
demo   1/1     1            1           3m16s

### Cluster Connectivity/Context

controlplane:~$ ```kubectl config set-context --current --namespace=dev```

**OutPut**
Context "kubernetes-admin@kubernetes" modified.

### Create LimitRange


```apiVersion: v1
kind: LimitRange
metadata:
  name: limit-practice
  namespace: practice-ns
spec:
  limits:
  - type: Container

    max:
      cpu: "1"
      memory: "1Gi"


    default:
      cpu: "200m"
      memory: "256Mi"
    defaultRequest:
      cpu: "100m"
      memory: "128Mi"```

controlplane:~$ kubectl apply -f practice-ns.yml
namespace/practice-ns created
controlplane:~$ kubectl apply -f limit-practice.yml
limitrange/limit-practice created
