

---

# 🧩 **Kubernetes Services Lab — ClusterIP, NodePort, LoadBalancer (Minikube Edition)**

---

---

## Step 1: Create Backend (ClusterIP)

### 📄 backend.yaml

kubectl apply -f backend-deployment.yaml
kubectl apply -f backend-clusterip-service.yaml
kubectl get pods,svc
---

### 🔍 ClusterIP Explanation

* Internal-only service, not accessible from outside the cluster.
* Used by other Pods (e.g., frontend) to reach backend services.

---

### 🧪 Test ClusterIP

Run a temporary pod:

```bash
kubectl run testpod --image=busybox --restart=Never -it

Inside the pod shell:

```bash
wget -qO- http://backend-clusterip:8080
```

Expected output:

```
<h2>This is Backend</h2>
```

Exit:

```bash
exit
```

---

## 🧱 Step 3: Create Frontend (NodePort)

### 📄 frontend.yaml

kubectl apply -f frontend-deployment.yaml
kubectl apply -f frontend-nodeport-services.yaml
kubectl get pods,svc

---

### 🔍 NodePort Explanation

* Opens an external port (30000–32767 range) on the Minikube VM.
* Allows access from outside the cluster using the node’s IP or loopback.
* Ideal for testing external access locally.

---

### 🧪 Access NodePort

```bash
minikube service frontend-nodeport-services --url
```

Example output:

```
http://127.0.0.1:31080
```

✅ Open this URL in your browser —
you’ll see *“This is Frontend”* and an embedded backend response.

---

## 🧱 Step 4: Create LoadBalancer

### 📄 frontend-lb.yaml


kubectl apply -f frontend-loadbalancer.yaml
kubectl get svc


You’ll see:

```
NAME             TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)        AGE
frontend-lb      LoadBalancer   10.96.250.99    <pending>       8080:31234/TCP 1m
```

---

### 🔍 LoadBalancer Explanation

* In cloud (EKS, AKS, GKE), this creates an external load balancer.
* In Minikube, you must simulate it using a **tunnel**.

---

### 🧪 Access LoadBalancer

Start tunnel (in another terminal):

```bash
minikube tunnel
```

Check service again:

```bash
kubectl get svc frontend-loadbalancer
```

Now EXTERNAL-IP will show:

```
127.0.0.1
```

Open via:

```bash
minikube service frontend-loadbalancer --url
```

Output:

```
http://127.0.0.1:51830
```

✅ Open in browser — you’ll see the same frontend connected to backend.

(Optional) add hostname:

```bash
sudo nano /etc/hosts
127.0.0.1   thisismyservices.com
```

Then access:

```
http://thisismyservices.com:51830
```

---

## 🧱 Step 5: Verification & Housekeeping Commands

### 🧩 Check deployments

```bash
kubectl get deployments
kubectl describe deployment frontend
kubectl describe deployment backend
```

### 🧩 Check pods

```bash
kubectl get pods -o wide
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

### 🧩 Scale deployments

```bash
kubectl scale deployment frontend --replicas=3
kubectl get pods -l app=frontend
kubectl scale deployment frontend --replicas=1
```

### 🧩 Check services

```bash
kubectl get svc
kubectl describe svc frontend-nodeport
```

### 🧩 Check endpoints (Pod-Service mapping)

```bash
kubectl get endpoints
```

### 🧩 Check events

```bash
kubectl get events --sort-by=.metadata.creationTimestamp
```

### 🧩 Check everything at once

```bash
kubectl get all
```

### 🧩 Test internal connectivity again

```bash
kubectl run testpod --image=busybox --restart=Never -it
wget -qO- http://backend-clusterip:8080
exit
```

### 🧩 Access externally again

```bash
minikube service frontend-nodeport --url
minikube service frontend-lb --url
```

---

## 🧱 Step 6: Cleanup

```bash
kubectl delete -f frontend-lb.yaml
kubectl delete -f frontend.yaml
kubectl delete -f backend.yaml
```

Confirm cleanup:

```bash
kubectl get all
```

---

## 🧠 **Final Summary**

| Type             | Name              | Port Mapping      | Access Method           | Testing Command                                        |
| ---------------- | ----------------- | ----------------- | ----------------------- | ------------------------------------------------------ |
| **ClusterIP**    | backend-clusterip | 8080 → 80         | Internal only           | `wget -qO- http://backend-clusterip:8080` (inside pod) |
| **NodePort**     | frontend-nodeport | 31080 → 8080 → 80 | External (via loopback) | `minikube service frontend-nodeport --url`             |
| **LoadBalancer** | frontend-lb       | 51830 → 8080 → 80 | External (via tunnel)   | `minikube service frontend-lb --url`                   |

---

✅ **What You’ve Learned**

* How traffic flows from **NodePort → Service → Pod**
* How LoadBalancer builds on NodePort to expose services
* How ClusterIP provides internal-only connectivity
* How to verify, troubleshoot, and scale deployments
* How to test connectivity both internally (Pods) and externally (Browser)

---

Would you like me to create a **diagram** (browser → NodePort → ClusterIP → Pod flow) and a **PDF handout** version of this full lab so you can print or distribute it to students?

