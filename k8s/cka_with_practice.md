# CKA

## Pods

### Pods with Yaml

consist of four parts.

- apiVersion
- kind
- metadata
- specification (spec)

```yaml (pod-defination.yaml)
apiVersion: v1
kind: Pod
metadata:
    name: myapp-pod
    labels:
        app: myapp
        type: front-end
spec:
    containers:
      - name: nginx-container
        image: nginx
```

```bash
$ kubectl create -f pod-defination.yml
run the pods from above file.

$ kubectl get pods
list pods

$ kubectl describe pod myapp-pod
```

## Replication Contorller

```yaml (rc-definition.yml)
apiVersion: v1
kind: ReplicationController
metadata: 
  name: myapp-rc
  labels:
    app: myapp
    type: front-end

spec:
  template:
      metadata:
        name: myapp-pod
        labels:
          app: myapp
          type: front-end
      spec:
        containers:
        - name: nginx-container
          image: nginx

  replicas: 3
```

## Replica Set

```yaml (replicaset-definition.yml)
apiVersion: apps/v1
kind: ReplicaSet
metadata: 
  name: myapp-replicaset
  labels:
    app: myapp
    type: front-end

spec:
  template:
      metadata:
        name: myapp-pod
        labels:
          app: myapp
          type: front-end
      spec:
        containers:
        - name: nginx-container
          image: nginx

  replicas: 3
  selector: 
    matchLabels:
      type: front-end
```

```bash
$ kubectl create -f replicaset-defination.yml
create replicaset

$ kubectl get replicaset

$ kubectl delete replicaset myapp-replicaset

$ kubectl replace -f replicaset-defination.yml

$ kubectl scale --replicas=6 -f replicaset-defination.yml
scale without changing in file defination.

$ kubectl edit replicaset 
```

How to scale the pods?

1. change in replicaset defination file ( replicas: 6)
    `kubectl replace -f replicaset-defination.yml`
2. Use kubectl scale command.
    `kubectl scale --replicas=5 -f replicaset-definition.yml`
3. Use kubectl scale command with type and name.
    `kubectl scale --replicas=6 replicaset myapp-replicaset`

## Conventions

```bash
$ kubectl run nginx --image=nginx
create nginx pod

$ kubectl run nginx --image=nginx --dry-run=client -o yaml

$ kubectl create deployment --image=nginx nginx

$ kubectl create deployment --image=nginx nginx --dry-run=client -o yaml

$ kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml

$ kubectl create -f nginx-deployment.yaml
```

## Service

- **NodePort** : where the service makes an internal port accessible on a prot on that node. service forwards the request to pods running on that node.
  
  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: myapp-service
  spec:
    types: NodePort
    ports:
    - targetPort: 80
      port: 80
      nodePort: 30008
  ```
  
  To connect the service to the Pod

  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: myapp-service
  spec:
    types: NodePort
    ports:
    - targetPort: 80
      port: 80
      nodePort: 30008
    selector:
      app: myapp
      type: front-end
  ```

- **ClusterIP** : In this case the service creates a virtual IP inside the cluster to enable communication between different services such as set of frontend servers to a set of backend servers.

  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: back-end
  spec:
    types: ClusterIP 
    ports:
      - targetPort: 80
        port: 80
    selector:
      app: myapp
      type: back-end
  ```

- **LoadBalancer** : Where the service porvisions a loadbalancer for our application in supported cloud providers.

```yaml
apiVersion: v1 
kind: Service
metadata:
  name: myapp-service
spec: 
  type: NodePort
  ports:
  - targetPort: 80
    port: 80
    nodePort: 30008
  selector:
    app: myapp
    type: front-end
```

Note: selector is used to map service to the pod(copy metadata section from pod to here).

Note: If multiple pods are running on a single node. then service forwards the request to any node in random fashion. what if pods are running in different nodes. then the service will span on all nodes and you can access service port from any of node ip.

```bash
$ kubectl create -f service-defination.yml
create service

$ kubectl get service
```

## Namespace

- **kube-system:** contains resources/objects created by kubernetes system.
- **Default:** contains object and resources created by administration and developers.
- **kube-public:** unsecured and readable by anyone, expose public info.
- **kube-node-lease:** holds node beat objects used for node heartbeat data.

Note: you can define namespace name in metadata option to create pod in that namespace.

```yml (namespace-dev.yml)
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

