
# Table of Contents



For example to modify the [security context](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) from the Pods:

    apiVersion: "nats.io/v1alpha2"
    kind: "NatsCluster"
    metadata:
      name: "nats-cluster"
    spec:
      size: 3
      version: "2.0.0"
      template:
        spec:
          securityContext:
            runAsUser: 1000
            runAsGroup: 3000
            fsGroup: 2000

