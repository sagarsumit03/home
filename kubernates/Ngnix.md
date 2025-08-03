
# 🚀 Kubernetes NGINX Ingress Example

This guide shows how to deploy a simple REST app (`vad1mo/hello-world-rest`) using Kubernetes and expose it via **NGINX Ingress**.

---

## 📦 What You'll Deploy

- A Deployment running the app
- A ClusterIP Service to expose the pod internally
- An Ingress to expose the service externally via hostname `myapp.local`
- Using NGINX Ingress Controller (e.g. in Minikube, OrbStack, etc.)

---

## 📝 Step-by-Step

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
