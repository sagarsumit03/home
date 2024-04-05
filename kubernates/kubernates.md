
#  Kubernates:

## Images:

Docker Image is _an executable package of software that includes everything needed to run an_ application.
####  Image pull policy[](https://kubernetes.io/docs/concepts/containers/images/#image-pull-policy)
`IfNotPresent`
`Always`
`Never`
#### Default image pull policy[](https://kubernetes.io/docs/concepts/containers/images/#imagepullpolicy-defaulting)
- if you remove the  `imagePullPolicy`, then  `imagePullPolicy`  is automatically set to  `IfNotPresent`.
-   if you rneove the  `imagePullPolicy`  field, and the tag for the container image is  `:latest`,  `imagePullPolicy`  is automatically set to  `Always`;
-  if you omit the  `imagePullPolicy`  field, and you don't specify the tag for the container image,  `imagePullPolicy`  is automatically set to  `Always`;
-   if you remove the  `imagePullPolicy`  field, and you specify the tag for the container image that isn't  `:latest`, the  `imagePullPolicy`  is automatically set to  `IfNotPresent`.

####  ImagePullBackOff
The status `ImagePullBackOff` means that a container could not start because Kubernetes could not pull a container image. The `BackOff` part indicates that Kubernetes will keep trying to pull the image, with an increasing back-off delay.


## Ingress vs API Gateway:

Ingress manages and route the traffic into Kubernetes services. Ingress as Nginx server which just do the work of forwarding the traffic to services based on the ruleset (ingress rule). Ingress is k8s native.

