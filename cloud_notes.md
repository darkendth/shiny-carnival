# Introduction to Cloud Infrastructure Technologies

## Virtualization

### Cloud Deployment Models

- Private Cloud
    : built using a software stack like Openstack.
- ***Public Cloud***
- ***Hybrid Cloud***
- Community Cloud
    : formed by multiple organization sharing a common cloud infrastructure.
- Distributed Cloud
    : formed by distributed systems connected by a single network.
- Multicloud
    : One organization uses multiple public cloud providers to run its workload.
- Poly Cloud
    : One organization uses multiple public cloud providers to leverage specific services from each provider.

### Virtual Machine

Virtual machines are created with the help of specialized virtualizaiton software, a `hypervisor` that runs on host machine.

A hypervisor is software capable of creating multiple isolated virtual operating environments, each composed of virtualized resources that are then made available to guest systems. Hypervisors are classified into two main categories:

==Type-1 hypervisor== runs directly on top of a physical host machine's hardware without need for a host OS. AWS Nitro, Xen, Red Hat Virtualization...

==Type-2 hypervisor== runs on top of the host's OS. VirtualBox, VMware workstation...

Some hypervisors matching both categories, They are Linux kernel modules that act as both type-1 and type-2 hypervisors at the same time.

- KVM (Kernel-based Virtual Machine) - is a loadable virtualization module of the linux kernel and it converts the kernel into a hypervisor capable of managing guest virtual machines.

- bhyve

Virtual systems may be created with the help of `emulators` as well. Software emulation that occurs at both system and user space levels, supported by a tool **QEMU**. It allows for any OS to run on any architecture or for programs to run on originally unsupported systems.

### Vagarant

Vagrant by HashiCorp helps us automate VMs management by providing an end-to-end lifecycle management utility - the vagrant command line tool. Key components,

- vagrantfile
    : describes how virtual machines shoud be configured and provisioned. It is a text file with the Ruby syntax, with all the information about configuring and provisioning a set of machines.

- Boxes
    : are the package format(image) for the vagrant environment

- Providers
    : are underlying engines or hypervisors used to provision VMs or containers.

- Synced Folders
    : we can sync a directory on the host system with a VM, which helps the user manage shared files/directories easily.

- Provisioning
    : allow us to automatically install software and make configuration changes after the machine is booted.

- Plugings
    : to extend the functionality of vagrant.

- Networking
    : provides networking options for port forwarding, network connectivity, and network creation.

## Infrastructure as a Service (IaaS)

- Amazon ec2
- Microsoft Azure
- DigitalOcean Droplets
- Google Compute Engine
- IBM Cloud Virtual Servers (always free)
- Linode Compute Instances
- Oracle Cloud Compute Virtual Machines (always free)
- OpenStack: Allows users to build a cloud computing platform for both public and private clouds. Open source software platform.
- Vultr: (250$ free credit)

## Platform as a Service (PaaS)

Allow users to develop, run and manage applications while concealing the tasks involved in the management of the underlying infrastructure.

- `Cloud Foundary` (CF) - open source PaaS framework aimed at developers in large organizations, that is portable to any cloud.
- `Cloud Foundary BOSH` - open source tool for release engineering, deployment, lifecycle management and monitoring of complex distributed systems, enabling developers to easily version, package and deploy software in a reproducible manner.
- `Korifi` - aims to abstract CF componenets and dependency configuratoin, enabling the developer to focus on building applications.
- `Red Hat OpenShift` - open source PaaS solution provided by Red Hat. It is built on top of the container technology orchestrated by Kubernetes. 
- `Heroku` - fully-managed container-based cloud platform with integrated data services and a strong ecosystem. It is used to deploy and run enterprise level modern applications. It encapsulated the application in a dyno, whihc is a lightweight and secure container, the application can be scaled based on demand.

## Containers

In the container world, this box containing our application source code and all its dependencies and libraries is referred to as an image. A running instance of this box is referred to as a container. We can run multiple containers from the same image.

An image contains the application, its dependencies and the user-space libraries like *glibc* enable switching from the user-space to kernel-space. An image does not contain any kernel-space component.

### Building Blocks

- **Namespaces** - wraps a particular global system resource like network, process Ids in an abstraction, that make it appear to the processes within the namespace that they have their own isolated instance of the global resource.

- **cgroups** - *Control groups* are used to organize processes hierarchically and distribute system resources along the hierarchy in a controlled and configurable manner.

- **Union filesystem** - The Union filesystem allows files and directories of separate filesystems, known as layers, to be transparently overlaid on top of each other, to create a new virtual filesystem. At runtime a container is made of multiple layers merged to create a read-only filesystem. On top of a read-only filesystem, a container gets a read-write layer, which is an ephemeral layer and it is local to the container.

> Linux achieve the process isolation through chroot.

### Container Runtimes

- runc
    : a CLI tool for spawning and running containers according to specifications for runtime and image. It create and start container.

- crun
    : A much faster and low-memory footpring OCI-conformant runtime written in C.

- containerd
    : OCI-compliant container runtime. it runs as a daemon and manages the entire lifecycle of containers.

- CRI-O
    : OCI-compatible runtime, which is an implementation of the kubernetes CRI. It is a lightweigh high-level runtime alternative to using Docker as the runtime for kubernetes.

### Containers vs VMs

virtual machine provisioned with the help of *hypervisor*, the software layer that virtualizes a host system's hardware resources, such as CPU, memory and networking to allow for guest OS to be installed and take advantage of them. 

Containers run directly as processes on the host OS. there are no middle layers, which help containers to achieve near-native performance.

### Docker

The Docker Platform is a collection of development tools that follow a client-server architecture, with a Docker Client connecting to a Docker Host server that runs the Docker daemon to execute commands for containers and images management in response to client requests.

#### Basic Docker Operations

```bash
$ docker image ls
list images

$ dokcer image pull apline
pulling an alipine image from the registry into the local cache.

$ docker container run -it apline sh
run a container from image.

$ docker container run -d nginx
run a container in detached mode.

$ docker container ls
list only running containers

$ docker containers ls -a
list all containers

$ docker container exec -it <container_id/name> bash
start a bash shell in interactive terminal mode inside the container

$ docker container stop <container id/name>

$ docker container rm <container id/name>
```

### Podman

Podman is a daemonless container engine for running and managing OCI compliant containers and container images on Linux systems. It can run containers both as a root and a non-privileged user.

The images for podman and docker are stored in different locations. Hence, podman images won’t list the images previously pulled by docker. The images for podman are stored in /var/lib/containers for root users and ~/.local/share/containers for non-privileged users. The storage location is different from docker (/var/lib/docker) because it’s storage structure is based on the OCI standards. The images for docker and podman are compatible, but they are stored differently on the system.

Podman have two additional open source tools to operate within the container images landscape.

- **Buildah** - supports container image builds one step at a time by taking an interactive approach to processing Dockerfile instruction.

- **Skopeo** - tool designed to work with container images in both local and remote repositories.

![docker vs podman](imges/1.png)

the Docker CLI interacts with the Docker daemon prior to invoking the containerd daemon responsible to run the runc runtime, while Podman is a daemonless tool capable of running the runc runtime without an intermediary daemon such as containerd.

Basic podman operations are similar with docker. you can simply set alias docker=podman also.

