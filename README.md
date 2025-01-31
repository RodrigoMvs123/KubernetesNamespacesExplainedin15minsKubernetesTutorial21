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

NamespaceWithConfigurationFile.yaml
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql-configmap
  namespace: my-namespace
data:
  db_url: mysql-service.database 
```

Use Cases when to use Namespaces 

1. **Structure** your components

2. **Avoid conflicts** between teams

3. **Share services** between different environments 

4. **Access and Resource Limits** on Namespaces level 

**Why** to use Namespaces ?

- Everything in one Namespace # No Overview 

```
Kubernetes Cluster

default

  deployments
              replicas 
                       services
                                configmaps                  
```

Resources **grouped** in Namespaces 

```
Kubernetes Cluster

Database     Monitoring      Elastic Stack      Nginx-Ingress 
```

Override the first team deployment
 
```
Kubernetes Cluster

default 
                  
           my-app deployment           my-app deployment 
                             config 1                    config 2
```

- Solution 

```
Kubernetes Cluster

default 
           Project A                   Project B   
           Namespace                   Namespace
           my-app deployment           my-app deployment 
                             config 1                    config 2
```

Resource Sharing: Staging and Development 

> Re-use those components in both environments 

```
Kubernetes Cluster

Staging 
          Development 
                        Nginx-Ingress Controller 
                                                   Elastic Stack
```

Resource Sharing: Blue-Green Deployment

> The versions of Application differ 

```
Kubernetes Cluster

Production Blue        Production Green

Nginx-Ingress Controller 
                           Elastic Stack
```

Access and Resources Limits on Namespaces 

> Each team has own, isolated environment

> Limit: CPU, RAM, Storage per Namespace 

```
Kubernetes Cluster

default 
           Project A                            Project B   
           Namespace                            Namespace
           my-app deployment                    my-app deployment 
                             config 1                             config 2
                             Resource Quota A                     Resource Quota B 
```

Characteristics of **Namespaces**

You can not access most resources from another Namespace 

> Create the same resource ConfigMap in another Namespace 

> Can not use the same ConfigMap from Database to another Namespace 

> Each Namespace must define its own ConfigMap 

> The same rule applies to Secrets 

```
Kubernetes Cluster

           Project A                            Project B   
           Namespace                            Namespace
           ConfigMap                            ConfigMap
                            Database

```

Access Service in another Namespace 

```
Kubernetes Cluster

           Project A                                   Project B   
           Namespace                                   Namespace
           ConfigMap                                   ConfigMap
                            Database
                                    mysql-service   
```

AccessServiceInAnotherNamespace.yaml
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql-configmap
data:
  db_url: mysql-service.database 
```

Components, which can not be created within a Namespace

> Live globally in the Cluster

> You can not isolate them in a Namespace

```
Kubernetes Cluster

           Project A                                   Project B   
           Namespace                                   Namespace
                                            
                       volume      node    
```

- List components that are not bound to a Namespace 

```bash

CLI Commands 

```bash
kubectl api-resources --namespaced=false
kubectl api-resources --namespaced=true
```

**Create** Components in a Namespace 

> No Namespace Defined

- By default, components are created in a default Namespace

NoNamespaceDefined.yaml
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql-configmap
data:
  db_url: mysql-service.database 
```

```
kubectl apply -f myconfigmap.yaml
> configmap/mysql-configmap created

kubctl get configmap
> NAME             DATA   AGE
  mysql-configmap  1      2s

kubectl get configmap -n default
> NAME             DATA   AGE
  mysql-configmap  1      2s

kubectl get configmap -o yaml
> apiVersion: v1
  data:
    db_url: mysql-service.database
  kind: ConfigMap
  metadata:
    creationTimestamp: "2021-08-29T14:00:00Z"
    name: mysql-configmap
    namespace: default
    resourceVersion: "123456"
    uid: 123456-123456-123456-123456

kubctl apply -f myconfigmap.yaml --namespace=my-namespace
> configmap/mysql-configmap created
```

Include configuration file in a Namespace

> Configuration file over kubectl command 

> It is better documented 

IncludeConfigurationFileInNamespace.yaml
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql-configmap
  namespace: my-namespace
data:
  db_url: mysql-service.database 
```

Get the component in the specified Namespace

```
kubectl get configmap -n my-namespace
> NAME             DATA   AGE
  mysql-configmap  1      2s
```

Change **Active Namespace**

> Change the active Namespace with kubens 

On Macbook 
```
brew install kubectx

kubens 
> default # Active Namespace
  kube-node-lease
  kube-public
  kube-system
  kubernetes-dashboard
  my-namespace

kubens my-namespace
> Context "my-namespace" modified.

kubens
> default
  kube-node-lease
  kube-public
  kube-system
  kubernetes-dashboard
  my-namespace # Active Namespace

```

Kubectx/Kubens Installation Guide: 

- https://github.com/ahmetb/kubectx#installation 

