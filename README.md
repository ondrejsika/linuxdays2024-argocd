# linuxdays2024-argocd

## Shared Document

https://docs.google.com/document/d/1pUWw83IWFOmd8sdA4e1ArzHC708nWWGLpVV7lPdzRxY/edit

## Connect to the lab

https://lab0.sikademo.com/?folder=/root/workspace

## SSH to k3s server

```
ssh root@k3s0.sikademo.com
```

## Connect to ArgoCD

- https://argocd.k3s0.sikademo.com
- username: `admin`
- get password using `slu argocd initial-password`

## Connect Gitlab

- https://gitlab.sikademo.com
- username: `demo-user`
- password: `asdfasdf`

## Create own repository

- public
- without README

https://gitlab.sikademo.com/projects/new?namespace_id=7

## Clone repository

```
git clone https://demo-user:asdfasdf@gitlab.sikademo.com/linuxdays/lab0.git
```

## Create app-of-apps

`./app_of_apps.yml`

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: app-of-apps
  namespace: argocd
  finalizers:
    - resources-finalizer.argocd.argoproj.io
spec:
  project: default
  destination:
    namespace: argocd
    server: https://kubernetes.default.svc
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
  source:
    repoURL: https://gitlab.sikademo.com/linuxdays/lab0.git
    path: apps
    directory:
      recurse: true
```

and `./apps/.gitkeep`


## Apply app-of-apps

```
ssh root@k3s0.sikademo.com
```

```
apt install -y git
```

```
git clone https://demo-user:asdfasdf@gitlab.sikademo.com/linuxdays/lab0.git
```

```
cd lab0
```

```
kubectl apply -f app_of_apps.yml
```

See: https://argocd.k3s0.sikademo.com

## Deploy example from Git

Create `./apps/hello_linuxdays/hello_linuxdays.yml`

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: hello-linuxdays
  namespace: argocd
  finalizers:
    - resources-finalizer.argocd.argoproj.io
spec:
  project: default
  destination:
    namespace: hello-linuxdays
    server: https://kubernetes.default.svc
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
  source:
    repoURL: https://github.com/sikalabs/charts.git
    path: charts/hello-world
    helm:
      valuesObject:
        host: hello-linuxdays.k3s0.sikademo.com
        image: sikalabs/hello-world-server:linuxdays
        replicas: 3
```

See: https://argocd.k3s0.sikademo.com

## Deploy example from Helm

Create `./apps/hello_world/hello_world.yml`

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: hello-world
  namespace: argocd
  finalizers:
    - resources-finalizer.argocd.argoproj.io
spec:
  project: default
  destination:
    namespace: hello-world
    server: https://kubernetes.default.svc
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
  source:
    repoURL: https://helm.sikalabs.io
    chart: hello-world
    targetRevision: 0.10.0
    helm:
      valuesObject:
        host: hello-world.k3s0.sikademo.com
        replicas: 3
```

See: https://argocd.k3s0.sikademo.com

## More Resources

- https://ondrej-sika.cz/skoleni/argocd/
- https://github.com/ondrejsika/argocd-training
- https://github.com/ondrejsika/argocd-apps-public

## Thank you! & Questions?

### Ondrej Sika

- email: <ondrej@sika.io>
- web: <https://sika.io>
- twitter: [@ondrejsika](https://twitter.com/ondrejsika)
- linkedin: [/in/ondrejsika/](https://linkedin.com/in/ondrejsika/)

Wanna to go for a beer or do some work together? Just [book me](https://book-me.sika.io) :)