### Aditional CLI Tools

==nredctl client==, advertised as a Docker-compatible client for containerd aims to make containerd more user friendly. **crictl** client supports CRI-O

#### Basic nerdctl and crictl operations

```bash
$ nerdctl images
list images available in local cache

$ nerdctl pull docker.io/library/alpline
pulling alpine image from docker hub registry into local cache

$ nerdctl run -it alpine sh
run a container from image

$ nerdctl run -d docker.io/library/nginx
run in background

$ crictl images 
list images in local cache

$ crictl pull alpine
pull image

$ crictl run -t alpine sh

$ crictl ps 
list running containers.
```

### Project Moby

It is an open source project that provides a framework for assembling different container systems to build a container platform like Docker. Individual container systems provide features such as image, container, and secret management.

Moby is particularly useful for engineers who want to build their own container-based system, to customize and patch an existing Docker build, or just to experiment with the latest container technologies

## Micro Operating Systems (Micro OSes)

Once we remove the packages that are not essential to boot the base OS and to run container-related services, we are left with specialized OSes, which are refered to as Micro OSes for containers.

### Alpine Linux

Built on top of the capabilities of BusyBox, Alpine Linux combines the security features of a Linux-based Operating System with a collection of small footprint, yet fully functional utilities. Although small, between 5 MB to 8 MB per container, or 130 MB as a minimal standalone OS install, it is more resource-efficient than typical distributions.

Once prepared for reboot it can be configured to boot in one of the three available runtime modes.

- *diskless mode* - the default mode, where the entire system run from RAM.

- *data mode* - Mostly runs from RAM but mounts /var as writable data partitions.

- *sys mode* - Typical hard-disk install that mounts /boot, swap and /.

### BusyBox

The Swiss Army Knife of Embedded Linux, combines very small version of several popular UNIX utilities into a single compact executable. Although not an Operating System in itself, BusyBox was designed to run on Linux and to enhance a lightweight OS with tools needed to help developers troubleshoot and debug their containerized applications.

### Fedora CoreOS

It combines the best of both CoreOS Container Linux and Fedora Atomic Host (FAH) while aiming to provide the best container host to run containerized workloads securely and at scale. Fedora CoreOS is a minimal operating system for running containerized workloads, that updates automatically and is available on multiple platforms.

FCOS offers multiple installation methods:

- *Cloud launchable* - launch directly on Amazon's AWS platform
- *Bare metal and virtualized* - for bare-metals installs from ISO.
- *for cloud operators*

### Flatcar Container Linux 

Container-optimized OS with a minimal image size, including only the tools needed to run containers. it's greatest strength are its immutable filesystem and automated atomic updates.

### k3OS (kubernetes Operating system)

is a Linux distribution that aims to minimize the OS maintenance tasks in a Kubernetes cluster. It was designed to work with Rancher's K3s lightweight Kubernetes distribution.

The k3OS speeds up the K3s cluster boot time. At boot time the k3OS image becomes available to K3s and the cluster takes full control over all the nodes' maintenance, eliminating the need to log in to each individual node for upgrades or other maintenance activities.

### Ubuntu Core Overview

lightweight version of Ubuntu, predominantly designed for IoT embedded devices but also found in large container deployments. size is arround 260MB. Top features are:

- Immutable image for simple and consistent installation and deployment.
- Isolated applications run with explicit permissions, such as read-only access to the filesystem.
- Transactional updates for signed, autonomous, atomic, and flexible updates.
- Security implemented at snap level, from build and distribution to deployment.

Snaps are secure, isolated, dependency-free, portable, software packages for Linux. They even include their own filesystems. Snaps benefits include:

- Automatic updates.
- Automated recovery in the case of failed updates.
- Critical update provision for unscheduled updates.
- Flexible hardware and network conditions support for unpredictable systems, including redundancy for roll-backs, and autonomous bootstrapping.

The snap packaging environment includes the following:

- *snap* - the application package format and the command line interface (CLI)
- *snapd* - the background service managing and maintaining snaps
- *snapcraft* - the framework including the command to build custom snaps
- *Snap Store* - the repository to store and share snaps.

### VMware Photon OS

is a minimal Linux container host provided by VMware, optimized for cloud-native applicaitons. It is designed with a small footprint in order to boot extremely quickly on VMware vSphere deployments and on public cloud computing platforms.

## Container Orchestrations

To manage containerized workloads, run containers in a multi-host environment at scale, which introduce new set of concern,

- How to group multiple host together to form a cluster, and manage them as a single compute unit?
- How to schedule containers to run on specific hosts?
- How can containers running on one host communicate with containers running on other hosts?
- How to access containers over a service name, instead of accessing them directly through their IP addresses?

Container Orchestration tools, together with different plugins, aim to address these concerns. Container orchestration is an umbrella concept that encompasses container scheduling and cluster management. Container scheduling becomes a policy-driven mechanism that automates the decision process that distributes containers across the nodes of the clusters.

Available Solutions for container orchestration are:

- Kubernetes
- Docker Swarm
- Nomad
- Amazon ECS

### Kubernetes Hosted Solutions and Platforms

All the management tasks would be performed by the cloud services providers. several hosted solutions available for kubernetes:

- Amazon Elastic Kubernetes Service (EKS)
- Azure Kubernetes Servic (AKS) {only pay for worker node}
- Google Kubernetes Engine (GKE)

### Docker Swarm

A native container orchestration solution from Docker, Inc. Docker, in swarm mode, logically groups multiple Docker Engines into a swarm, or cluster, that allows application to be deployed and managed at scale. Two major components of swarm:

- *Swarm Manager Nodes*
- *Swarm Worker Nodes*

## Unikernels

With unikernels, we can select the part of the kernel needed to run with the specific application. The unikernel image becomes a single address space executable, including both application and kernel components.

The Unikernel goes one step further than other technologies, creating specialized virtual machine images with just:

- The application code
- The configuration files of the application
- The user-space libraries needed by the application
- The application runtime (like JVM)
- The system libraries of the unikernel, which allow back and forth communication with the hypervisor.

According to the protection ring of the x86 architecture, we run the kernel on ring0 and the application on ring3, which has the least privileges. Ring0 has the most privileges, like access to hardware, and a typical OS kernel runs on that. With unikernels, a combined binary of the application and the kernel runs on ring0.

Unikernel images would run directly on hypervisors like Xen, or on bare metal, based on the unikernels' type. unikernel implementation divided into two categories:

- Specialized and purpose-build unikernels
- Generalized 'fat' unikernels

**NonoVMs** is an open source project that aims to promote unikernels. It uses the ops CLI to build and run unikernels, or nanos, across several public cloud providers and on-premises.

## MicroServices

Microservices are light-weight, independent processes that collectively form complex application solutions. These independent processes communicate with each other through language agnostic APIs. Services are small building blocks, independently deployed, loosely coupled, that aim to perform simple tasks.

### Refactoring a Monolith Into Microservices

refactor a monolith into microservices, there are a few guidelines to follow:

