## Application workload monitoring using Prometheus Operator

### Contents

The contents of this repo are composed by a series of K8s manifests that are intended to show how to leverage [Prometheus Operator](https://github.com/coreos/prometheus-operator)
Applying these YAML files to your K8s/OKD/OCP cluster will be:

* Deploy Prometheus Operator (PO), including RBAC required objects, i.e. ``clusterRole``, ``clusterRoleBinding``, and ``serviceAccount``.
* Deploy the sample app to be scrapped, and create the service.
* Create the ``serviceMonitor`` object associated to such a service.
* Create RBAC required objects for Prometheus pods.
* Create a Prometheus object targeting the ``serviceMonitor`` previously created via the ``serviceMonitorSelector`` and matching labels..
* Create a ``nodePort`` service for Prometheus instance, and expose it.
* Create a different ``serviceMonitor`` object for a different application in a different namespace.
* Edit the Prometheus object for including the new ``serviceMonitor`` through the ``serviceMonitorNamespaceSelector``.

### Environment

This demo sample has been tested using a single node OKD cluster deployed on an AWS-provided ``t2.large`` instance.

### Step-by-Step Quickstart Guide

1. Deploy the PO itself:

``kubectl apply -f pometheus-sample-deployment.yml``

2. Deploy example-app and its associated service:

``kubectl apply -f pometheus-sample-app-dp.yml && kubectl apply -f pometheus-sample-app-svc.yml``

3. Create the ServiceMonitor object:

``kubectl apply -f pometheus-sample-service-monitor.yml``

4. Create RBAC related objects for Prometheus pods:

``kubectl apply -f pometheus-sample-sa.yml && kubectl apply -f pometheus-sample-cr.yml && kubectl apply -f pometheus-sample-crb.yml``

5. Create the Prometheus instance and its associated service:

``kubectl apply -f pometheus-sample-prom.yml && kubectl apply -f pometheus-sample-prom-svc.yml``

6. Expose the Prometheus Service:

``oc expose service/prometheus``

