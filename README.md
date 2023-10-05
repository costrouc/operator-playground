# Steps to Recreate

[Following tutorial](https://sdk.operatorframework.io/docs/building-operators/golang/tutorial/)

```shell
# start a kubernetes cluster
kind create cluster
```

Change into directory for memcached-operator

```shell
mkdir memcached-operator
cd memcached-operator
```

Initialize and create new project

```shell
# create initial structure
$ operator-sdk init --domain ostrolabs.com --repo github.com/costrouc/memcached-operator
# create crds
$ operator-sdk create api --group cache --version v1alpha1 --kind Memcached --resource --controller
# edit api/v1alpha1/memcached_types.go
# update the generated code for resource type
$ make generate 
# generate crd manifests
$ make manifests
# edit controllers/memcached_controller.go (replace all example.com with your domain)
# edit main.go
# regenerate crd manifests
$ make manifests
# edit the docker image name in Makefile use quay.io
# build docker image and push
$ make docker-build docker-push
```

Deploy the operator

```shell
make deploy
```

Check that the operator successfully deployed

```shell
kubectl get pods -A
```

Now deploy a custom memcached resource

```shell
# edit config/manager/manager.yaml
#         env:
#          - name: "MEMCACHED_IMAGE"
#            value: "memcached:1.4.36-alpine"
# edit config/samples/cache_v1alpha1_memcached.yaml
kubectl apply -f config/samples/cache_v1alpha1_memcached.yaml
# you will see three memcached clusters start
kubectl delete kubectl delete memcached.cache.ostrolabs.com memcached-sample
```

Now redeployment should be reasonably simple on changes

```shell
# edit Makefile increment version/tag
$ make manifests
$ make docker-build docker-push
$ make deploy
$ kubectl apply -f config/samples/cache_v1alpha1_memcached.yaml
# you will see three memcached clusters start
$ kubectl delete kubectl delete memcached.cache.ostrolabs.com memcached-sample
```