- If you have a complex monolith application, then it is not advisable to rewrite the entire application from scratch. Instead, you should start carving out services from the monolith, which implement the desired functionalities for the code we take out from the monolith. Over time, all or most functionalities will be implemented in the microservices architecture.
- We can split the monoliths based on the business logic, front-end (presentation), and data access. In the microservices architecture, it is recommended to have a local database for individual services. And, if the services need to access the database from other services, then we can implement event-driven communication between these services. 
- As mentioned earlier, we can split the monolith based on the modules of the monolith application. Each time we do it, our monolith shrinks.

## Software-Defined Networking (SDN)

To connect devices, applications, VMs, and containers, we need a similar level of flexibility which can only be achieved by virtualizing the networking.

SDN decouples the network control layer from the traffic forwarding layer. This allows SDN to program the control layer and to create custom rules in order to meet these new networking requirements.

### SDN Architecture

In networking, there are three distinctive planes:

- Data Plane
The Data Plane, also called the Forwarding Plane, is responsible for handling data packets and applying actions to them based on rules which we program into lookup-tables. 
- Control Plane
The Control Plane is tasked with calculating and programming the actions for the Data Plane. This is where the forwarding decisions are made and where services such as Quality of Service (QoS) and VLANs are implemented. 
- Management Plane 
The Management Plane is the place where we can configure, monitor, and manage network devices.

Every network device has to perform three distinct activities:

- *Ingress and egress packets*
These are performed at the lowest layer, which decides what to do with ingress packets and which packets to forward based on forwarding tables. These activities are mapped as Data Plane activities. All routers, switches, modems, etc., are part of this plane.
- *Collect, process, and manage the network information*
By collecting, processing, and managing the network information, the network device makes the forwarding decisions, which the Data Plane follows. These activities are mapped by the Control Plane activities. Some of the protocols which run on the Control Plane are routing and adjacent device discovery.
- *Monitor and manage the network*
Using the tools available in the Management Plane, we can interact with the network device to configure it and monitor it with tools like SNMP (Simple Network Management Protocol).

The Control Plane has well-defined APIs that receive requests from applications to configure the network. After preparing the desired state of the network, the Control Plane communicates that to the Data Plane (also known as the Forwarding Plane), using a well-defined protocol like OpenFlow.

### Networking for Containers

The host uses the network namespace feature of the Linux Kernel to isolate the network from one container to another on the host system.

On a single host, when using the virtual Ethernet (vEth) feature with Linux bridging, we can give a virtual network interface to each container and assign it an IP address.

multi-host networking with containers can be achieved by using some form of Overlay network driver, which encapsulates the Layer 2 traffic to a higher layer. Examples of this type of implementation are the Docker Overlay Driver, Flannel, and Weave.

Two distinct standards have been proposed for container networking.

- Container Network Model (CNM)
Docker, Inc. is the primary driver for this networking model implemented using the *libnetwrok* project. Libnetwork standardizes the container network creation process through three main build components: a network sandbox, one or multiple endpoints, and one or multiple networks. Libnetwrks is used in one of the following modes,
    1. *NULL* - NOOP implementation of the driver. It is used when no networking is required.
    2. *Bridge* - It provides a Linux-specific bridging implementation based on Linux Bridge.
    3. *Ovelay/Swarm* - It provides multi-host communication over VXLAN.
    4. Remote - It does not provide a driver. Instead, it provides a means of supporting drivers over a remote transport, by which we can write third-party drivers.
- Container Networking Interface (CNI)
It is a Cloud Native Computing Foundation (CNCF) project which consists of specifications and libraries for writing plugins to configure network interfaces in Linux containers, along with a number of supported plugins. It is limited to providing network connectivity of containers and removing allocated resources when the container is deleted. As such, it has a wide range of support. It is used by projects like Kubernetes, OpenShift, and Cloud Foundry. 

### Service Discovery

Service discovery is a mechanism that enables processes and services to find each other automatically and to talk to each other. With respect to containers, it is used to map a container name with its IP address so that we can access the container directly by its name without worrying about its exact location (IP address), which may change during the life of the container.

Service discovery is achieved in two steps:

- *Registration*
When a container starts, the container scheduler registers the container name to the container IP mapping in a key-value store such as etcd or Consul. And if the container restarts or stops, the scheduler updates the mapping accordingly.
- *Lookup*
Services and applications use Lookup to retrieve the IP address of a container so that they can connect to it. Generally, this is supported by DNS (Domain Name Server), which is local to the environment. The DNS used resolves the requests by looking at the entries in the key-value store, which is used for Registration. Consul, CoreDNS, SkyDNS, and Mesos-DNS are examples of such DNS services.

### Single-Host Networking

```bash
$ docker network ls
NETWORK ID          NAME          DRIVER
6f30debc5baf        bridge        bridge
a1798169d2c0        host          host
4eb0210d40bb        none          null
```

bridge,null and host are disting network drivers available on a single Docker host.

#### Bridge

By default, Docker creates a docker0 Linux 
bridge network. Each container running on a single host receives a unique IP address from this bridge network unless we specify some other network with the --net= option. Docker uses Linux's virtual Ethernet (vEth) feature to create a pair of two virtual interfaces, with the interface on one end attached to the container and the interface on the other end of the pair attached to the docker0 bridge network.

```bash
$ ifconfig
Display the network configuration after
installing Docker on a signle host should
reveal the default bridge network.

$ docker container run -it --name=c1 busybox /bin/sh
create a new container.

$ ip a
list its IP address.
```

The new container received its IP address from the private IP address range 172.17.0.0/16, which is created by the bridge network.

```bash
$ docker network inspect bridge
inspect a network to list detailed information
about it. container c1 appears to be connected
to the bridge network.

$ docker network create --driver bridge my_bridge
It creates a custom Linux bridge on the host system.

$ docker container run --net=my_bridge -itd --name=c2 busybox 
create a container and use the newly created network

```

#### NULL Driver

NULL means no networking. If we run a container with the null driver, then it would just get the loopback interface. It would not be accessible from any other network.

```bash
docker container run -it --name=c3 --net=none busybox /bin/sh
```

#### Host Driver

The host driver allows us to share the host machine's network namespace with a container. By doing so, the container would have full access to the host's network, which is not a recommended approach due to its security implications. Running an ifconfig command inside the container lists all the interfaces of the host system:

```bash
docker container run -it --name=c4 --net=host  busybox /bin/sh

ifconfig
```

#### Sharing Container Network Namespaces

Similar to host, we can share network namespaces among containers. As a result, two or more containers can share the same network stack and reach each other through localhost.

```bash
docker container run -it --name=c5 busybox /bin/sh
```

If we start a new container with the `--net=container:CONTAINER` option, the second container will show the same IP address.

```bash
docker container run -it \
--name=c6--net=container:c5 busybox /bin/sh
```

> Kubernetes, uses the feature detailed above to share the same network namespaces among multiple containers in a pod.

#### Multi-Host Networking

Docker also supports multi-host networking which allows containers from one Docker host to communicate with containers from another Docker host when they are part of a Swarm. By default, Docker supports two drivers for multi-host networking:

- Docker Overlay Driver
With the Overlay driver, Docker encapsulates the container's IP packet inside a host's packet while sending it over the wire. While receiving, Docker on the other host decapsulates the whole packet and forwards the container's packet to the receiving container. This is accomplished with libnetwork, a built-in VXLAN-based overlay network driver.

