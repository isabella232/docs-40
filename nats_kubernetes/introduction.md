- [Introduction](#org5eb48bf)
- [Getting started](#org3081466)
- [Creating your first NATS cluster](#org736f68f)
- [Connecting to your cluster](#orgc2d80fb)



<a id="org5eb48bf"></a>

# Introduction

The [NATS Operator](https://github.com/nats-io/nats-operator) is a


<a id="org3081466"></a>

# Getting started

In order to install the latest version of the operator:

```sh
$ kubectl apply -f https://github.com/nats-io/nats-operator/releases/latest/download/00-prereqs.yaml
$ kubectl apply -f https://github.com/nats-io/nats-operator/releases/latest/download/10-deployment.yaml
```


<a id="org736f68f"></a>

# Creating your first NATS cluster

```yaml
apiVersion: "nats.io/v1alpha2"
kind: "NatsCluster"
metadata:
  name: "nats-cluster"
spec:
  size: 3
  version: "2.0.0"
```


<a id="orgc2d80fb"></a>

# Connecting to your cluster
