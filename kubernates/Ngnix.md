
# 🚀 Kubernetes NGINX Ingress Example

This guide shows how to deploy a simple REST app (`vad1mo/hello-world-rest`) using Kubernetes and expose it via **NGINX Ingress**.


Service: `LoadBalancer` or `NodePort` is used by an Ingress Controller.
<img width="1400" height="905" alt="image" src="https://github.com/user-attachments/assets/0de94517-98f8-4f0e-ad17-22879d3f62c3" />

NodePort: on top of having a cluster-internal IP, expose the service on a port on each node of the cluster 
By creating a NodePort service, you are saying to Kubernetes reserve a port on all its nodes and forwards incoming connections to the pods that are part of the service. it doesnt do load balancing


LoadBalancer: Leverages an external load balancer provided by the cloud provider:



---

## 📦 What You'll Deploy

- A Deployment running the app
- A ClusterIP Service to expose the pod internally
- An Ingress to expose the service externally via hostname `myapp.local`
- Using NGINX Ingress Controller (e.g. in Minikube, OrbStack, etc.)

---

## 📝 Step-by-Step

### Deploy Ngnix Controller:
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.10.1/deploy/static/provider/cloud/deploy.yaml
```

### 1️⃣ Create `deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp-container
        image: vad1mo/hello-world-rest
        ports:
        - containerPort: 5050
```

---

### 2️⃣ Create `service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 5050
  type: ClusterIP
```

---

### 3️⃣ Create `ingress.yaml`

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-myapp
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: myapp.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: myapp-service
            port:
              number: 80
```

---
By Default the Ngnix Load Balancing is round robin but you can use `least_conn` or `ip_hash` for stiky sessions.
you need to add a Annotation:
```
metadata:
  name: ingress-myservicea
  annotations:
    nginx.ingress.kubernetes.io/load-balance: "least_conn"
```

### If you want to route based on the resouce `/api/` or `/web` you can do that as well:

in the ingress path you can use below:
```yaml
http:
  paths:
    - path: /api(/|$)(.*)
      pathType: Prefix
      backend:
        service:
          name: service-a
          port:
            number: 80
    - path: /web(/|$)(.*)
      pathType: Prefix
      backend:
        service:
          name: service-b
          port:
            number: 80
```
Here the paths are divided based on the path prefix /api goes to service A and /web goes to service B
---

## ⚙️ Apply Resources

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f ingress.yaml
```

---

## 🌐 Configure Local DNS

look for the ngnix controller External-IP and map it to the `/etc/hosts`

```

~/Documents/Kubernates/ingress
❯ kubectl get svc -n ingress-nginx
NAME                                 TYPE           CLUSTER-IP        EXTERNAL-IP    PORT(S)                      AGE
ingress-nginx-controller             LoadBalancer   192.168.194.229   198.19.249.2   80:30300/TCP,443:32766/TCP   44s
ingress-nginx-controller-admission   ClusterIP      192.168.194.222   <none>         443/TCP                      44s
```

Edit your `/etc/hosts` file:

```bash
sudo nano /etc/hosts
```

Add this line:

```
198.19.249.2 myapp.local
```
if you have multiple services then map it something like this:
Then add this to /etc/hosts:
```
192.168.49.2 service-a.local service-b.local
```
---

## ✅ Test It

```bash
curl http://myapp.local
```

Expected output:

```json
{"status":"OK","message":"Hello World"}
```

---

## 🔎 Troubleshooting

- ❌ `502 Bad Gateway` → Check if pods are running and service targets the correct port.
- ❌ `curl: Could not resolve host` → Ensure your `/etc/hosts` is correctly set.
- ❌ Nothing happens? → Check Ingress controller logs:
  ```bash
  kubectl logs -n ingress-nginx -l app.kubernetes.io/component=controller
  ```

---

Happy K8s-ing! 🐳