![image](https://github.com/sagarsumit03/home/assets/8539646/3c576f30-daf1-41da-9b3b-55212e71ecb7)


API Gateway could be installed anywhere (although there are now many that run in Kubernetes natively like Ambassador, Gloo, Kong), and they do have more functionality available like Oauth, rate limiting, etc. 
An api gateway is used for application routing, rate limiting, security, request and response handling and other application related tasks.


## Deployment:

Deployment works one level above ReplicaSet object. Deployment is recommended for stateless application(web) services.

### Rolling Restart:
Deployment file for the app which require rolling restart. Need to make sure is to keep the `imagepullpolicy` as `always`

We can trigger rollout restarts by this:

`kubectl rollout restart deployment <deployment-name>` or,

`line:47` strategy type rolling restart with `maxSurge:1` meaning only 1 pod is created at a time.
`maxunavailable:1` only 1 pod is taken town at a time.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 10
  selector:
    matchLabels:
      app: nginx
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: hello-seattle:latest
          ports:
            - containerPort: 8080

```

With deployment you should be able to do rolling upgrade or rollback. You can update image from v1 to v2.

## Replicaset:

With ReplicaSet you define number of replicas you want to run. For a particular service. You would have those many replicas running.

when we reapply a replicaset with updated image there is no restart and the pods still use the old image.

```yaml
apiVersion:  apps/v1
kind:  ReplicaSet
metadata:
  name:  nginx-replicaset
spec:
  selector:
    matchLabels:
      app:  nginx
  replicas:  5  # tells deployment to run 2 pods matching the template
template:
  metadata:
    labels:
      app:  nginx
  spec:
    containers:
      - name:  nginx
        image:  nginx:1.14.2
        ports:
          - containerPort:  80
```

So from pod definition, we can observe that nginx is using 1.13.2. Now let's change image version to 1.14.2 in replicaset.yaml Again apply changes

```yaml
kubectl apply -f replicaset.yaml
kubectl get pods
kubectl describe pod <<name_of_pod>>

```

Now we don't see any changes in Pod and they are still using old image.


##  Daemonset

_DaemonSet_  ensures that all (or some) Nodes run a copy of a Pod. As nodes are added to the cluster, Pods are added to them. As nodes are removed from the cluster, those Pods are garbage collected. Deleting a DaemonSet will clean up the Pods it created.

Some typical uses of a DaemonSet are:

-   running a cluster storage daemon on every node
-   running a logs collection daemon on every node
-   running a node monitoring daemon on every node
  
##  Statefulset

Like a Deployment, a StatefulSet manages Pods that are based on an identical container spec. Unlike a Deployment, a StatefulSet maintains a sticky identity for each of its Pods. These pods are created from the same spec, but are not interchangeable: each has a persistent identifier that it maintains across any rescheduling.

For stateful applications or those requiring stable network identifiers and persistent storage, StatefulSets are the ideal choice.


> Replicated databases are a good example of the scenarios that StatefulSets accommodate. One Pod acts as the primary database node, handling both read and write operations, while additional Pods are deployed as read-only replicas.

1.  Assume you deployed a MySQL database in the Kubernetes cluster and scaled this to three replicas, and a frontend application wants to access the MySQL cluster to read and write data. The read request will be forwarded to three Pods. However, the write request will only be forwarded to the first (primary) Pod, and the data will be synced with the other Pods. You can achieve this by using StatefulSets.
2.  Deleting or scaling down a StatefulSet will not delete the volumes associated with the stateful application. This gives you your data safety. If you delete the MySQL Pod or if the MySQL Pod restarts, you can have access to the data in the same volume.
![StatefulSets architecture with persistent volume](https://loft.sh/images/blog/posts/stateful-set-bp-2.png?nf_resize=fit&w=1040)


![MySQL primary and replica architecture](https://loft.sh/images/blog/posts/stateful-set-bp-5.png?nf_resize=fit&w=1040)

Example of stateful set pointing to a single pod `line:133`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mongodb
spec:
  type: LoadBalancer
  selector:
    statefulset.kubernetes.io/pod-name: mongodb-0
  ports:
    - port: 27017
      targetPort: 27017

---
apiVersion: v1
kind: Secret
metadata:
  name: mongo-password
type: opaque
stringData:
  MONGO_INITDB_ROOT_PASSWORD: secret
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mongodb
spec:
  selector:
    matchLabels:
      app: mongodb
  serviceName: mongodb
  replicas: 3
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
        - name: mongodb
          image: mongo:latest
          ports:
            - name: mongodb
              containerPort: 27017
          volumeMounts:
            - name: data
              mountPath: /data/db
          env:
            - name: MONGO_INITDB_ROOT_USERNAME
              value: admin
            - name: MONGO_INITDB_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mongo-password
                  key: MONGO_INITDB_ROOT_PASSWORD
  volumeClaimTemplates:
    - metadata:
        name: data
      spec:
        accessModes: ["ReadWriteOnce"]
        resources:
          requests:
            storage: 1Gi

```

Example of stateful set pointing to all pods:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mongodb
spec:
  type: LoadBalancer
  selector:
    app: mongodb
  ports:
    - port: 27017
      targetPort: 27017

---
apiVersion: v1
kind: Secret
metadata:
  name: mongo-password
type: opaque
stringData:
  MONGO_INITDB_ROOT_PASSWORD: secret
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mongodb
spec:
  selector:
    matchLabels:
      app: mongodb
  serviceName: mongodb
  replicas: 3
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
        - name: mongodb
          image: mongo:latest
          ports:
            - name: mongodb
              containerPort: 27017
          volumeMounts:
            - name: data
              mountPath: /data/db
          env:
            - name: MONGO_INITDB_ROOT_USERNAME
              value: admin
            - name: MONGO_INITDB_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mongo-password
                  key: MONGO_INITDB_ROOT_PASSWORD
  volumeClaimTemplates:
    - metadata:
        name: data
      spec:
        accessModes: ["ReadWriteOnce"]
        resources:
          requests:
            storage: 1Gi


## kubectl port-forward svc/mongodb 8080:27017
## forwarding port
```
### headless service:
when client  connect to your service, they're connecting directly to the pods.

A headless service doesn't provide any sort of proxy or load balancing -- it simply provides a mechanism by which clients can look up the ip address of pods. This means that when they connect to your service, they're connecting directly to the pods; there's no intervening proxy.

Consider a situation in which you have a service that matches three pods; e.g., I have this Deployment:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: example
  name: example
spec:
  replicas: 3
  selector:
    matchLabels:
      app: example
  template:
    metadata:
      labels:
        app: example
    spec:
      containers:
      - image: docker.io/traefik/whoami:latest
        name: whoami
        ports:
        - containerPort: 80
          name: http
```
If I'm using a typical ClusterIP type service, like this:

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: example
  name: example
spec:
  ports:
  - name: http
    port: 80
    targetPort: http
  selector:
    app: example
```
Then when I look up the service in a client pod, I will see the ip address of the service proxy:

```yaml
/ # host example
example.default.svc.cluster.local has address 10.96.114.63
```

However, when using a headless service, like this:

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: example
  name: example-headless
spec:
  clusterIP: None
  ports:
  - name: http
    port: 80
    targetPort: http
  selector:
    app: example
```
I will instead see the addresses of the pods:

```yaml
/ # host example-headless
example-headless.default.svc.cluster.local has address 10.244.0.25
example-headless.default.svc.cluster.local has address 10.244.0.24
example-headless.default.svc.cluster.local has address 10.244.0.23
```

You can use a headless service to deploy a database cluster, with each replica of the database having its own IP address. This allows clients to connect directly to the replicas of the database without having to go through a load balancer or a single instance of the database.


> Kubernetes Services of type ClusterIP, NodePort and LoadBalancer have one thing in common: They loadbalance between all pods that match the service's selector

Typically, a service has a single IP address and load balancer that directs incoming traffic to the appropriate backend pods.

Headless services expose a set of endpoints for a service without the need for a load balancer or a single IP address. Instead of having a single IP address pointing to a load balancer or a single instance of the service, headless services expose a set of IP addresses for each replica of the service. This allows clients to connect directly to the replicas of the service, bypassing the need for a load balancer or a single instance of the service.

--- 
Cluster IP service type is used when we want to expose pods **only inside kuberntes cluster**, so pods will not be available to the outside world. Usually database pods are managed by ClusterIP services as we don't want database endpoints to be exposed to the outside world.

As you can notice below the declaritive definition for a service, this service has a selector of (layer: db), So only pods with label (layer: db) will match the service, means any request coming to this service will be redirected to one of these pods:

```yaml
apiVersion: v1
kind: Service 
metadata: 
  name: postgres-db 
  namespace: db
  spec: 
    selector: 
      layer: db 
      type: ClusterIP
```


NodePort service type exposes the service to the outside world through <NodeIP>.<NodePort>. The default range of the NodePort is (30000–32767). It is an optional field in the definition of the service. Each node of the cluster proxies that port to the kubernetes service port. Don't get confused here about the difference between **port, targetPort and nodePort**. So "port" is the port of the service, "targetPort" is the port of the pod. The request flow will start from the nodePort, proxied to the port then forwarded to the targetPort.

```yaml
apiVersion: v1
kind: Service 
metadata: 
  name: my-service 
    spec: 
      type: NodePort 
      selector: 
        app: MyApp
      ports: 
        - port: 8080 
        targetPort: 80 
        nodePort: 30007
```

Loadbalancer service type exposes the service through a loadbalancer resource issued by the cloud provider (AWS, Azure, GCP, etc). Once this laodbalancer is created, its DNS name will be assigned to the service. Traffic will come to the loadbalancer, then redirected to the service to be forwarded to the pods.


> Loadbalancer service type exposes the service through a loadbalancer resource issued by the cloud provider (AWS, Azure, GCP, etc). Once this laodbalancer is created, its DNS name will be assigned to the service. Traffic will come to the loadbalancer, then redirected to the service to be forwarded to the pods.

---

![Components of Kubernetes](https://kubernetes.io/images/docs/components-of-kubernetes.svg)
  
##  Control Plane Components

### kube-apiserver

The API server is a component of the Kubernetes control plane that exposes the Kubernetes API.

### etcd

Consistent and highly-available key value store used as Kubernetes' backing store for all cluster data.

### kube-scheduler

Control plane component that watches for newly created Pods with no assigned node, and selects a node for them to run on.

### kube-controller-manager
Control plane component that runs controller processes.  
There are many different types of controllers. Some examples of them are:
- Node controller: Responsible for noticing and responding when nodes go down.
- Job controller: Watches for Job objects that represent one-off tasks, then creates Pods to run those tasks to completion.
- ServiceAccount controller: Create default ServiceAccounts for new namespaces.

## Node Components:
### kubelet[](https://kubernetes.io/docs/concepts/overview/components/#kubelet)

An agent that runs on each  [node](https://kubernetes.io/docs/concepts/architecture/nodes/)  in the cluster. It makes sure that  [containers](https://kubernetes.io/docs/concepts/containers/)  are running in a  [Pod](https://kubernetes.io/docs/concepts/workloads/pods/).

### kube-proxy[](https://kubernetes.io/docs/concepts/overview/components/#kube-proxy)

kube-proxy is a network proxy that runs on each  [node](https://kubernetes.io/docs/concepts/architecture/nodes/)  in your cluster, implementing part of the Kubernetes  [Service](https://kubernetes.io/docs/concepts/services-networking/service/)  concept
