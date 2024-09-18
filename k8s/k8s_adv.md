# Kubernetes Advance

## Accesing multiple cluster

Many organizations set up a single Kubernetes cluster to manage their applications, but in some scenarios, a single Kubernetes cluster isn’t enough, which means organizations will set up multiple Kubernetes clusters. Such scenarios may include a need for increased availability and performance, policy compliance requirements that are region-specific, or a desire to eliminate vendor lock-in.

Shifting from a single Kubernetes cluster to multiple Kubernetes clusters raises new problems, such as managing access and securing the clusters.

### Use cases for accessing multiple  clusters

#### Providing managed Kubernetes service

If you’re providing a managed Kubernetes service, such as AWS EKS, you’ll need to manage multiple master nodes, allowing the clients to take charge of the worker nodes. While your clients won’t be bothered with the management of their master nodes, you and your team will need to access them to manage things like scaling, availability, and backups.

#### Access to multiple in-house clusters

One of the most popular reasons an organization may have multiple in-house clusters is to isolate apps on the basis of their environment. For instance, all apps in development are deployed in the dev cluster, while apps in the testing stage are deployed in the test cluster, and apps ready to be used by end users are deployed in the production cluster. This approach restricts access to the production environment, and creates stronger security on the production cluster, so that if there’s a misconfiguration on any of the dev or test clusters, users on the production cluster are not affected.

#### Virtual clusters for developers

Just as you can have multiple clusters based on the application environments, Kubernetes administrators can also create virtual clusters.Virtual clusters are spun from an existing Kubernetes cluster, and use the cluster’s resources. The primary advantage of virtual clusters is that they offer similar benefits to multiple clusters, such as restricting users’ access to specific clusters, which improves security but is less expensive than running multiple resource-independent clusters. Examples for Developer Virtual Clusters include: MicroK8s, Minikube and K3s.

### How to manage cluster access at scale

The kubeconfig configuration file is an important aspect of managing access to any kubernetes cluster. Without this file, you can't connect to any of your kubernetes clusters via `kubectl`.

There are three major sections in a kubeconfig file:

- **Clusters:** Contains the endpoint to the kubernetes cluster API server, as well as public certificate.

- **Users:** This is a list of users that are permitted to access the kubernetes API.

- **Contexts:** This maps users and clusters together, allowing one user to connect to multiple clusters, or multiple users to connect to one Kubernetes cluster.

```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority: demo-ca-file-1
    server: https://demo-cluster-1.com
  name: demo-cluster-1
- cluster:
    certificate-authority: demo-ca-file-2
    server: https://demo-cluster-2.com
    insecure-skip-tls-verify: false
  name: demo-cluster-2
- cluster:
    certificate-authority: demo-ca-file-3
    server: https://demo-cluster-3.com
    insecure-skip-tls-verify: true
  name: demo-cluster-3
contexts:
- context:
    cluster: demo-cluster-1
    namespace: development
    user: developer
  name: dev-cluster
- context:
    cluster: demo-cluster-2
    namespace: production
    user: admin
  name: admin-cluster
- context:
    cluster: demo-cluster-1
    namespace: staging
    user: developer
  name: dev-staging-cluster
current-context: ""
kind: Config
preferences: {}
users:
- name: developer
  user:
    client-certificate: demo-cert-file
    client-key: demo-key-file
- name: admin
  user:
    password: admin-password
    username: admin
```

## Kubectl-cheatsheet

```yaml
$ kubectl get
lists information about one or more resources

$ kubectl get pods

$ kubectl get pods --namespace=<namespace>
get pods in specific namespace.

$ kubectl get pods -o wide

$ kubectl get pod my-pod -o yaml
gets the YAML representation of a pod.

$ kubectl get namespaces/ns

$ kubectl config set-context --current --namespace=ggckad-s2
set current context to a namespace.

$ kubectl get deployment | kubectl get deploy

$ kubectl get deployment <deployment-name>
```

### Creating Resources

```yaml
$ kubectl create namespace <namespace-name>
create a new namespace

$ kubectl create deploy <deploy-name> --image=<image-name>

$ kubectl create deploy <deploy-name> --image=<image-name>
--replicas=<no-of-replicas> --port=<port-number>

$ kubectl create deploy <deploy-name> --image=<image-name> 
--replicas=<no-of-replicas>
--port=<port-number>

$ kubectl create -f <file-name>
creating a resource from a JSON or YAML file.
```

### Applying and updating resources

The kubectl apply command creates objects if none yet exist or applies changes to existing objects if they differ from the specifications in the input manifest file.

```yaml
$ kubectl apply -f service.yaml
create a new service with the definition in the service.yaml file.

$ kubectl apply -f deployment.yaml

$ kubectl apply -f [directory-name]
creates the objects defined in directory.
```

### Editing Kubernetes resources

`kubectl edit` lets you directly edit any API resource. It’ll open a text editor defined by your KUBE_EDITOR or EDITOR environment variables. As a default all files are YAML.

```yaml
$ kubectl edit svc/docker-registry
Edit the service name docker-registry

$ KUBE_EDITOR="code" kubectl edit svc/docker-registry
use an alternative editor.
```

### Get a shell to a running container

```yaml
$ kubectl get pod pod_name
find the pod

$ kubectl exec --stdin --tty shell-demo -- /bin/bash
```

```yaml
$ kubectl logs mypod
dump pod logs to stdout

$ kubectl describe nodes
show the details for all nodes.

$ kubectl describe deployment-name

$ kubectl describe -f service.yaml
display the details about a service defined in service.yaml file.

```

### Hands on Command

```bash
$ kubectl get pod <pod_name> -o=jsonpath='{.spec.containers[*].name}'
get containers running on that pod

$ kubectl logs <pod-name> -c <container-name>
retreive logs from a specific container in a pod.
```

## Explore

To identify and resolve kubernetes failure you need to:

- Monitor health and performance of cluster components.
- Ensure node have sufficient resources for the workloads they are running and quickly identify memory, storage, or process ID (PID) pressure.
- Identify pods that have trouble scheduling to nodes, frequently fail, or experience crash loops.
Detect and diagnose problems with Kubernetes objects like StatefulSets, DaemonSets, and Deployments.
Identify problems with containers and container images that can lead to cascading problems in the cluster.