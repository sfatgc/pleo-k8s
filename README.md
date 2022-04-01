# pleo-k8s

To apply on minikube or docker-desktop k8s cluster: `kubectl apply -k environments/minikube`

To ckeck:
```
kubectl port-forward -n pleo-stack service/app-tinjis-svc 8000
curl -v -X POST -d "{}" http://localhost:8000/rest/v1/invoices/pay
curl http://localhost:8000/rest/v1/invoices |jq