## Application workload monitoring using Prometheus Operator (PO)

[] Yes, you're right, the name of the repo is **PO**metheus, ``pee-ou``... I was trying to highlight the fact that we are using the PO.

### Contents

The contents of this repo are composed by a series of K8s manifests that are intended to show how to leverage [Prometheus Operator](https://github.com/coreos/prometheus-operator). Apply these YAML files, that have been taken from [here](https://github.com/coreos/prometheus-operator/blob/master/Documentation/user-guides/getting-started.md) to your K8s/OKD/OCP cluster in order to:

* Deploy Prometheus Operator (PO), including RBAC required objects, i.e. ``clusterRole``, ``clusterRoleBinding``, and ``serviceAccount``.
* Deploy the sample app to be scrapped, and create the service.
* Create the ``serviceMonitor`` object associated to such a service.
* Create RBAC required objects for Prometheus pods.
* Create a Prometheus object targeting the ``serviceMonitor`` previously created via the ``serviceMonitorSelector`` and matching labels..
* Create a ``nodePort`` service for Prometheus instance, and expose it.
* Create a different ``serviceMonitor`` object for a different application in a different namespace.
* Edit the Prometheus object for including the new ``serviceMonitor`` through the ``serviceMonitorNamespaceSelector``.

### Environment

This demo sample has been tested using a single-node OKD v3.11 cluster deployed on an AWS-provided ``t2.large`` instance.

### Step-by-Step Quickstart Guide

1. Deploy the PO itself:

``kubectl apply -f pometheus-sample-deployment.yml``

2. Deploy example-app and its associated service:

```
kubectl apply -f pometheus-sample-app-dp.yml && \
kubectl apply -f pometheus-sample-app-svc.yml
```

3. Create the ServiceMonitor object:

``kubectl apply -f pometheus-sample-service-monitor.yml``

4. Create RBAC related objects for Prometheus pods:

```
kubectl apply -f pometheus-sample-sa.yml && \
kubectl apply -f pometheus-sample-cr.yml && \
kubectl apply -f pometheus-sample-crb.yml
```

5. Create the Prometheus instance and its associated service:

```
kubectl apply -f pometheus-sample-prom.yml && \
kubectl apply -f pometheus-sample-prom-svc.yml
```

6. Expose the Prometheus Service:

``oc expose service/prometheus``

At this point, you should be able to head to your Prometheus instance's URL for checking that the right targets are getting scrapped.
Now you can add another service to be monitored from a different namespace/project. The only thing you need is to create a new
``serviceMonitor`` object within that namespace/project, and targeting it into the Prometheus object specs using the ``serviceMonitorNamespaceSelector``.

In my example I'm creating such a ``serviceMonitor`` object by applying the ``pometheus-sample-sm-demoapp.yml`` manifest that is referring to a ``demoapplication``namespace, and a ``restservice`` service.

You can do that editing the Prometheus object:

``oc edit prometheus/prometheus``

If you go back to your Prometheus UI, you can see how the operator has been doing its magic under the hood, and the new targets,
and the associated config file elements appear.

### References

* [Getting started with Prometheus Operator](https://github.com/coreos/prometheus-operator/blob/master/Documentation/user-guides/getting-started.md)
* [Design document for Prometheus Operator](https://github.com/coreos/prometheus-operator/blob/master/Documentation/design.md)

