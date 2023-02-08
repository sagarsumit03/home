# Kubernates Basics:

### Key Features of Kubernates:

```
* Horizontal Scaling
* Auto Scaling
* Health Check
* Load Balancer
* Service Discovery
```

---

### VM Vs Container:

To make use of full computing power and resources of a physical server, the concept called Hypervisor-based Virtualization

came into the picture, where hypervisor layer is introduced on top of the operating system which allows us to install multiple virtual machines on top of the single physical server.

![https://miro.medium.com/max/535/1*UTZ3K5Yn4-8Fzs3Ht57t7Q.png](https://miro.medium.com/max/535/1*UTZ3K5Yn4-8Fzs3Ht57t7Q.png)

Each VM would have it’s own operating system and use it’s own allocated compute and memory. This approach is very cost-effective and easy scaling.

![https://miro.medium.com/max/1024/1*LL8jHPZY2dkIPkbWo-Z-ww.png](https://miro.medium.com/max/1024/1*LL8jHPZY2dkIPkbWo-Z-ww.png)

The main difference between VM and Container is as Above.

> Here each application is running in its own copy of kernel. But with containers, instead of virtualizing the underlying computer like a virtual machine (VM), just the OS is virtualized. Containers share host OS and this is much more efficient and light weighted.
> 

---

![https://miro.medium.com/max/720/1*LFMMBlUysm87TjdHlrlMTQ.jpeg](https://miro.medium.com/max/720/1*LFMMBlUysm87TjdHlrlMTQ.jpeg)

---

## IMAGE:

A Docker image is containing everything needed to run an application as a container. This includes:

```markdown
* code
* runtime
* libraries
* environment variables
* configuration files

The image can then be deployed to any Docker environment and executable as a container. The Extension is .img 
```

---

## CONTAINERS:

Programs running on Kubernetes are packaged as Linux containers.

```
An instance of an image is called a container. You have an image, which is a set of layers as you describe. If you start this image, you have a running container of this image. You can have many running containers of the same image.
```

> Containerization allows you to create self-contained Linux execution environments. Any program and all its dependencies can be bundled up into a single file.
> 

Multiple programs can be added into a single container, but you should limit yourself to one process per container if at all possible.

---

## PODS:

Unlike other systems, Kubernetes doesn’t run containers directly; instead it wraps one or more containers into a higher-level structure called a pod.`Any containers in the same pod will share the same resources and local network.`

> A Kubernetes pod is a group of containers, and is the smallest unit that Kubernetes administers. Pods have a single IP address that is applied to every container within the pod. Containers in a pod share the same resources such as memory and storage.
> 

`Pods can hold multiple containers, but you should limit yourself when possible. Because pods are scaled up and down as a unit, all containers in a pod must scale together, regardless of their individual needs.`

pods should remain as small as possible, typically holding only a main process and its tightly-coupled helper containers (these helper containers are typically referred to as “side-cars”). Implementing `side car Pattern` of Microservices.

---

## CONTAINER RUNTIME:

To run containers in Pods, Kubernetes uses a container runtime. As Docker runtime.

> It provides updates control as well as rollback functionalities. This means you can update or downgrade an application to the desired version without experiencing a user blackout as well as roll back to the previous version in case the new version is unstable or filled with bugs.
> 

---

## DEPLOYMENTS:

Kubernetes deployments define the scale at which you want to run your application by letting you set the details of how you would like pods replicated on your Kubernetes nodes.

> When a deployment is added to the cluster, it will automatically spin up the requested number of pods, and then monitor them. If a pod dies, the deployment will automatically re-create it.
> 

A Deployment has the following features:

- Create a ReplicaSet and Pods
- Update a ReplicaSet
- Scale-up/down a Deployment
- Pause/continue a Deployment
- Rollback to the previous version
- Clean up unwanted ReplicaSets

---

## REPLICA SET:

A ReplicaSet’s purpose is to maintain a stable set of replica Pods running at any given time. As such, it is often used to guarantee the availability of a specified number of identical Pods.

> ReplicaSet helps bring up a new instance of a Pod when the existing one fails, scale it up when the running instances are not up to the specified number, and scale down or delete Pods if another instance with the same label is created. A ReplicaSet ensures that a specified number of Pod replicas are running continuously and helps with load-balancing in case of an increase in resource usage.
> 

//[https://www.kubermatic.com/blog/introduction-to-replicasets-deployment/](https://www.kubermatic.com/blog/introduction-to-replicasets-deployment/)

---

## SERVICE:

As pods are replaced, their internal names and IPs might change. A service exposes a single machine name or IP address mapped to pods whose underlying names and numbers are unreliable. A service ensures that, to the outside network, everything appears to be unchanged.

---

## NODE:

A Kubernetes node manages and runs pods.

> In most production systems, a node will likely be either a physical machine in a datacenter, or virtual machine hosted on a cloud provider like Google Cloud Platform.
> 

---

## CLUSTER:

A cluster is all of the above components put together as a single unit.

---

## INGRESS:

By default, Kubernetes provides isolation between pods and the outside world. If you want to communicate with a service running in a pod, you have to open up a channel for communication. This is referred to as ingress.

> here are multiple ways to add ingress to your cluster. The most common ways are by adding either an Ingress controller, or a LoadBalancer.
> 

NodePort and LoadBalancer let you expose a service by specifying that value in the service’s type. Ingress, on the other hand, is a completely independent resource to your service. You declare, create and destroy it separately to your services.

This makes it decoupled and isolated from the services you want to expose. It also helps you to consolidate routing rules into one place.

---

## PERSISTENT VOLUMES:

Because programs running on your cluster aren’t guaranteed to run on a specific node, data can’t be saved to any arbitrary place in the file system.

If a program tries to save data to a file for later, but is then relocated onto a new node, the file will no longer be where the program expects it to be. For this reason, the traditional local storage associated to each node is treated as a temporary cache to hold programs, but any data saved locally can not be expected to persist.

Instead, local or cloud drives can be attached to the cluster as a Persistent Volume. This can be thought of as plugging an external hard drive in to the cluster. `Persistent Volumes provide a file system that can be mounted to the cluster, without being associated with any particular node.`.

## Main commands for getting info of Java app on Kubernates:

1. To get basic information about your pods you can use this simple command:

```
    $ kubectl get pods
    NAME                         READY     STATUS    RESTARTS   AGE
    guestbook-75786d799f-fg72k   1/1       Running   0          7m

```

1. But you can get much more information if you describe a specific pod, like this:

```
      $ kubectl describe pod <your-pod-name>
      Name:           guestbook-75786d799f-fg72k
      Namespace:      default
      Node:           10.47.84.98/10.47.84.98
      Start Time:     Sun, 19 Aug 2018 12:56:23 +0300
      Labels:         pod-template-hash=3134283559
                       run=guestbook
      Annotations:    kubernetes.io/psp=ibm-privileged-psp
      Status:         Running
      ...

```

1. To see the logs, you can run this simple command:

```
      $ kubectl logs <your-pod-name>
      [negroni] listening on :3000
      [negroni] 2018-08-19T11:55:39Z | 200 |   332.277µs | 173.193.106.55:32412 | GET /
      [negroni] 2018-08-19T11:55:39Z | 200 |   140.407µs | 173.193.106.55:32412 | GET /style.css
      [negroni] 2018-08-19T11:55:39Z | 200 |   123.595µs | 173.193.106.55:32412 | GET /script.js
      [negroni] 2018-08-19T11:55:39Z | 200 |   87.508µs | 173.193.106.55:32412 | GET /lrange/guestbook
      [negroni] 2018-08-19T11:55:39Z | 404 |   74.307µs | 173.193.106.55:32412 | GET /favicon.ico
      [negroni] 2018-08-19T11:57:30Z | 304 |   89.418µs | 173.193.106.55:32412 | GET /
      [negroni] 2018-08-19T11:57:30Z | 200 |   60.671µs | 173.193.106.55:32412 | GET /lrange/guestbook
      [negroni] 2018-08-19T12:06:23Z | 304 |   152.557µs | 173.193.106.55:32412 | GET /
      [negroni] 2018-08-19T12:06:23Z | 200 |   94.091µs | 173.193.106.55:32412 | GET /lrange/guestbook

```