- Macvvlan Driver
With the Macvlan driver, Docker assigns a MAC (physical) address for each container and makes it appear as a physical device on the network. As the containers appear in the same physical network as the Docker host, we can assign them an IP from the network subnet as the host. As a result, direct container-to-container communication is enabled between different hosts. Containers can also directly talk to hosts

### Kubernetes Networking

Kubernetes assigns a unique IP address to each Pod. Containers in a Pod share the same network namespace, thus sharing the same IP address of the Pod, and can refer to each other by localhost.

As each Pod gets a unique IP, Kubernetes assumes that Pods should be able to communicate with each other, irrespective of the nodes they get scheduled on. There are different ways to achieve this. Kubernetes introduced the Container Network Interface (CNI) specification for container networking.

The projects implementing the Kubernetes CNI are in fact SDN implementations, deployed on a Kubernetes cluster as plugins, that build a private, virtual, and isolated network layer for Pod-to-Pod communication. Several implementations of Kubernetes networking plugins:

- AWS VPS CNI for Kubernetes
- Azure CNI for Kubernetes
- Calico
- Cilium
- Flannel
- NSX-T
- OVN-Kubernetes
- Weave Net

### Cloud Foundry: Container-to-Container Networking

Gorouter routes the external and internal traffic to various Cloud Foundry (CF) components. The container-to-container networking feature of CF enables application instances to communicate with each other directly. However, when the container-to-container networking feature is disabled, all application-to-application traffic must go through the Gorouter.

## Software Define Storage

SDS is a software controller that controls your physical storage by virtualizing it.

Software-defined storage (SDS) represents storage virtualization aimed to separate the underlying storage hardware from the software that manages and provisions it. Physical hardware from various sources can be combined and managed with software, as a single storage pool. SDS replaces static and inefficient storage solutions backed directly by physical hardware with dynamic, agile, and automated solutions. In addition, SDS may provide resiliency features such as replication, erasure coding, and snapshots of the pooled resources. Once the pooled storage is configured in the form of a storage cluster, SDS allows multiple access methods such as File, Block, and Object.

Some examples of Software-Defined Storage implementations are:

- Ceph
- Gluster
- LINBIT
- MinIO
- Nexenta
- OpenEBS
- Soda Dock
- TrueNAS
- VMware vSAN.

### Ceph

Ceph is an open source distributed storage system that supports applications with different storage interface needs, as it provides object, block, and filesystem storage in a single unified storage cluster, making Ceph flexible, highly reliable, and easy to manage.

Ceph uses the CRUSH (Controlled Replication Under Scalable Hashing) algorithm to deterministically find, write, and read the location of objects.

we will take closer look at Ceph's architecture:

- Reliable Autonomic Distributed Object Store (RADOS)
It is the object store which stores the objects. This layer makes sure that data is always in a consistent and reliable state. It performs operations like replication, failure detection, recovery, data migration, and rebalancing across the cluster nodes. This layer has the following three major components:

1. Object Storage Device (OSD): This is where the actual user content is written and retrieved with read operations. One OSD daemon is typically tied to one physical disk in the cluster.
2. Ceph Monitors (MON): Monitors are responsible for monitoring the cluster state. All cluster nodes report to Monitors. Monitors map the cluster state through the OSD, Place Groups (PG), CRUSH, and Monitor maps.
3. Ceph Metadata Server (MDS): It is needed only by CephFS, to store the file hierarchy and metadata for files. 

- Librados
It is a library that allows direct access to RADOS from language like C,C++,...
- Ceph Block Device (RBD)
This provides the block interface for Ceph. It works as a block device and has enterprise features like thin provisioning and snapshots.
- RADOS Gateway (RADOSGW)
This provides a REST API interface for Ceph, which is compatible with Amazon S3 and OpenStack Swift.
- Ceph File System (CephFS)
This provides a POSIX-compliant distributed filesystem on top of Ceph. It relies on Ceph MDS to track the file hierarchy.

### GlusterFS Overview

Gluster can utilize common off-the-shelf hardware to create large, distributed storage solutions for media streaming, data analysis, and other data- and bandwidth-intensive tasks.

To create shared storage, we need to start by grouping the machines in a trusted pool. Then, we group the directories (called bricks) from those machines in a GlusterFS volume using FUSE (Filesystem in Userspace). GlusterFS supports different types of volumes:

- Distributed GlusterFS volume
- Replicated GlusterFS volume
- Distributed replicated GlusterFS volume
- Dispersed GlusterFS volume
- Distributed dispersed GlusterFS volume.

The GlusterFS volume can be accessed using one of the following methods:

- Native FUSE mount
- NFS (Network File System)
- CIFS (Common Internet File System).

### Docker storage Drivers

When a container is created from an image. Docker creates an additional Container layer on top of the Image layers . Container layer is a read/write layer, that stores any data generated by the container during the runtime. If the containers need to modify anything which is under the Image layers, then it is copied into the Container layerfor modifying the image as per requirement. This procedure is also called the copy-on-write mechanism. Because of the copy-on-write mechanism, Image layers always stays intact. Therefore, an image can be used by multiple containers.

Docker uses storage drivers to store Image layers and store data in the writable layer of a container. The container layer does not persist after the container is deleted, but it is suitable for storing ephemeral data that is generated at runtime.

For persistent storage of docker container data, there are two options: volumes and bind mounts.

- Volumes
    : It creates a new volume under /var/lib/docker/volumes directory. And persist container data after a container is deleted or stopped.

```bash
$ docker volume create <volume-name>
create a volume 

$ docker volume ls
list the available volumes

$ docker run -d --name <container-name> \
-v <source-volume-name>:<target-valume-name> \
<image-name>:<tag>
mount the volume to a container (using -v option or --mount option)

$ docker run -d  --name webserver1 \
-v newVolume01:/usr/share/nginx/html nginx:latest

$ docker run -d \
--name webserver1 \
--mount source=newVolume01,target=/usr/share/nginx/html \
nginx:latest
using mount option

$ docker volume inspect <volume-name>
ispect a volume

$ docker volume rm <volume-name>
remove a volume
```

> If the mounted volume does not exist on the /var/lib/docker/volumes location during the creation of a container. It will be automatically created.

- Bind mounts
    : Using the bind mount, a file or directory from the host machine can be mounted into a container.

```bash
$ mkdir var/local/data
used it to mount to a container.

$ docker run -d --name <container-name> \
-v <directory or file path>:<target-volume-name> \
<image-name>:<tag>

$ docker run -d  --name webserver1 \
-v /var/local/data:/usr/share/nginx/html nginx:latest

---

$ docker run -d \
--name <container-name> \
--mount type=bind,source=<directory-path>,target=<target-volume-name> \
<image-name>:<tag>

docker run -d \
--name webserver1 \
--mount type=bind,source=/var/local/data,target=/usr/share \
nginx:latest
```

Docker uses the copy-on-write mechanism when containers are started from container images. The container image is protected from direct edits by being saved on a read-only filesystem layer. All of the changes performed by the container to the image filesystem are saved on a writable filesystem layer of the container. Docker images and containers are stored on the host system, and we can choose the storage driver for Docker storage, depending on our requirements. Docker supports the following storage drivers on Linux:

