## Overview

* Pod: Holds a the Docker container where the application is running.
* Node: A Kubernetes resource that maps to a Google VM and hosts one or more pods.
* ReplicaSet: responsible for creating the pods and ensuring that the amount of replicas is as defined in its YAML file. Whenever you need to perform horizontal scaling (i.e. increase/decrease the amount of pods), change the 'replicas' property defined in the ReplicaSet file.
* NodePort Service: a single, constant point of entry to a group of pods.
* Namespace: a logical boundary for groups of resources. It is used to separate environments.
* Ingress: responsible for redirecting requests to specific services.
* Secret: used for creating the load balancer HTTPS certificate.

The following illustration represents the request flow for a single namespace.

![Ingress](assets/1_KIVa4hUVZxg-8Ncabo8pdg.png)

## Creating Cluster

```
  gcloud container clusters create main-cluster \
  --num-nodes 1 \
  --enable-autoscaling \
  --min-nodes 1 \
  --max-nodes 3 \
  --enable-autoupgrade \
  --machine-type=n1-standard-2 \
          --disk-size=10
```

## Creating the namespaces

Create the namespaces YAML descriptors (https://kubernetes.io/docs/tasks/administer-cluster/namespaces-walkthrough). Example:

```yml
apiVersion: v1
kind: Namespace
metadata:
  name: kubernetes-poc-staging
  labels:
  name: kubernetes-poc-staging
```

#### create the Namespace resource from the YAML file

```
 kubectl create -f ./my-namespace.yaml
```

## Creating Docker Image

```
gcloud builds submit --tag gcr.io/poc-kubernetes-node/nest .
```

## Creating Context

```
kubectl config set-context kubernetes-poc-staging --namespace=kubernetes-poc-staging \
  --cluster=main_cluster 
```

## Apply deployment, service and ingress

```
kubectl apply -f k8s/staging.deployment.yaml
kubectl apply -f k8s/staging.service.yaml
kubectl apply -f k8s/staging.ingress.yaml
kubectl apply -f k8s/master.deployment.yaml
kubectl apply -f k8s/master.service.yaml
kubectl apply -f k8s/master.ingress.yaml
```

