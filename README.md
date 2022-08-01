# helmrelease-addon
PoC HelmRelease Addon for Open Cluster Management (OCM).

By applying this add-on to your OCM environment, a HelmRelease controller will be installed to all your `managed` (`spoke`) clusters.
You can then apply a `ManifestWork` wrapping the `HelmRelease` CR on your `hub` cluster and the `HelmRelease` CR will be reconciled on your `managed` (`spoke`) clusters.

# Prerequisite

Setup an Open Cluster Management environment. See: https://open-cluster-management.io/getting-started/quick-start/ for more details

# Get started

Git Clone the repo.

Edit the `Makefile` and replace the `docker.io/mikeshng` to the registry of your choice.

Create the image:

```
make docker-build
```

Push the image:

```
make docker-push
```

Edit the `deploy/deployment.yaml` file and replace the `docker.io/mikeshng` to the registry of your choice.

Deploy the add-on the OCM `hub` cluster:

```
kubectl apply -f deploy
```

This will automatically install the add-on to all `managed` (`spoke`) clusters.
Validate the add-on is installed on a `managed` (`spoke`) cluster:

```
$ kubectl -n flux get deploy
NAME            READY   UP-TO-DATE   AVAILABLE   AGE
helm-operator   1/1     1            1           32s
```

You can also validate the add-on is available on the `hub` cluster:

```
$ kubectl -n cluster1 get managedclusteraddon # replace "cluster1" with your managed cluster name
NAME          AVAILABLE   DEGRADED   PROGRESSING
helmrelease   True                   
```

Deploy an example of a `ManifestWork` wrapping a `HelmRelease` CR on the `hub` cluster:

```
kubectl -n cluster1 apply -f example # replace "cluster1" with your managed cluster name
```

Validate the `HelmRelease` is created on the `managed` (`spoke`) cluster:

```
$ kubectl -n default get helmrelease
NAME            RELEASE                 PHASE       RELEASESTATUS   MESSAGE                                                                         AGE
nginx-ingress   default-nginx-ingress   Succeeded   deployed        Release was successful for Helm release 'default-nginx-ingress' in 'default'.   58s
```
