Run this once at the beginning of the session:

kubectl get nodes
kubectl create namespace statefulset-lab
kubectl config set-context --current --namespace=statefulset-lab
kubectl get ns

vi ss-lab1-basic.yaml

kubectl apply -f ss-lab1-basic.yaml
Observe Pod Names and Ordering
kubectl get pods -w
Pods will appear as: web-0, web-1, web-2

They come up in order: first web-0, then web-1, then web-2.

Press Ctrl + C when done.

Delete a Pod and Watch It Recreate

Delete web-1:

kubectl delete pod web-1

Clean Up (Optional now, or after all labs)
kubectl delete -f ss-lab1-basic.yaml
kubectl get pods -w
