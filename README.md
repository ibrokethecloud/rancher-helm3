## Installing Rancher using Helm3

Installation of Rancher using Helm3 is very similar to using Helm2.

There are some differences though:

1. Helm3 drops tiller, so no tiller initialisation is required.
2. Helm3 maintains chart state in secrets in the namespace the chart is being deployed to.
   This means that the namespace should exist before chart is installed
3. A minor tweak to the actual helm install command as `--name` is not a valid flag


### Steps
#### Install Cert Manager
- Install the CRD's
```
kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.9/deploy/manifests/00-crds.yaml
```

- Create the Namespace
```
kubectl create namespace cert-manager
```

- Disable validation on Namespace
```
kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true
```

- Add cert manager repo
```
helm repo add jetstack https://charts.jetstack.io
```

- Update the repo
```
helm repo update
```

- Install the cert-manager helm chart
```
helm install cert-manager --namespace cert-manager --version v0.9.1 jetstack/cert-manager
```

#### Install Rancher

- Add the rancher chart repo
```
helm repo add rancher-stable https://releases.rancher.com/server-charts/stable
```

- Create namespace to install Rancher to
```
kubectl create namespace cattle-system
```

- Install the rancher helm chart
```
helm install rancher rancher-stable/rancher --namespace cattle-system --set hostname=rancher.demo
```


Since there is no tiller in Helm 3 the state of deployments is managed as secrets in each namespace.

```
$ helm ls --all-namespaces
NAME                   NAMESPACE      REVISION             UPDATED                                         STATUS                 CHART                 APP VERSION
cert-manager     cert-manager     1             2019-11-18 11:47:15.84416 +1100 AEDT               deployed             cert-manager-v0.9.1 v0.9.1
rancher                cattle-system     1             2019-11-18 11:49:09.336562 +1100 AEDT             deployed             rancher-2.3.2                      v2.3.2
```