- BtrFS
Supports snapshots.
- Device Mapper
For earlier CentOS and RHEL releases.
- Fuse-OverlayFS
Preferred for rootless mode.
- Overlay2
Preferred for all supported Linux distributions (Ubuntu, Debian, CentOS, Fedora, RHEL, SLES 15).
- VFS (Virtual File System)
For testing only, not for production.
- ZFS
Supports snapshots.

Docker supports several options for storing files on a host system:

- Volumes
On Linux, volumes are stored under the /var/lib/docker/volumes directory, and they are directly managed by Docker. Volumes are the recommended method of storing persistent data in Docker.
- Bind Mounts
Allow Docker to mount any file or directory from the host system into a container.
- Tmpfs
Stored in the host's memory only but not persisted on its filesystem, recommended for non-persistent state data.
In the cases of volumes and bind mounts, Docker bypasses the Union Filesystem by using the copy-on-write mechanism. The writes happen directly to the host directory.

### Podman Storage Drivers

Podman is also capable of using the copy-on-write mechanism when containers are run. The container image is saved on a read-only filesystem layer, while all changes performed by the container are saved on a writable filesystem layer of the container.

Podman supports several mount types for storing container files on a host system:

- Volumes
Volumes are stored under the /var/lib/containers/storage/volumes directory for root and under the $HOME/.local/share/containers/storage/volumes directory for regular users. They are directly managed by Podman and represent the recommended method of storing persistent data with Podman.
- Bind
Podman can mount a named volume from the host system into the container.
- Tmpfs
Stored in the host's memory only but not persisted on its filesystem, and the content is erased when the container is stopped.
- Image
To mount an OS image file.
- Devpts
To mount a filesystem storing pseudoterminal (telnet, ssh, xterm) data.

```bash
$ podman container run -d --name=web \
-v webvol:/webdata myapp:latest
create a volume in the podman working directory of the current user and mount it to the container at mount point.

$ podman container inspect web

$ podman container run -d --name=web -v /mnt/webvol:/webdata myapp:latest

$ podman volume create container-volume
create a volume

$ podman volume ls
listing volume

$ podman container run -v ocntainer-volume:/data \
-it --name data-container alpine sh
```

### Volume Management in Kubernetes

Kubernetes uses volumes to attach external storage to containers managed by Pods. A volume in Kubernetes is linked to a Pod and shared among containers of that Pod. The volume has the same lifetime as the Pod, but it outlives the containers of that Pod, meaning that data remains preserved across container restarts. However, once the Pod is deleted, the volume and all its data are lost as well.

A volume mounted inside a Pod is backed by an underlying volume type. A volume type decides the properties of the volume, such as size and content type. Some of the volume types supported by kubernetes are:

- awsElasticBlockStore
- azureDisk
- azureFile
- cephfs
- configMap
- emptyDir
- hostPath
- nfs
- rbd

> type and learn more about it.

### Cloud Foundry Volume Service

On cloud Foundry, applications connect to other services via a service marketplace. Each service has a service broker, which encapsulates the logic for creating, managing and binding services to applications.

## DevOps and CI/CD

The collaborative work between Developers and Operations is referred to as DevOps. DevOps is more of a mindset, a way of thinking, versus a set of processes implemented in a specific way.

DevOps practitioners are involved with various development workflows, such as Continuous Integration (CI), Continuous Delivery (CD), and Continuous Deployment (CD). The CI represents a flow of code merge, build, and automated testing operations performed frequently and quickly. The Continuous Delivery (CD) flow follows the CI, and it automates the code deployment to a staging environment, expecting a manual deployment for the production release. The key difference between this CD and the Continuous Deployment (CD) is the production release method - manual for Continuous Delivery (CD) while automated for the Continuous Deployment (CD). The automation of the Deployment process means frequent smaller releases that trigger faster customer feedback allowing for faster fixes to smaller bugs.

### Jenkins

It is one of the most popular automation tools, open source, written in Java that supports Continous Integration and Continous Delivery.

Jenkins supports the build of a pipeline - a concept representing an entire application lifecycle. A Jenkings Pipeline is a series of plugins that collectively implement CD.

### Travis CI

It is a hosted, distribution CI solution for projects hosted on Github, Bitbucket and more.

To run the test with CI, first we have to link our GitHub account with Travis and select the project (repository) for which we want to run the test. In the project's repository, we have to create a travis.yml file, which defines how our build should be executed step-by-step.

A typical job build with Travis consists of two major phases:

- install: to install any dependency or pre-requisite
- script: to run the build script

### Concourse

With Concourse, we run a series of containerized tasks to perform the desired operations. For containerization, it uses Docker and Docker-Compose, while the series of tasks, together with resources, allow us to build a job or pipeline.

### Cloud Native

In the Cloud Native approach, we design a package and run applications on top of our infrastructure (on-premises or public cloud) using operations tools like containers, container orchestration, and services like continuous integration, logging, monitoring, etc. Kubernetes integrated with several tools meets our requirements to run Cloud Native applications.

## Tools for Cloud Infrastructure

### Configuration Management

At any point in time, we may want to have a consistent and desired state of systems and software installed on them. This leads to the concept of *Infrastructure as Code*, which means that the infrastructure resources are defined in a declarative fashion in configuration files that ultimately can be re-used to be able to provision consistently reproducible systems across environments.

*Configuration management* tools allow us to define the desired state of the systems in an automated way.

#### Ansible

Red Hat's Ansible is an easy-to-use, open source configuration management (CM) tool. It is an agentless tool that works through SSH. In addition, Ansible can automate infrastructure provisioning (on-premises or public cloud), application deployment, and orchestration.

It let's you configure state or requirement of your cluster and take care of everything automatically to setup it. **Playbooks** are Ansible's configuration, deployment and orchestration language. Example of a playbook, which performs multiple tasks based on roles:

```bash
# This playbook deploys the whole application stack in this site. 
- name: apply common configuration to all nodes
  hosts: all
  remote_user: root

  roles:
    - common

- name: configure and deploy the webservers and application code
  hosts: webservers
  remote_user: root

  roles:
    - web

- name: deploy MySQL and configure the databases
  hosts: dbservers
  remote_user: root

  roles:
    - db
```

The Ansible management node connects to the nodes listed in the inventory file and runs the tasks included in the playbook. A management node can be installed on any Unix-based system like Linux or macOS. It can manage any node which supports SSH and Python.

#### Puppet

Puppet is an open source configuration management tool. It uses an agent/server model to configure the systems. The agent is referred to as the Puppet Agent, and the server is referred to as the Puppet Server. Puppet also supports the agentless model to manage target systems.

Puppet Agent needs to be installed on each system we want to manage/configure with Puppet. Each agent is responsible for:

- Connecting securely to the Puppet Server to pull updates and a series of instructions in a file referred to as the Catalog File.
- Performing operations from the Catalog File to get to the desired state.
- Sending back the status to the Puppet Server.

Puppet Server can be installed only on Unix-based systems. It is responsible for: 

- Compiling the Catalog File for hosts based on system, configuration, manifest file, etc. 
- Sending the Catalog File to Agents when they query the Server. 
- Storing information about the entire environment, such as host information, metadata such as authentication keys.
- Gathering reports from each Agent and then preparing the overall report.

#### Chef

Chef uses the client/server model or the clientless model to perform configuration management. The client is installed on each host that we want to manage and is referred to as Chef Client, while pulling updates from the server is referred to as Chef Server.

