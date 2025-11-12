This folder is for ingress only. It has further folders of deployments and services. 

The requirement of customer is as follows
=========================================

Customer needs to deploy two microservices sample-1 and sample-2 with 3 replicas.
Both applications needs to be exposed to internet and to be accessed by using path based routing.
devopsdummies.com/sample-1
devopsdummies.com/sample-2
Customer also requires two more microservices sample-3 and sample-4 with three replicas. 
These microservices to be accesssed by usinbg subdomain as follows
sample-3.devopsdummies.com, sample-4.devopsdummies.com



# Kubernetes Ingress MiniKube 

Since we are using MiniKube here, we do not have any cloud env or public access at present. Two steps to be taken to make it work

1- Port forward Tunnel
2- Updating host file manually with FQDN

1- Port forward Tunnel (run following command)
================================================
minikube service ingress-nginx-controller -n ingress-nginx --url

You got:

http://127.0.0.1:51296  > This would differ in your case and port may change 
http://127.0.0.1:51297 > This would differ in your case and port may change

So two ports were opened on your Mac:

51296 → for HTTP (port 80)

51297 → for HTTPS (port 443)

🧠 Why this happens

Inside the cluster, your NGINX ingress service exposes:

kubectl get svc -n ingress-nginx

shows something like:

PORT(S): 80:30549/TCP, 443:32752/TCP


This means:

Port 80 is mapped internally for HTTP

Port 443 is mapped internally for HTTPS

But since you’re using Minikube with the Docker driver, those ports (30549, 32752) aren’t directly reachable from your host.
When you run minikube service ... --url, Minikube starts a temporary port-forward tunnel to your host and maps them to random available ports on your Mac (in your case, 51296 and 51297).

So effectively:

Inside Cluster	Minikube Tunnel on Mac
Port 80 (HTTP)	127.0.0.1:51296
Port 443 (HTTPS)	127.0.0.1:51297

When to use each
Purpose	Use this
Access your HTTP-based app (no TLS)	http://127.0.0.1:51296 (or http://devopsdummies.com:51296)
Access your HTTPS-based app (with TLS enabled later)	https://127.0.0.1:51297 (or https://devopsdummies.com:51297)

2- Updating /etc/hosts file with FQDN for DNS
=============================================

cat /etc/hosts                        

127.0.0.1    devopsdummies.com sample-1.devopsdummies.com sample-2.devopsdummies.com

============================
URLS as part of INGRESS LAB:
Non-Secure
==========
http://devopsdummies.com:51296/sample-1 > sample-1 dep/svc

http://devopsdummies.com:51296/sample-2 > sample-2 dep/svc

http://sample-3.devopsdummies.com:51296 > sample-3 dep/svc

http://sample-4.devopsdummies.com:51296 > sample-4 dep/svc

Secure
======
https://devopsdummies.com:51297/sample-1 > sample-1 dep/svc

https://devopsdummies.com:51297/sample-2 > sample-2 dep/svc

https://sample-3.devopsdummies.com:51297 > sample-3 dep/svc

https://sample-4.devopsdummies.com:51297 > sample-4 dep/svc


Enable NGINX Ingress Controller in Minikube
-------------------------------------------

Minikube already includes NGINX ingress as an addon — just enable it:

minikube addons enable ingress

kubectl get pods -n ingress-nginx

ingress-nginx-controller-xxxx   Running

Installing Cert Manager
----------------------

STEP 2 — Install cert-manager (official way)
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.15.0/cert-manager.yaml


Wait until all pods are ready:

kubectl get pods -n cert-manager


✅ Expect:

cert-manager
cert-manager-cainjector
cert-manager-webhook

all in Running state.

kubectl apply -f Selfsigned-cert.yaml

kubectl get certificate
kubectl describe certificate devopsdummies-cert
kubectl get secret devopsdummies-tls


