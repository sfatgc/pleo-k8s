# pleo-k8s

Kubernetes manifests to run [sfatgc/tinjis](https://github.com/sfatgc/tinjis) with [sfatgc/sinatra-money-adapter](https://github.com/sfatgc/sinatra-money-adapter) as an external payment provider on a K8s clluster.

Tested only on minikube & docker-desktop for Mac installations.

To apply on k8s cluster, please run: `kubectl apply -k environments/minikube`

To ckeck on minikube/docker-desktop:
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


## Applications

### [sfatgc/tinjis](https://github.com/sfatgc/tinjis)

Branch [feature/ci](https://github.com/sfatgc/tinjis/tree/feature/ci) contains simple CircleCI pipeline to build and publish docker image into the [hub.docker.com](hub.docker.com) registry.

Dockerfile contains small changes to avoid application to run as root user.

### [sfatgc/sinatra-money-adapter](https://github.com/sfatgc/sinatra-money-adapter)

Probably simpliest possible implementation of the REST-like API to satisfy [sfatgc/tinjis](https://github.com/sfatgc/tinjis) application needs.

Implemented in [Ruby](https://www.ruby-lang.org/en/) language, using [Sinatra](http://sinatrarb.com/) framework.

Handles `GET /health` requests to implement health check entrypoint for Kubernetes.

Handles `POST /` requests to implement payment provider entrypoint for [sfatgc/tinjis](https://github.com/sfatgc/tinjis) application. Randomly returns JSON response with true or false value of the `result` field.

Randomness can be configured via `SINATRA_MONEY_COEFFICIENT` environment variable configurable per deployment environment in the proper [kustomization file](environments/minikube/kustomization.yaml#L40) (See project's [README.md](https://github.com/sfatgc/sinatra-money-adapter/blob/main/README.md) for details)