A Chef Cookbook is the basic unit of configuration that defines a scenario and contains everything that supports the scenario. Two of its most important components are recipes and attributes.

- Recipes
    : A recipe is the most fundamental unit for configuration, which mostly contains resources, resources names, attribute-value pairs, and actions.
- Attributes
    : An attribute helps us define the state of the node. After each chef-client run the node's state is updated on the chef server.

#### Salt

Salt is an open source configuration management system built on top of a remote execution framework. It can be used in a client/server model or agentless model as well. In a client/server model, the server pushes commands and configurations to all the clients in a parallel manner, which the clients run, returning back the status. In the agentless model, the server connects with remote systems via SSH, however, this model offers limited functionality.

Each managed client is referred to as a Salt minion, receiving configuration commands from the Master and reporting back results.

A central management server is referred to as a Salt master. Multi-master configuration is also supported.

### Build & Release

With source code management tools like Git, we can easily version the code and retrieve the same bits we saved in the past. This saves a lot of time and helps developers automate most of the non-coding activities, like creating automated builds, running tests, etc. Extending the same analogy to infrastructure would allow us to create a reproducible deployment environment, which is referred to as Infrastructure as a Code.

Infrastructure as a Code helps us create a near production-like environment for development, staging, etc. With some tooling around them, we can also create the same environments on different cloud providers.  

By combining Infrastructure as a Code with versioned software, we are guaranteed to have a reproducible build and release environment every time.

#### Terraform

Terraform is a tool that allows us to define the infrastructure as code. This helps us deploy the same infrastructure on virtual machines, bare metal, or cloud. It helps us treat the infrastructure as software. The configuration file can be written in HashiCorp Configuration Language(HCL).

If you wants to deploy your application which has many micro-service. you will choose infrastructure AWS and create your private network, ec2 instance and install docker on them. After setting up you will deploy applicaiton.

Tools for Infrastructure Provisioning.

1. Provisioning Infrastructure {DevOps}
2. Deploying Applicaiton {Software Developer}

Terraform used in first stage to auto setup infrastructure. allows reconfiguration/changes to infrastructure. Also used for replicating Infrastructure.

Difference between Ansible and Terraform ?

- Both are Infrastructure as a Code
- Both automate: provisioning, configuring and managing the infrastructure.
- Terraform is mainly infrastructure provisioning tool and has the ability to deploy apps.
- Ansible a configuration Tool
- Ansible is more mature whereas Terraform is relatively new.
- Terraform is more advanced in orchestration.

How does terraform work?
Terraform Architecture has two main component:

1. Core - take two input sources, TF-Config and state. matched current state with the desired state and make plan to what needs to be created/updated/destroyed.

2. Providers for specific tecnology - various cloud providers. uses providers to execute the plan.

```yaml
# configure aws provider
provider "aws" {
    version = "~> 2.0"
    region = "us-east-1"
}
# create a VPC
resource "aws_vpc" "example" {
    cidr_block = "10.0.0.0/16"
}
```

#### CloudFormation

a tool that allows us to define our infrastructure as code on Amazon AWS. The configuration files can be written in YAML or JSON format, while CloudFormation can also be used from a web console or from the command line.

AWS CloudFormation provides an easy method to create and manage resources on AWS, adding order and predictability to the provisioning and updating processes. CloudFormation uses templates to describe the AWS resources and dependencies needed to run applications. It also allows for resources to be modified and updated while applying version control to the AWS infrastructure, similar to software version control.

#### Bosh

BOSH was primarily developed to deploy the Cloud Foundry PaaS, but it can deploy other software as well (e.g., Hadoop). BOSH supports multiple Infrastructure as a Service (IaaS) providers. BOSH creates VMs on top of IaaS, configures them to suit the requirements, and then deploys the applications on them.

### Key-Value Pair Store

While building any distributed and dynamically-scalable environment, we need an endpoint which is a single point of truth. For example, we need such an endpoint if we want each node in a distributed environment to look up specific configuration values before performing an operation. For such cases, all the nodes can reach out to a central location and retrieve the value of a desired variable or key.

the Key-Value Pair Storage provides the functionality to store or retrieve the value of a key. Most of the Key-Value stores provide REST APIs to support operations like GET, PUT, and DELETE, which help with operations over HTTP.

#### etcd

etcd is an open source distributed key-value pair storage, and it uses the Raft consensus algorithm for communication between instances of distributed multi-instance scenerios.

etcd can be configured to run standalone or in a distributed cluster. it allows users or services to watch the value of a key, to then perform certain operations as a result of any change in that particular value.

#### Consul KV

Consul KV, of HashiCorp, is a distributed, highly-available system which can be used for service discovery and configuration.

Other than providing a distributed key-value store, it also provides features like:

- Service discovery in conjunction with DNS or HTTP
- Health checks for services and nodes
- Multi-datacenter support.

#### Zookeeper

ZooKeeper is a centralized service for maintaining configuration information, providing distributed synchronization together with group services for distributed applications.

ZooKeeper aims to provide a simple interface to a centralized coordination service that is also distributed and highly reliable. It implements consensus, group management, and presence protocols on behalf of applications.

### Image Building

In an immutable infrastructure environment, we prefer to replace an existing service with a new one, to fix any problems/bugs or to perform an update.

Whether we start a new service or replace an older service with a new one, we need the image from which the service can be started. These images should be created in an automated fashion.

#### Dockerfile (Docker)

Docker has a feature that allows it to read instructions from a text file in order to generate an image. Internally, it creates a container after each instruction and then commits it to persistent storage. The file with instructions is referred to as a Dockerfile. In order to build a container image from a Dockerfile the docker build command is run.

Typically, Dockerfiles start from a parent image or a base image, specified with the FROM instruction. A parent image is an image used as a reference, modified by subsequent Dockerfile instructions. Parent images can be built directly out of working machines, or with tools such as Debootstrap. A base image has FROM scratch in the Dockerfile.

A **multi-stage build** is an advanced feature of Docker, very usefult for optimizing the Dockerfiles and minimizing the size of a container image. we create a new Docker image in every stage. Every stage can copy files from images created either in earlier stages or from already available images.

#### Containerfile and Dockerfile (Podman)

A custom container image can be created with Podman by starting a container from a base image and, after making the required changes (e.g., installing the software), we can use the podman commit command to save it to persistent storage, resulting in a new container image. This is not a scalable and efficient solution.

Podman has a feature that allows it to read instructions from a text file and then generate the requested image. Internally, it creates a container after each instruction and then commits it to persistent storage. The file with instructions is referred to as a Containerfile or a Dockerfile, which makes Podman more flexible with the source it uses for the image build process.

If the image pull phase takes a longer time. alternative is to download the source of the image, the Dockerfile or Containerfile instead. Then the image needs to be build locally by the developer.

#### Containerfile and Dockerfile (Buildah)

Buildah has a feature that allows it to read instructions from a text file and then generate the requested image. Internally, it creates a container after each instruction and then commits it to persistent storage. The file with instructions is referred to as a Containerfile or a Dockerfile, which also makes Buildah flexible with the source it uses for the image build process

#### Packer

Is an oper source tool for creating virtual machine images from a configuration file for different platforms. Configuration files are written using HashiCorp Configuration Languages (HCL).

