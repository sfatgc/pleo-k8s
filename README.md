# pleo-k8s

Kubernetes manifests to run [sfatgc/tinjis](https://github.com/sfatgc/tinjis) with [sfatgc/sinatra-money-adapter](https://github.com/sfatgc/sinatra-money-adapter) as an external payment provider on a K8s clluster.

Tested only on minikube & docker-desktop for Mac installations.

To apply on k8s cluster, please run: `kubectl apply -k environments/minikube`

To ckeck:
```
kubectl port-forward -n pleo-stack service/app-tinjis-svc 8000
curl -v -X POST -d "{}" http://localhost:8000/rest/v1/invoices/pay
curl http://localhost:8000/rest/v1/invoices |jq
```

Manifests are rendered with [Kustommize](https://kustomize.io/) tool. To just get the manifests rendered please run one of: `kustomize build environments/minikube` or `kubectl kustomize environments/minikube`.

Directory structure:
```
.
├── README.md
├── environments ....................contains environment-specific resources kustomizations for both applications
│   ├── minikube
│   │   ├── kustomization.yaml
│   │   └── namespace.yaml
│   └── production
│       ├── kustomization.yaml
│       └── namespace.yaml
└── resources .......................contains environment-agnostic resources manifests for both applications
    └── base
        ├── sinatra-money-adapter
        │   ├── deployment.yaml
        │   ├── env
        │   ├── kustomization.yaml
        │   ├── rbac.yaml
        │   └── service.yaml
        └── tinjis
            ├── deployment.yaml
            ├── env
            ├── kustomization.yaml
            ├── rbac.yaml
            └── service.yaml
```