```bash
$ kubectl create -f namespace-dev.yml
create ns

$ kubectl create namespace dev
or usie this to create namespace.

$ kubectl config set-context $(kubectl config current-context) --namespace=dev
to switch namespace
```

## Imperative Command

```bash
$ kubectl run redis --image=redis:alpine -l tier=db
to specify labels in pod

$ k expose pod redis --port=6379 --name redis-service
create a service redis-service to expose the redis application within the cluster on port 6379

$ k create deployment webapp --image=[image-name] --replicas=3
create a deployment webapp with replicas of 3.

$ k run custom-nginx --image=nginx --port=8080
create a pod custom-nginx using the nginx image and expose it on container port 8080.

$ k create deployment redis-deploy --image redis --replicas 2 -n dev-ns
create a new deployment redis-deploy in dev-ns namespace with the redis image and have 2 replcias.

$ k run httpd --image httpd:alpine --expose --port 80
create a pod called httpd with image httpd:alpine in default namespace and create a service of type clusterIP by the same name httpd and the target port for the service is 80.

$ k set image deployment/myapp-deployment nginx=nginx:1.9.1
update an image in deployment.
```

## Application lifecycle management

## Commands

CMD define the program to run inside the container. as long as the program is running container is alive.

```bash
$ docker run ubuntu [COMMAND]
base command

$ docker run ubuntu sleep 5

$ docker run --entrypoint sleep2.0 ubuntu-sleeper
```

ENTRYPOINT - run default command

### ENV variables in kubernetes

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: simple-webapp-color
spec:
  containers:
  - name: simple-webapp-color
    image: simple-webapp-color
    ports:
      - containerPort: 8080
    
    env:
      - name: APP_COLOR
        value: Blue
```

when you have more environment variable it is difficult to manage inside pods.

### ConfigMaps

1. Create ConfigMap

    a. Imperative - `kubectl create configmap` directly pass variable in command.

    ```bash
      $ kubectl create configmap
        <config-name> --from-literal=<key>=<value>
      
      $ kubectl create configmap \
        app-config --from-literal=APP_COLOR=blue \
                  --from-literal=APP_MOD=prod

      $ kubectl create configmap
        <config-name> --from-file=<path-to-file>
      ```

      b. Declarative - `kubectl create -f` create defination file.

      ```yaml
        apiVersion: v1
        kind: ConfigMap
        metadata:
          name: app-config
        data:
          APP_COLOR: blue
          APP_MODE: prod
      ```

2. Inject into Pod
    inject config map into the pod defination file.

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
    name: simple-webapp-color
    spec:
    containers:
    - name: simple-webapp-color
    image: simple-webapp-color
    ports:
      - containerPort: 8080

    envFrom:
      - configMapRef:
          name: app-config
    ```

Note: if edit not worked use replace `kubectl replace --force -f file-name`

### Secrets

1. Create Secret
  a. Imperative
  
    ```bash
    kubectl create secret generic
      <secret-name> --from-literal=<key>=<value>

    kubectl create secret generic
      <secret-name> --from-file=<path-to-file>
    ```
  
    b. Declarative
  
    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: app-secret
    data:
      DB_Host: mysql_encoded
      DB_User: user_encoded
      DB_Password: password_encoded
    ```

    ```bash
    $ echo -n 'mysql' | base64
    pass the output to secret
    ```

2. Inject into Pod

### Multi-Container Pods

share the same lifecycle. share the same network space and volume sharing.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: simple-webapp
  labels:
    name: simple-webapp
spec:
  containers:
  - name: simple-webapp
    image: simple-webapp
    ports:
      - containerPort: 8080
  - name: log-agent
    image: log-agent
```

### Init Containers

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp

spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    command: ['sh','-c','echo the app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox
    command: ['sh','-c','git clone repo; done;']
```

When a POD is first created the initContainer is run, and the process in the initContainer must run to a completion before the real container hosting the application starts. You can configure multiple such initContainers as well, like how we did for multi-containers pod. In that case each init container is run one at a time in sequential order. If any of the initContainers fail to complete, Kubernetes restarts the Pod repeatedly until the Init Container succeeds.

### Self Healing Applications

Kubernetes supports self-healing applications through ReplicaSets and Replication Controllers. The replication controller helps in ensuring that a POD is re-created automatically when the application within the POD crashes. It helps in ensuring enough replicas of the application are running at all times.

## Cluster Maintenance

### OS Upgrades

When node is down, if it comes alive immediately kubelet process starts and the pod come back online. If node is down for more than 5 minutes then the node as dead and if the pods are part of replica set they will be recreated on other nodes.

pod-eviction-timeout : time a pod takes to come back online. it is set on kube-controller-manager with the default value of 5m. `kube-controller-manager --pod-eviction-timeout=5m0s`

```bash
$ kubectl drain node-1
terminate pods on node-1 and rescheduled on other nodes. and mark node as unscheduled (no new pod will scheduled on this node)

$ kubectl drain node01 --ignore-daemonsets

$ kubectl cordon node-2
it will not terminate the existing pods. but mark the node as unscheduled.

$ kubectl uncordon node-1
allow node to be scheduled.
```

## Kubernetes Software Versions

major.minor.patch
minor - feature and functionalities.

### Cluster Upgrade Process

kube-apiserver : X
controller-manager, kube-sceduler : X or X-1
kubelet, kube-proxy : X or X-1 or X-2

kubectl : X+1 > X-1

kubernetes supports only upto recent 3 minor versions. So when v1.12 is released then v1.12, v1.11, v1.10. 

How to upgrade?
upgrade one minor version at a time. v1.10 to v1.11 to v1.12. suppose our node and components are at version v1.10.

Upgrading involves two major steps. First, you upgrade your **master nodes** and then the **worker nodes**.

In worker nodes -

1. either you can update all nodes at once but the cluster will be down.
2. upgrade node one by one so that pod can be resceduled to other nodes
3. add nodes with the newer version and remove the old nodes.

#### Kubeadm upgrade

```bash
$ kubeadm upgrade plan
list all details of upgrading. but we have to upgrade minor updates.

$ apt-get upgrade -y kubeadm=1.12.0-00
download all the necessary images for the upgrade.

$ kubeadm upgrade apply v1.12.0
upgrade you cluster 

Now upgrade the kubelet on master node.
$ apt-get upgrade -y kubelet-1.12.0-00

$ systemctl restart kubelet

Now the worker nodes. first resceduled node one by on eand update.
$ kubectl drain node-1

$ apt-get upgrade -y kubeadm=1.12.0-00

$ apt-get upgrade -y kubelet=1.12.0-00

$ kubeadm upgrade node config --kubelet-version v1.12.0

$ systemctl restart kubelet

$ kubectl uncordon node-1
repeat the same steps for all worker nodes.
```

### Backup and Restore Methods

#### Backup Methods

1. Resource configuration.

    ```bash
    k get all --all-namespaces -o yaml > all-deploy-service.yaml
    ```

2. ETCD cluster
store/backup state of cluster. There is --data-dir path in etcd.service where state of etcd data is stored.

    ```bash
    $ ETCDCTL_API=3 etcdctl snapshot save snapshot.db
    store snapshot in current directory.

    $ ETCDCTL_API=3 etcdctl snapshot status snapshot.db
    to see the snapshot status.

    To restore the cluster from the backup first stop the kube-api server.
    $ service kube-apiserver stopped

    $ ETCDCTL_API=3 etcdctl snapshot resotre snapshot.db \
      --data-dir /var/lib/etcd-from-backup
    then configure the etcd.service file to use the new data dir.
    YOU CAN GO TO ETCD POD MANIFEST FILE AND MAKE CHANGES TO HOST PATH. 

    $ systemctl daemon-reload

    $ service etcd restart

    $ service kube-apiserver start
    ```

    To make use of etcdctl for tasks such as back up and restore, make sure that you set the `ETCDCTL_API` to 3. You can do this by exporting the variable `ETCDCTL_API` prior to using the etcdctl client. This can be done as follows:

    `export ETCDCTL_API=3`

    For example, if you want to take a snapshot of etcd,
    use: `etcdctl snapshot save -h` and keep a note of the mandatory global options.

    Since our ETCD database is TLS-Enabled, the following options are mandatory:

    - **--cacert** verify certificates of TLS-enabled secure servers using this CA bundle
    - **--cert** identify secure client using this TLS certificate file.
    - **--endpoints=[127.0.0.1:2379]** (check endpoint from etcd pod first) This is the default as ETCD is running on master node and exposed on localhost 2379.
    - **--key**  identify secure client using this TLS key file.

3. Persistent Volume

> **&#9432;** valaria is third party solution for backup.

Example:

```bash
$ etcdctl snapshot save --endpoints=127.0.0.1:2379 \
> --cacert=/etc/kubernetes/pki/etcd/ca.crt \
> --cert=/etc/kubernetes/pki/etcd/server.crt \
> --key=/etc/kubernetes/pki/etcd/server.key \
> /opt/snapshot-pre-boot.db

$ etcdctl snapshot restore /opt/snapshot-pre-boot.db --data-d
ir /var/lib/etcd-from-backup
Then change etcd pod manifest file from static pod directory and add host path to /var/lib/etcd-from-backup.
```

```bash
$ k config get-context
get list of all context.

$ k config use-context {context-name}
```

<span style="color:green">
How many nodes are part of ETCD cluster that etcd-server is a part of?
</span>

```bash
First export to API=3 then,
get info from 
$ ps -ef | grep -i etcd
$ etcdctl --endpoint --cacert --cert --key member list

$ etcdctl --endpoints=192.17.124.14:2379 --cacert=/etc/etcd/pk
i/etcd.pem --cert=/etc/etcd/pki/etcd.pem --key=/etc/etcd/pki/etcd-key.pem member list
```

<span style="color:green">
Restore Backup on etcd-server
</span>

```bash
go to etcd server and load data using snapshot restore command and then. 
change the permission of --data-dir path to etcd
$ chown -R etcd:etcd dir
now i have to change the path of data-dir in  etcd service file
$ vi /etc/systemd/system/etcd.service
$ systemctl daemon-reload
$ systemctl restart etcd
Now restart all control plan component - delete all pod from control plane component
```

Reference:

- [Backing up an etcd cluster](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#backing-up-an-etcd-cluster)

- [Recovery](https://github.com/etcd-io/website/blob/main/content/en/docs/v3.5/op-guide/recovery.md)

- [Disaster Recovery from your kubernetes cluster](https://www.youtube.com/watch?v=qRPNuT080Hk)

## Security

### Security Primitives

Secure Hosts,

- Password based authentication disabled.
- SSH key based authentication.

Secure Kubernetes cluster, entrypoint to cluster is kube-apiserver. So limit control access to kube-apiserver.

- Authentication, Who can access
  - Files - Username and Passwords
  - Files - Username and tokens
  - Certificates
  - External Authentication providers - LDAP
  - service Accounts

- Authorization, what can they do?
  - RBAC Authorization
  - ABAC Authorization
  - Node Authorization
  - Webhook Mode

### Authentication

Types of users accessing the cluster - User(Admins, Developers) and Bots.
you can't create user accounts using command but you can create for Bots account.
All the request to cluster are going through kube-apiserver. It has Auth mechanisms to authenticate users.

Static password file - is to create user-details.csv file by storing password, username, and userId. then pass this filename as a option to kube-apiserver. (in kube-apiserver.service file specifies --basic-auth-file=user-details.csv). When making request to api's specify username and password in curl command.

```bash
curl -v -k https://master-node-ip:6443/api/v1/pods -u "user1:password123"
```

Static Token file - you can specify token in place of password in csv  file (kube-apiserver.service file specify --token-auth-file=user-token-details.csv)

```bash
curl -v -k https://master-node-ip:6443/api/v1/pods --header "Authorization: Bearer kpadfalfjalsjflasjf;l"
```

#### Basic Auth Mechanism: 

Setting up Basic Authentication,

- Create a file with user details locally at `/tmp/users/user-details.csv`

  ```bash
  # User File Contents
  password123,user1,u0001
  password123,user2,u0002
  password123,user3,u0003
  password123,user4,u0004
  password123,user5,u0005
  ```

- Edit the kube-apiserver static pod configured by kubeadm to pass in the user details. The file is located at `/etc/kubernetes/manifests/kube-apiserver.yaml`

  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: kube-apiserver
    namespace: kube-system
  spec:
    containers:
    - command:
      - kube-apiserver
        <content-hidden>
      image: k8s.gcr.io/kube-apiserver-amd64:v1.11.3
      name: kube-apiserver
      volumeMounts:
      - mountPath: /tmp/users
        name: usr-details
        readOnly: true
    volumes:
    - hostPath:
        path: /tmp/users
        type: DirectoryOrCreate
      name: usr-details
  ```

- Modify the kube-apiserver startup options to include the basic-auth file

  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    creationTimestamp: null
    name: kube-apiserver
    namespace: kube-system
  spec:
    containers:
    - command:
      - kube-apiserver
      - --authorization-mode=Node,RBAC
        <content-hidden>
      - --basic-auth-file=/tmp/users/user-details.csv
  ```

- Create the necessary roles and role bindings for these users:

  ```yaml
  ---
  kind: Role
  apiVersion: rbac.authorization.k8s.io/v1
  metadata:
    namespace: default
    name: pod-reader
  rules:
  - apiGroups: [""] # "" indicates the core API group
    resources: ["pods"]
    verbs: ["get", "watch", "list"]
  
  ---
  # This role binding allows "jane" to read pods in the "default" namespace.
  kind: RoleBinding
  apiVersion: rbac.authorization.k8s.io/v1
  metadata:
    name: read-pods
    namespace: default
  subjects:
  - kind: User
    name: user1 # Name is case sensitive
    apiGroup: rbac.authorization.k8s.io
  roleRef:
    kind: Role #this must be Role or ClusterRole
    name: pod-reader # this must match the name of the Role or ClusterRole you wish to bind to
    apiGroup: rbac.authorization.k8s.io
  ```

- Once created, you may authenticate into the kube-api server using the users credentials

  ```bash
  curl -v -k https://localhost:6443/api/v1/pods -u "user1:password123"
  ```

### How to generate certifate for cluster? 

#### Generate for CA

1. Create private key using openssl.

  ```bash
  $ openssl genrsa -out ca.key 248
  ca.key
  ```

2. Certificate Signing Request.

  ```bash
  $ openssl req -new -key ca.key -subj "/CN=KUBERNETES-CA" -out ca.csr
  ca.csr
  ```

3. Sign certificate

  ```bash
  $ openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt
  ca.crt
  ```

#### Gerate for Admin User

1. Create private key using openssl.

  ```bash
  $ openssl genrsa -out admin.key 248
  admin.key
  ```

2. Certificate Signing Request.

  ```bash
  $ openssl req -new -key admin.key -subj "/CN=kube-admin" -out admin.csr
  admin.csr
  ```

3. Sign certificate

  ```bash
  $ openssl x509 -req -in admin.csr -CA ca.crt -CAkey ca.key -out ca.crt
  ca.crt
  ```

**What you will do with these certificate ?**
you can pass these to API request instead of username and password.

```bash
curl https://kube-apiserver:6443/api/v1/pods \
  --key admin.key --cert admin.crt --cacert ca.crt
```

or you can pass these certificate in `kube-config.yaml` file register with the endpoint.

```yaml (kube-config.yaml)
apiVersion: v1
clusters:
- cluster:
    certificate-authority: ca.crt
    server: https://kube-apiserver:6443
  name: kubernetes
kind: Config
users:
- name: kubernetes-admin
  user:
    client-certificate: admin.crt
    client-key: admin.key
```

#### Generates key for ETCD server

Search

#### Generate key for Admin

```bash
$ openssl genrsa -out apoiserver.key 2048
apiserver.key

$ openssl req -new -key apiserver.key -subj \
  "/CN=kube-apiserver" -out apiserver.csr
pass diferent name in openssl.conf file.

$ openssl x509 -req -in apiserver.csr \
  -CA ca.crt -CAkey ca.key -out apoiserver.crt
```

To view the certification details - go to the path of certificate and open with ssl to view certificate.

```bash
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -nout
```

To view logs,

```bash
$ jouralct -u etcd.service -l
if you setup the cluster from scratch by yourself.

$ k logs etcd-master
if you setup the cluster using kubeadm
```

### Generate Certificate for User

**Create a certificateSigningRequest object for new user.**

- user creates is key-value pair and sends admin to issue certificate for it.
- Admin create a certificateSigningRequest object and sends to Certificate API.
- administrator can see the signing request by running the `k get csr`
- Aprove the reqest `k certificate approve name`
- Now you can view this certificate, `k get csr jane -o yaml`
- decode the certificate `echo "certificate" | base64 --decode`

which component is responsible for all certificate related operation - **Controller Manager.**

```bash
To denied the request 
$ k certificate deny csr-name
```

### KubeConfig

config file has 3 section,

- Clusters : (Development, Production, Google)
- Contexts : it defines which user accounts can be used to access which clusters (Admin@Production)
- Users : (Admin, Dev User, Prod User)

> current-context used to specify current context to work on.

you can pass kube-config file to set it as default for kubectl.

```bash
k config view --kubeconfig=my-custom-config
```

To change context

```bash
k config use-context prod-user@production
```

To switch context on config file.

```bash
$ k config --kubeconfig=path_to_config use-context context_name
This will change the current context in config file

$ k config --kubeconfig=path_to_config current-context
show the current context.
```

### Authorization

#### 1. RBAC (Role Based Access Control)

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer
rules:
- apiGroups: [""] # for core groups leave blank.
  resources: ["pods"] # define resource
  verbs: ["list","get","create","update","delete"]

- apiGroups: [""]
  resources: ["configMap"]
  verbs: ["create"]
```

Link user to the Role by creating RoleBinding object.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: devuser-developer-binding
subjects: # define user here.
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
```

```bash
$ k get roles
list all roles

$ k get rolebindings

$ k describe role developer

$ k describe rolebindings {name}

$ k auth can-i create deployments
to check whether you have permission or not.

$ k auth can-i delete nodes

$ k auth can-i create deployments --as dev-user
to check whether the dev-user have permission to create deployment
```

> You can restrict access to pod by specifying resourcename field in role object defination.

```bash
# to creat role and bind the role.
k create role developer --namespace=default --verb=list,create,delete --resource=pods

k create rolebinding dev-user-binding --namespace=default --role=developer --user=dev-user
```

## Storage

### Persistent Volume 

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-log
spec:
  capacity:
    storage: 100Mi
  accessModes:
  - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /pv/log
```

### Storage class

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: google-storage
provisioner: kubernetes.io/gce-pd
parameters:
  type: pd-standard
  replication-type: none
```

## Network

```bash
$ ip link
to see interfaces.

$ ip addr add [ip-addr] dev eth0
to assign ip addr

$ route 
display the routing table.

by default no linux system forword traffic to other interface.
$ cat /proc/sys/net/ipv4/ip_forward
0 : no forwarding 

To allow forwarding set it to 1.
$ echo 1 > /proc/sys/net/ipv4/ip_forward

$ cat /etc/resolve.conf
dns nameserver 

$ ip netns add red
add red namespace to system.

$ ip netns
to list namespace

$ ip netns exec red ip link
execute ip link in red namespace.

To create connection between two namespace 
you need a line or link. (virtual ethernet)
$ ip link add veth-red type veth peer name veth-blue
veth-red ----------------------- veth-blue.

Now attached both end to namespace
$ ip link set veth-red netns red
$ ip link set veth-blue netns blue

Now assign ip address to each namespace.
$ ip -n red addr add 192.168.15.1 dev veth-red
$ ip -n blue addr add 192.168.15.2 dev veth-blue

Now up the link.
$ ip -n red link set veth-red up
$ ip -n blue link set veth-blue up

$ ip netns exec red ping 192.168.15.2

$ ip netns exec red arp
```

What if i have many namespaces how to connect them?
use virtual switch - Linux Bridge.

```bash
$ ip link add v-net-0 type bridge
create a virtual bridge in host machine.

$ ip link set dev-net-0 up

Now connect each namespace to bridge network.
$ ip link add veth-red type veth peer name veth-red-br

$ ip link add veth-blue type veth peer name veth-blue-b

Now attach these link to bridge.
$ ip link set veth-red netns red
$ ip link set veth-red-br master v-net-0
do same for each namespace

Now attach ipaddr.
$ ip -n red addr add 192.168.15.1 dev veth-red
$ ip -n blue addr add 192.168.15.2 dev veth-blue

Now up the link.
$ ip -n red link set veth-red up
$ ip -n blue link set veth-blue up
```

Q - Create a network policy to allow traffic from the Internal application only to the payroll-service and db-service. Use the spec given below. You might want to enable ingress traffic to the pod to test your rules in the UI. Also, ensure that you allow egress traffic to DNS ports TCP and UDP (port 53) to enable DNS resolution from the internal pod.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: internal-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      name: internal
  policyTypes:
  - Egress
  - Ingress
  ingress:
    - {}
  egress:
  - to:
    - podSelector:
        matchLabels:
          name: mysql
    ports:
    - protocol: TCP
      port: 3306

  - to:
    - podSelector:
        matchLabels:
          name: payroll
    ports:
    - protocol: TCP
      port: 8080

  - ports:
    - port: 53
      protocol: UDP
    - port: 53
      protocol: TCP
```