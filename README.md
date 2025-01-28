Kubernetes Namespaces Explained in 15 mins Kubernetes Tutorial 21 

- https://www.youtube.com/watch?v=K3jNo4z5Jx8 

## Kubernetes Namespaces Explained 

> What is Namespace in Kubernetes? 

> What are the use Cases of Namespaces in Kubernetes?

> How Namespaces Work and How to Use It ?

```
Kubernetes Cluster

Namespace A

           Namespace B
```

- What is a Namespace ?

Organize resources in Namespaces 

Virtual Clusters inside a Kubernetes Cluster 

**4 Namespaces by Default**

```
kubetcl get namespace
> NAME                   STATUS   AGE
  default                Active   2d
  kube-node-lease        Active   2d
  kube-public            Active   2d
  kube-system            Active   2d
  kubernetes-dashboard   Active   2d
```

#### kubernetes-dashboard

kubernetes-dashboard only with minikube

```
           Kubernetes Cluster
           Kube-system

              Kube-public

                kube-node-lease

                  default
```

#### kube-system 

> Do Not create or modify in kube-system 

> System processes

> Master and Kubectl processes 

#### kube-public

> Public accessible data

> A config map that contains cluster information

```
kubectl cluster-info
> Kubernetes master is running at https://192.168.64.5:8443
> KubeDNS is running at https://192.168.64.5:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

```

#### kube-node-lease

> Heartbeat between master and nodes 

> Each node has a associated lease object in this namespace 

> Determines the availability of the node 

#### default

> Resources you create are located here 

## Create Namespace 

```
kubectl create namespace my-namespace 
> namespace/my-namespace created

kubectl get namespace
> 
    default                Active   2d
    kube-node-lease        Active   2d
    kube-public            Active   2d
    kube-system            Active   2d
    kubernetes-dashboard   Active   2d
    my-namespace           Active   2s
```

Create a namespace with a configuration file 

NamespaceWithConfigurationFile
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql-configmap
  namespace: my-namespace
data:
  db_url: mysql-service.database 
```





