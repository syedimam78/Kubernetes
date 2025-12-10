# Lab 01 Stateful Set, Run this once at the beginning of the session

```
kubectl get nodes
kubectl create namespace statefulset-lab
kubectl config set-context --current --namespace=statefulset-lab
kubectl get ns
```
# Paste the ss-lab1-basic.yaml content to create yaml

```
vi ss-lab1-basic.yaml (i > paste content > esc > :wq)
kubectl apply -f ss-lab1-basic.yaml
```
# Observe Pod Names and Ordering
```
kubectl get pods -w
```
Pods will appear as: web-0, web-1, web-2

They come up in order: first web-0, then web-1, then web-2.
Press Ctrl + C when done.

# Delete a Pod and Watch It Recreate
Delete web-1:
```
kubectl delete pod web-1
```
# Clean Up (Optional now, or after all labs)
```
kubectl delete -f ss-lab1-basic.yaml
kubectl get pods -w
```

# LAB 2 – Adding a Headless Service (Stable DNS per Pod)
We have two yaml file here. First one for statefullset and second one for headless service.
Create both yaml file in the repo by using vi editor

```
kubectl apply -f lab2-ss-with-headless.yaml
kubectl get pods -w
kubectl apply -f lab2-headless-svc.yaml
kubectl get svc
```
CLUSTER-IP shows <none> → headless.
This allows Kubernetes to give each pod its own DNS record.

Use a Temporary Pod to Test DNS
Run BusyBox:
```
kubectl run tmp-shell --rm -it --image=busybox:1.36 -- /bin/sh
Inside the shell:
```

1) Show service DNS
```
nslookup web-svc.statefulset-lab.svc.cluster.local
```
# 2) Show pod-level DNS
nslookup web-0.web-svc.statefulset-lab.svc.cluster.local
nslookup web-1.web-svc.statefulset-lab.svc.cluster.local
nslookup web-2.web-svc.statefulset-lab.svc.cluster.local

# 3) Curl each pod
```
wget -qO- web-0.web-svc
```
<h1>Hello from web-0</h1>

```
wget -qO- web-1.web-svc
```
<h1>Hello from web-1</h1>
```
wget -qO- web-2.web-svc
```
<h1>Hello from web-2</h1>
```
wget -qO- web-0.web-svc.statefulset-lab.svc.cluster.local
```
<h1>Hello from web-0</h1>
```
wget -qO- web-1.web-svc.statefulset-lab.svc.cluster.local
```
<h1>Hello from web-1</h1>
```
wget -qO- web-2.web-svc.statefulset-lab.svc.cluster.local
```
<h1>Hello from web-2</h1>
