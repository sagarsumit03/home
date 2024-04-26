
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

## Copying files from pod to local system

	kubectl cp -n default my-pod:/path/to/file.txt .
-n : namespace `default`  
my-pod: pod name   
/path/to/file.txt: location of file in pod  
. : to current directory in laptop.  

and vice versa for copying from local to pod

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


### Persistant Volume:
1. #### Persistent Volume access modes[](https://spacelift.io/blog/kubernetes-persistent-volumes#persistent-volume-access-modes)
-   **`ReadWriteOnce (RWO)`** – The volume is mounted with read-write access for a _single_ Node in your cluster. Any of the Pods running on that Node can read and write the volume’s contents.
-   **`ReadOnlyMany (ROX)`** – The volume can be concurrently mounted to any of the Nodes in your cluster, with read-only access for any Pod.
-   **`ReadWriteMany (RWX)`** – Similar to ReadOnlyMany, but with read-write access.
-   **`ReadWriteOncePod (RWOP)`** – This new variant, introduced as a beta feature in [Kubernetes v1.27](https://kubernetes.io/blog/2023/04/11/kubernetes-v1-27-release/), enforces that read-write access is provided to a _single_ Pod. No other Pods in the cluster will be able to use the volume simultaneously.

2. #### Provisioning:
	 Step 1: Check your cluster’s storage classes
	 find out which storage classes are available in your cluster:

	 ```bash
	$ kubectl get storageclass
	NAME                 PROVISIONER                RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
	standard (default)   k8s.io/minikube-hostpath   Delete          Immediate           false                  7d2h
	```

	 Step 2: Create a Persistent Volume:
	 ```yaml
	apiVersion: v1
	kind: PersistentVolume
	metadata:
	  name: demo-pv
	spec:
	  accessModes:
	    - ReadWriteOnce
	  capacity:
	    storage: 1Gi
	  storageClassName: standard
	  hostPath:
	    path: /tmp/demo-pv
	```
	Applying the yaml:
	```bash
	$ kubectl get pv
	NAME      CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
	demo-pv   1Gi        RWO            Retain           Available           standard                113s
	```
	 Step 3: Create a Persistent Volume Claim
	 ```yaml
	apiVersion: v1
	kind: PersistentVolumeClaim
	metadata:
	  name: demo-pvc
	spec:
	  accessModes:
	    - ReadWriteOnce
	  resources:
	    requests:
	      storage: 1Gi
	  storageClassName: standard
	  volumeName: demo-pv
	```
	Step 4: Dynamically provisioning PVs:
	Static provisioning of PVs, as shown above, is cumbersome: you must create the PV, then the PVC while ensuring their properties match.  
The PV is created for you based on your PVC’s configuration.

	```yaml
	apiVersion: v1
	kind: PersistentVolumeClaim
	metadata:
	  name: pvc-dynamic
	spec:
	  accessModes:
	    - ReadWriteOnce
	  resources:
	    requests:
	      storage: 1Gi
	  storageClassName: standard
	```
	```bash
	$ kubectl get pv
	NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                 STORAGECLASS   REASON   AGE
	demo-pv                                    1Gi        RWO            Retain           Bound    default/demo-pvc      standard                33m
	pvc-fd022049-eabf-415c-937a-94927c84ef6f   1Gi        RWO            Delete           Bound    default/pvc-dynamic   standard                
	```
	Step 5: Attach PVCs to Pods
	```yaml
	apiVersion: v1
	kind: Pod
	metadata:
	  name: pvc-pod
	spec:
	  containers:
	    - name: pvc-pod-container
	      image: nginx:latest
	      volumeMounts:
	        - mountPath: /data
	          name: data
	  volumes:
	    - name: data
	      persistentVolumeClaim:
	        claimName: pvc-dynamic
	```
	**Spec.volumes.claimName** PV claimed by `pvc-dynamic` being mounted to `/data` inside the container.

	```bash
	# Get a shell inside the Pod
	$ kubectl exec -it pod/pvc-pod -- bash

	# No files in the volume yet
	root@pvc-pod:/# ls /data

	# Write a file to the volume
	root@pvc-pod:/# echo "bar" > /data/foo

	# The file now shows in the volume
	root@pvc-pod:/# ls /data
	foo
	```

	The files written to the volume are now stored independently of the Pod. You can delete the Pod and recreate it – your data will still be intact within the PV:
	```bash
	$ kubectl delete pod/pvc-pod
	pod "pvc-pod" deleted

	$ kubectl apply -f pod.yaml
	pod/pvc-pod created

	$ kubectl exec -it pod/pvc-pod -- bash

	root@pvc-pod:/# cat /data/foo
	bar
	```
	
---
###  CronJobs

```bash
# ┌───────────── minute (0 - 59)
# │ ┌───────────── hour (0 - 23)
# │ │ ┌───────────── day of the month (1 - 31)
# │ │ │ ┌───────────── month (1 - 12)
# │ │ │ │ ┌───────────── day of the week (0 - 6) (Sun to Sat;
# │ │ │ │ │                      7 is also Sunday on some systems)
# │ │ │ │ │                   OR sun, mon, tue, wed, thu, fri, sat
# │ │ │ │ │
# * * * * *
```
example this job will run everyday at 11 pm:
```bash
0 23 * * *
```
each job will create a pod and run it:
this job will run every minute
```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "* * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox:1.28
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
```

To view the pods that have been created to run the jobs:

```bash
kubectl get pods
```

![kubectl get pods](https://spacelift.io/_next/image?url=https%3A%2F%2Fspaceliftio.wpcomstaging.com%2Fwp-content%2Fuploads%2F2022%2F12%2Fkubectl-get-pods.webp&w=3840&q=75)

If you have lots of running pods you’ll want to filter the selection to the job name in your system using the `--selector`  argument.

![](https://spacelift.io/_next/image?url=https%3A%2F%2Fspaceliftio.wpcomstaging.com%2Fwp-content%2Fuploads%2F2022%2F12%2Fkubectl-get-pods-selectorjob-name.webp&w=3840&q=75)

---

### Liveness probes
Liveness probes are used to determine whether a container is still running and functioning correctly. They check if the container is **alive and responsive**

The `periodSeconds` field specifies that the kubelet should perform a liveness probe every 5 seconds. The `initialDelaySeconds` field tells the kubelet that it should wait 5 seconds before performing the first probe.

   ExecAction handler example:
   The below example shows a usage of the  `exec`  command to check if a file exists at the path _/usr/share/liveness/html/index.html_ by using the `cat` command. If no file exists, then the liveness probe will fail and the container will be restarted.
```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-exec
spec:
  containers:
  - name: liveness
    image: registry.k8s.io/liveness:0.1
    ports:
    - containerPort: 8080
    livenessProbe:
      exec:
        command:
        - cat
        - /usr/share/liveness/html/index.html
      initialDelaySeconds: 5
      periodSeconds: 5
```

HTTPGetAction handler example:

This example shows the HTTP handler, which will send an HTTP GET request on port 8080 to the _/health_ path. If a code between 200–400 is returned, the probe is considered successful.
The httpHeaders option is used to define any custom headers you want to send.

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-http
spec:
  containers:
  - name: liveness
    image: registry.k8s.io/liveness:0.1
    livenessProbe:
      httpGet:
        path: /health
	port: 8080
	httpHeaders:
	- name: Custom-Header
	  value: ItsAlive
      initialDelaySeconds: 5
      periodSeconds: 5
```

The [kubelet](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/) uses liveness probes to know when to restart a container.

### Readiness probes 
Readiness probes are used to determine whether a container is ready to **accept incoming traffic.** They check if the container is in a state where it can safely handle requests.
When the readiness probe fails for a container, the K8s control plane ensures that this container is not included in the load balancer’s pool of endpoints that receive incoming traffic.

The `initialDelaySeconds` specifies how long to wait after the container starts before the first check is executed, and the `periodSeconds` defines the interval between subsequent probe checks.
Unless you explicitly specify the `readinessProbe`in your YAML configuration, Kubernetes will not perform any readiness checks.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
spec:
  containers:
  - name: my-app-container
    image: my-app-image
    ports:
    - containerPort: 80
    readinessProbe:
      exec:
        command:
        - /bin/sh
        - -c
        - check-script.sh
      initialDelaySeconds: 20
      periodSeconds: 15
```
## Ingress and Ingress Controller

Ingress Resource: object with a set of routing rules.
Ingress controller: just another pod(usually run with Deployment) running in k8.

The ingress controller is responsible for reading the Ingress Resource information and processing that data accordingly.

![image](https://github.com/sagarsumit03/home/assets/8539646/188ca3f6-e9d7-435a-8056-f884efda982c)

> The Nginx ingress controller is nothing but a service of type `LoadBalancer`. What it does is be the public-facing endpoint for your services. The IP address assigned to this service can route traffic to multiple services. So you can go ahead and define your services as ClusterIP and have them exposed through the Nginx ingress controller.

As mentioned, you will need the ingress controller service regardless of how many ingress resources you have. So go ahead and apply this code on your EKS cluster.

Now let's see how you would expose your pod to the world through Nginx-ingress. Say you have a wordpress deployment. You can define a simple ClusterIP service for this app:

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: ${WORDPRESS_APP}
  namespace: ${NAMESPACE}
  name: ${WORDPRESS_APP}
spec:
  type: ClusterIP
  ports:
  - port: 9000
    targetPort: 9000
    name: ${WORDPRESS_APP}
  - port: 80
    targetPort: 80
    protocol: TCP
    name: http
  - port: 443
    targetPort: 443
    protocol: TCP
    name: https
  selector:
    app: ${WORDPRESS_APP
```
This creates a service for your wordpress app which is not accessible outside of the cluster. Now you can create an ingress resource to expose this service:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  namespace: ${NAMESPACE}
  name: ${INGRESS_NAME}
  annotations:
    kubernetes.io/ingress.class: nginx
    kubernetes.io/tls-acme: "true"
spec:
  tls:
  - hosts:
    - ${URL}
    secretName: ${TLS_SECRET}
  rules:
  - host: ${URL}
    http:
      paths:
      - path: /
	backend:
	  serviceName: ${WORDPRESS_APP}
	  servicePort: 80
```
Now if you run kubectl get svc you can see the following:

```bash
NAME                      TYPE          CLUSTER-IP      EXTERNAL-IP    PORT(S)                   AGE
wordpress                 ClusterIP     10.23.XXX.XX   <none>         9000/TCP,80/TCP,443/TCP   1m
nginx-ingress-controller  LoadBalancer  10.23.XXX.XX    XX.XX.XXX.XXX  80:X/TCP,443:X/TCP   1m
```
Now you can access your wordpress service through the URL defined, which maps to the public IP of your ingress controller LB service.


#### Generally speaking:

**LoadBalancer** type service is a L4(TCP) load balancer. You would use it to expose single app or service to outside world. It would balance the load based on destination IP address and port.

**Ingress** type resource would create a L7(HTTP/S) load balancer service. You would use this to expose several services at the same time, as L7 LB is application aware, so it can determine where to send traffic depending on the application state.


> my personal example:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: spring-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: spring-app
  template:
    metadata:
      labels:
	app: spring-app
    spec:
      containers:
      - name: spring-app
	image: sagarsumit03/app
	ports:
	- containerPort: 8080
---

apiVersion: v1
kind: Service
metadata:
  name: spring-app
spec:
  type: ClientIP
  selector:
    app: spring-app
  ports:
    - port: 8080
      targetPort: 8080
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: spring-app
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
    - host: localhost
    - http:
	paths:
	- path: /api/s3
	  pathType: Prefix
	  backend:
	    service:
	      name: 'spring-app'
	      port:
		number: 8080
```
# https://stackoverflow.com/questions/51511547/empty-address-kubernetes-ingress
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
