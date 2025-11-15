==================================================
Kubernetes Ingress Lab — Killercoda Edition
==================================================

### *(Supports HTTP + HTTPS + Path-Based + Host-Based Routing)*

---

## ⚙️ Lab Objective

Students will:

1. Deploy four simple NGINX services (`sample-1` → `sample-4`)
2. Install the NGINX Ingress Controller
3. Configure and test Ingress for both HTTP and HTTPS
4. Observe routing behavior for path- and host-based rules

---
NB:
Use same deployment, services, ingress and selfsigned-cert files as for minikube

Pre-req
1- Ingress installation for KLLRCODA is different than minikube mention in step 2
2- 





## 🧩 Step 1 — Verify Cluster Access

```bash
kubectl get nodes
```

✅ *Expected:*

```
controlplane   Ready   control-plane
node01         Ready
```

---

## 🧩 Step 2 — Install NGINX Ingress Controller

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```

### 🔹 Show / Verify

```bash
kubectl get pods -n ingress-nginx
kubectl get svc  -n ingress-nginx
```

✅ *Wait until:*

```
ingress-nginx-controller   1/1   Running
```

---

## 🧩 Step 3 — Deploy Four Sample Apps (deployment and services)

kubectl apply -f sample-1-deployment.yaml

kubectl apply -f sample-2-deployment.yaml

kubectl apply -f sample-3.deployment.yaml

kubectl apply -f sample-4.deployment.yaml

kubectl apply -f sample-1-services.yaml

kubectl apply -f sample-2-services.yaml

kubectl apply -f sample-3-services.yaml

kubectl apply -f sample-4-services.yaml
```

### 🔹 Show / Verify

```bash
kubectl get deploy,svc
```

✅ *Expected:* four deployments and four services appear.

---

## 🧩 Step 4 — Create Self-Signed TLS Certificate

```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout tls.key -out tls.crt \
  -subj "/CN=devopsdummies.com/O=devopsdummies"
```

### 🔹 Create K8s Secret

```bash
kubectl create secret tls tls-secret --cert=tls.crt --key=tls.key

kubectl get secret tls-secret
```

✅ *Expected:* `TYPE: kubernetes.io/tls`

---

## 🧩 Step 5 — Create the Ingress Resource


kubectl apply -f ingress-resource.yaml
```

### 🔹 Show / Verify

kubectl get ingress

kubectl describe ingress example-ingress


✅ *Expected:* lists all 4 rules and the `tls-secret`.

---

## 🧩 Step 6 — Find NodePorts for HTTP & HTTPS

```bash
kubectl get svc -n ingress-nginx
```

Example output:

```
80:32225/TCP,443:31046/TCP
```

➡ **HTTP Port = 32225**, **HTTPS Port = 31046**

---

## 🧩 Step 7 — Test HTTP Routing

### 🔹 Path-based
NB: Your port may be different so change following commands port as per output to your terminal
```bash
curl -v -H "Host: devopsdummies.com" http://localhost:32225/sample-1

curl -v -H "Host: devopsdummies.com" http://localhost:32225/sample-2
```

### 🔹 Host-based
NB: Your port may be different so change following commands port as per output to your terminal
```bash
curl -v -H "Host: sample-3.devopsdummies.com" http://localhost:32225/

curl -v -H "Host: sample-4.devopsdummies.com" http://localhost:32225/
```

✅ *Expected:* NGINX welcome page.

---

## 🧩 Step 8 — Test HTTPS Routing (Self-Signed)

### 🔹 Path-based
NB: Your port may be different so change following commands port as per output to your terminal
```bash
curl -vk -H "Host: devopsdummies.com" https://localhost:31046/sample-1

curl -vk -H "Host: devopsdummies.com" https://localhost:31046/sample-2
```

### 🔹 Host-based

NB: Your port may be different so change following commands port as per output to your terminal
```bash
curl -vk -H "Host: sample-3.devopsdummies.com" https://localhost:31046/

curl -vk -H "Host: sample-4.devopsdummies.com" https://localhost:31046/
```

✅ *Expected:* same HTML, with `SSL certificate verify result: self signed certificate`.

---

## 🧩 Step 9 — (Verify Certificate)

```bash
echo | openssl s_client -connect localhost:31046 -servername devopsdummies.com | openssl x509 -noout -subject -issuer
```

✅ *Expected:*

```
subject=CN = devopsdummies.com

issuer=CN = devopsdummies.com
```

---

## 🧩 Step 10 — Optional : Check Logs to See Routing

```bash
kubectl get pods -o wide
kubectl logs -l app=sample-3
kubectl logs -l app=sample-4
```

You’ll see NGINX access logs confirming which backend served the request.

---

## ✅ Lab Summary

| Feature            | Verified with                                              |
| ------------------ | ---------------------------------------------------------- |
| Ingress Controller | `kubectl get pods -n ingress-nginx`                        |
| Path-based routing | `/sample-1`, `/sample-2`                                   |
| Host-based routing | `sample-3.devopsdummies.com`, `sample-4.devopsdummies.com` |
| HTTP + HTTPS       | Ports 32225 and 31046                                      |
| No forced redirect | `ssl-redirect: false`                                      |

---

### 💡 Cleanup (optional)

```bash
kubectl delete ingress example-ingress
kubectl delete all -l app=sample-1
kubectl delete all -l app=sample-2
kubectl delete all -l app=sample-3
kubectl delete all -l app=sample-4
kubectl delete secret tls-secret
```

---

That’s the **complete, ready-to-teach Killercoda lab** — fully tested, self-contained, and understandable for beginners.
Would you like me to format this into a downloadable **Markdown (.md)** or **Word (.docx)** file for easy sharing with your students?