There are three steps to create virtual machines.

- Building the base image
- Provision the base image for configuration
- Perform optional post-build operations

### Debugging, Logging and Monitoring for Containerized Applications

It would be beneficial to have external tools for logging and monitoring instead of collecting them directly from individual containers. This may be achieved because each container runs as a process on the host OS, which has complete control of that process. Once we have collected the data from all the containers running on a system or in a cluster, like Kubernetes or Swarm, we can run a logical mapping of all collected logs and gain a system/cluster-wide visibility, known as observability.

Docker, Podman, nerdctl, and crictl have similar built-in command-line options that help with debugging, logging, and monitoring.

```bash
$ docker inspect
debugging

$ docker logs
logging

$ docker stats

$ docker top
monitoring
```

#### Sysdig

It provides an on-cloud and on-premises platform for DevOps security for containers, Kubernetes, monitoring and forensics through DevSecOps. sysdig is:
    "strace + tcpdump + htop + iftop + lsof + awesome sauce".

It has two open source tools along with their paid enterprise-class offerings.

- Sysdig
- Sysdig Monitor
- Sysdig Secure

#### cAdvisor

cAdvisor (Container Advisor) is an open source tool to collect resource usage and performance characteristics for the host system and running containers. It collects, aggregates, processes, and exports information about running containers.

We can enable the cAdvisor tool to run as a Docker container and start collecting statistics with the following command:

```bash
sudo docker run \
  --volume=/:/rootfs:ro \
  --volume=/var/run:/var/run:ro \
  --volume=/sys:/sys:ro \
  --volume=/var/lib/docker/:/var/lib/docker:ro \
  --volume=/dev/disk/:/dev/disk:ro \
  --publish=8080:8080 \
  --detach=true \
  --name=cadvisor \
  --privileged \
  –-device=/dev/kmsg \
  gcr.io/cadvisor/cadvisor:latest
```

And then point the browser to http://host_IP:8080 to get live statistics. cAdvisor exposes its raw and processed statistics via a versioned remote REST API.

#### Elasticsearch--

Elasticsearch is the distributed search and analytics engine at the core of the Elastic Stack. It is responsible for data indexing, search, and analysis. Kibana is an interface that enables interactive data visualization while providing insights into the data.

Elasticsearch provides real-time search and analytics for structured or unstructured text, numerical data, or geospatial data. It is optimized to efficiently store and index data in order to speed up the search process.

#### Fluentd--

an open source data collector, which lets us unify the data collection and consumption for a better use and understanding of data.

#### Datadog

Datadog provides monitoring and analytics with integrated security as a service for DevOps. Through its wide array of products, the Datadog platform can monitor the performance and security of the infrastructure, network devices, network performance, cloud workloads, databases, and the entire continuous integration (CI) environment.

#### Prometheus--

Prometheus is an open source tool used for system monitoring and alerting. Prometheus is suitable for recording any purely numeric time-series data. It works well for both machine-centric monitoring like CPU, memory usage, and monitoring of highly dynamic service-oriented architectures. It is primarily written in Go.

#### Splunk

Splunk includes a family of products aiming to deliver highly scalable and fast real-time insight into enterprise data. It allows for data aggregation and analysis, with unique investigative methods.Splunk can be deployed on-premises or in the public cloud, while also capable of scaling across many data sources. It provides interactive dashboards for users to visualize data and to set up actions and alerts.

#### OpenTelemetry--

OpenTelemetry is a set of tools, APIs, and SDKs that can be used to enable observability. It can create, retrieve, and export metrics, logs, and traces to help with software’s performance and behavior analysis.

#### Dynatrace

offers an AI-powered unified platform for observability, automation, and security. It monitors on-premises infrastructure, hybrid and public clouds, microservices and containers. It automates DevOps and DevSecOps activities by optimizing the integration between AI and Kubernetes to secure cloud native pipelines.

## Service Mesh--

Service mesh is a network communication infrastructure layer for a microservices-based application. When multiple microservices are communicating with each other, a service mesh allows us to decouple resilient communication patterns, such as circuit breakers and timeouts from the application code.

Service mesh is generally implemented using a sidecar proxy. A sidecar is a container that runs alongside the primary application and complements it with additional features like logging, monitoring, and traffic routing. In the service mesh architecture, the sidecar pattern implements inter-service communication, monitoring, or other features that can be decoupled and abstracted way from individual services.

Similar to SDN, a service mesh also features Data and Control Planes.

- Service Mesh Data Plane
- Service Mesh Control Plane

### Consul (by HashiCorp)

It aims to provide a secure multi-cloud service networking through automated network configuration and service discovery.

Consul's strength lies in its support to connect services running in multiple datacenters. It is highly available for fault tolerance and increased performance, while it supports thousands or tens of thousands of simultaneous client services.

### Envoy

It provies an L7 proxy and communication bus for large, modern, service-oriented architectures. Envoy has an out-of-process architecture, which means it is not dependent on the applicaiton code. It runs alongside the application and communicates with the application on localhost. (sidecar pattern)

Envoy can be configured as service and edge proxy. In the service type of configuration, it is used as a communication bus for all traffic between microservices. With the edge type of configuration, it provides a single point of ingress to the external world.

### Istio Overview

Istio is one of the most popular service mesh solutions. it is divided into following two planes.

- Data Plane
    : it is composed of a set of envoy proxies deployed as sidecars to provide a medium for communication and to control all network communication between microservices.
- Control Plan
    : It manages and configures proxies to route traffic, enforces policies at runtime, and collects telemetry. The control plane includes the citadel, gallery and pilot.

The main components of Istio are:

- Envoy Proxy
    : Istio uses an extended version of the Envoy proxy, which it implements using features like dynamic service discovery, load balancing, TLS termination, circuit breakers, health checks, etc. Envoy is deployed as sidecars.
- Istiod
    : Istiod provides service discovery, configuration, and certificate management.

### Kuma

Kuma is platform-agnostic, modern and universal open source control plane for Service Mesh. It can be easily set up on VMs, bare-metal, and on Kubernetes.

Kuma is based on the Envoy proxy - a sidecar proxy designed for cloud-native applications, that supports monitoring, security, and reliability for microservice application at scale. with Envoy used as a data plane, Kuma can handle any L4/L7 traffic to observe and route traffic between services.

Kuma includes the following two planes:

- Control-Plane
    : Kuma is a control-plane that creates and configures policies that manage services within the Service Mesh.
- Data-Plane
    : Implemented on top of the Envoy proxy, runs as an instance together with every service to process incoming and outgoing requests.

### Linkerd

It supports all features of the service meshes listed earlier. In addition, Linkerd can be installed per host or instance, as a replacement for the sidecar deployment in Kubernetes clusters.

### Traefik Mesh

It is a simple and easy to configure service mesh that provides traffic visibility and management inside a Kubernetes cluster.

Traefik Mesh improves cluster security through monitoring, logging, visibility, and access controls. Communication monitoring and tracing also help with traffic optimization and increased application performance.

Traefik Mesh being non-invasive by design does not require any sidecar containers to be injected into Kubernetes pods. Instead, it routes through proxy endpoints, called mesh controllers, that run on each node as dedicated pods. Traefik Mesh operates in conjunction with its own ingress controller, the Traefik Proxy.

