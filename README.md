# Kubernetes

minikube service ingress-nginx-controller -n ingress-nginx --url


You got:

http://127.0.0.1:51296
http://127.0.0.1:51297

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


⚙️ Optional — fix the ports to always be same (advanced tip)

If you want to keep the same host ports every time instead of random ones (so you don’t have to remember new ports):

Run:

kubectl port-forward -n ingress-nginx svc/ingress-nginx-controller 8080:80 8443:443


Then open in your browser:

http://127.0.0.1:8080/sample-1


Now the mapping is static:

Inside Cluster	Host Port
80	8080
443	8443
Summary
Port	Protocol	Description
51296	HTTP	Mapped from internal port 80 (your main app URL)
51297	HTTPS	Mapped from internal port 443 (for SSL, not used yet)