### Tanzu Service Mesh

It aims to simplify the connectivity, security, and monitoring of applications on any runtime and on any cloud. In addition, as a modern distributed solution, it brings together application owners, DevOps, SRE, and SecOps.

### Mashery

By design Meshery adds a Management plane on top of the existing Control planes and Data planes of typical service mesh implementations. The Management plane provides federation, backend system integration, expanded policy and governance, continuous delivery integration, workflow, chaos engineering, and application performance tuning. This enables operators, developers, and service owners to realize the full potential of a service mesh and it enhances in-network intelligence of the Data plane.

With the help of adapters, Meshery supports a number of Service mesh implementations, such as Consul, Istio, Kuma, Linkerd, Traefik Mesh, and others. This multi-mesh management interface supports various aspects of each managed service mesh, such as lifecycle, workload, performance, configuration, patterns and practices, chaos and filters.

Meshery is deployed as containers on a Docker container host or on Kubernetes. It consists of the Meshery server that exposes the API to client requests. The server can either request information or invoke an Adapter operation as a result of a client request. Adapters are used to manage different infrastructure layers while the Operator is deployed on each managed Kubernetes cluster.

### Internet of Things

Similar to networking, computing has evolved for IoT. We would need to handle situations like the following:

- Latency between the actual device and the datacenter (cloud) where we store the actual data.
- Not sending unnecessary data, like frequent keep-alive messages from the devices/sensors to the backend datacenter.

To handle such situations, Distributed Computing Architecture has evolved to include edge and fog computing. By using edge and fog computing we bring computing applications, data, and services away from some central nodes (datacenter) to the other logical extreme (the edge) of the Internet.

With IoT devices, we generate a variety of data in large volumes at very high speeds, which makes very good use for Big Data technologies like Apache Hadoop. We can make smart decisions, then augment this via machine learning and artificial intelligence.

## Serverless Computing

Serverless computing or just serverless is a method of running applications without concerns about the provisioning of computer servers or any of the compute resources. However, behind the scenes, computer servers are most definitely involved.

Serverless computing requires compute resources to run applications, but the server management and capacity planning decisions are completely abstracted from developers and users, because they are time consuming and counterproductive.

In serverless computing, we generally write applications/funcitons that focus and master one particular task. We then upload that application on the cloud provider, which gets invoked via different events, such as HTTP request, webhooks..

### AWS Lambda

AWS Lambda is the serverless event-driven service offered through Amazon Web Services Compute collection of products. AWS Lambda can be triggered in different ways, such as an HTTP request, a new document upload to S3 bucket, a scheduled job, an AWS Kinesis data stream, a notification from AWS Simple Notification Service, or a REST API call through the Amazon API Gateway.

AWS managed to advance the efficiency and speed of AWS Lambda services, through the Firecracker virtualization technology. An open source project, Firecracker relies on a virtual machine monitor(VMM) that creates microVMs leveraging the Linux Kernel Virtual Machine(KVM). Firecracker aims for a minimalist design, reduces the memory footprint and attack surface of a VM, while improving security, startup times, and hardware utilization.

### Google Cloud Functions

An application that runs with Cloud Functions can connect to other cloud services. Cloud Functions is essentially a Function-as-a-Service (FaaS) offering and it is complemented by two additional services, App Engine and Cloud Run :

- *App Engine* allows users to build highly scalable applications on a fully managed serverless platform.
- *Cloud Run* is a managed compute platform for fast and secure deployment and scaling of containerized applications.

### Azure Functions

Azure Functions is part of Microsoft Azure’s serverless compute offering, together with Serverless Kubernetes and Serverless application environments.

- Azure Functions offers an event-driven environment. It is available as a managed service in Azure and Azure Stack, but it also works on Kubernetes, Azure IoT Edge, on-premises, and other clouds.
- Serverless Kubernetes allows users to create serverless, Kubernetes-based applications orchestrated with Azure Kubernetes Service (AKS) and AKS virtual nodes, based on the open source Virtual Kubelet project.
- Azure Container Apps allow containerized application deployments without infrastructure management.
- Serverless application environments allow the running and scaling of web, mobile, and API applications on any platform of your choice through Azure App Service.

### Serverless and the Containers

Container imags become a prominent choice for packaging serverless applications and container orchestrators became the preferred choice for executors. some list of projects that are using container to execute serverless applications.

- Azure Container Instances
- AWS Fargate

### Knative

Knative is an open source platform based on Kubernetes that allows for the deployment and management of serverless applications. It is portable, running on any Kubernetes distribution without vendor lock-in. It is implemented in the form of controllers that get installed on the Kubernetes cluster and then registers custom API resources with the Kubernetes API Server. Knative is flexible and it supports plugins for logging, monitoring, networking, and even service mesh. In addition, it can be used with popular tools and frameworks such as Django, Ruby on Rails, and Spring.

### OpenFaaS

OpenFaaS allows both microservices and functions deployment in containers, aided by a functions store and a templating system which enable collaboration, sharing, and reusing of both functions and code. Once the code is packaged in a Docker container image, it becomes a highly scalable endpoint that can be monitored as well.

## Distributed Tracing

Organizations are adopting microservices for agility, easy deployment, scaling, etc. However, with a large number of services working together, sometimes it becomes difficult to pinpoint the root cause of latency issues or unexpected behaviors. To overcome such situations, we would need to instrument the behavior of each participating service in our microservices-based application. After collecting and combining the instrumented data from each participating service, we should be able to gain visibility of the entire system, known as Observability. Full observability is achieved through metrics, logs, and distributed tracing.

### Tracing with OpenTelemetry

In general, a trace represents the details about a transaction, like how much time it took to call a specific function. A trace is referred to by the directed acyclic graph (DAG) of spans. Each span can be referred to as a timed operation between contiguous segments of work. In distributed tracing, each service would contribute to its own span of set of spans. A parent can start other spans, either in serial or in parallel.

![trace](imges/2.png)

However, it does not give us timing information and is difficult to understand when parallelism is involved. the following visualization presents service hierarchy, together with serial and parallel execution.

![trace parallelism](imges/trace2.png)

Using Open Telemetry, we collect tracing spans for our services and then forward them to different tracers. Tracers are then used to monitor and troubleshoot microservices-based applications. Some of the supported tracers for OpenTelemetry:

- Jaeger
- ServiceNow Cloud Observability
- Instana
- Elastic APM
- Aria by VMware

### Jaeger

Jaeger is an open source tracer which is compatible with the OpenTelemetry data model to support spans. It can be used for the following:

- Distributed content propagation
- Distributed transaction monitoring
- Root cause analysis
- Service dependency analysis
- Performance and latency optimizaiton.

## Tips

Though not everyone has to master all the topics we have discussed in this course, you should at least have a basic understanding of the following:

- Different cloud offerings (IaaS, PaaS, SaaS) and cloud models (public, private, and hybrid)
- Container technologies like Docker, Podman, Kubernetes, Nomad, Swarm, and their ecosystems
- DevOps, DevSecOps, GitOps
Continuous Integration (CI), Continuous Delivery (CD), and Continuous Deployment (CD) pipelines
- Cloud Native CI/CD
- Software Defined Networking and Storage
- Debugging, Logging, Monitoring, Telemetry of cloud applications.
