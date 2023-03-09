---
author: Patrik Kaura
pubDatetime: 2023-03-09T20:56:42Z
title: Kustomize - Kubernetes resource customization
postSlug: kubernetes-resource-customization
featured: false
draft: false
tags:
  - kubernetes
  - devops
ogImage: ""
description: Using kustomize to customize Kubernetes resources.
---

## Description

Kustomize is a tool that allows you to customize Kubernetes resources without
forking them. It is a part of the Kubernetes project and is used by many
companies to manage their Kubernetes deployments.

![kustomize](/assets/kustomize/overlay.jpg)

## Installation

Kustomize is part of the kubectl tool. You can check it's version with the:

```bash
$ kubectl kustomize version
```

## Usage

In our case we have simple folder structure for different environments:

```
├── base
|── master ----------> cluster.1
|── staging ---------> cluster.1
|── pre-production --> cluster.1
|── production ------> cluster.1
               ------> cluster.2
```

In base folder we have to create all the resources that are common for all
environments. For example, we have a deployment and a service:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: application
spec:
  replicas: 2
  selector:
    matchLabels:
      app: application
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 1
  template:
    metadata:
      annotations:
        team: "team@email.cz"
        metrics.scrape: "true"
        metrics.scheme: "http"
        metrics.port: "80"
        metrics.path: "/metrics"
        log.format: "json"
      labels:
        app: application
    spec:
      containers:
        - name: application
          image: DOCKER_IMAGE
          imagePullPolicy: IfNotPresent
          resources:
            requests:
              cpu: 200m
              memory: 256Mi
            limits:
              cpu: 500m
              memory: 1Gi
          livenessProbe:
            httpGet:
              path: "/liveness"
              port: 80
            initialDelaySeconds: 30
            timeoutSeconds: 5
          readinessProbe:
            httpGet:
              path: "/readiness"
              port: 80
            initialDelaySeconds: 30
            timeoutSeconds: 5
          ports:
            - containerPort: 80
              protocol: TCP
          env:
            - name: CONF_DATABASE_USER
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: user
            - name: CONF_DATABASE_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: password
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: application
labels:
    app: application
spec:
type: ClusterIP
loadBalancerIP: ""
ports:
    - port: 80
    name: http
    protocol: TCP
selector:
    app: application
```

The next step is to create a kustomization.yaml file in the base folder.
This file will contain all the resources that are common for all environments.

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
  - deployment.yaml
  - service.yaml
```

Now we can create a kustomization.yaml file in each environment folder.
We either can update deployment and service dirrectly in the kustomization.yaml
or we can create a patch file for each resource.

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - ../../base         <-- path to base folder with non templated resources
patches:
  - path: patch-deployment.yaml  <-- patch file for deployment
    target:
      group: apps
      kind: Deployment
      name: application
      version: v1
  - path: patch-service.yaml    <-- patch file for service
    target:
      kind: Service
      name: application
      version: v1
images:
  - name: DOCKER_IMAGE
    newName: docker.com/application
    newTag: 1.0.0
```

In patch-deployment.yaml we can update any field from the deployment resource.
In our case we want update number of replicas to 1.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: application
spec:
  replicas: 1
```

Same thing we can do with the service resource. In our case we want to change
IP address for load balancer.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: application
spec: loadBalancerIP 10.10.10.10
```

After running the command `kubectl kustomize .` we will get the final
deployment and service resources. The final deployments are separated by `---`.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: application
labels:
    app: application
spec:
  loadBalancerIP: 10.10.10.10   <-- Updated IP address
  ports:
  - name: http
    port: 80
    protocol: TCP
  selector:
    app: application
  type: ClusterIP
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: application
spec:
  replicas: 1                    <-- Updated number of replicas
  selector:
    matchLabels:
      app: application
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 1
    type: RollingUpdate
  template:
    metadata:
      annotations:
        log.format: json
        metrics.path: /metrics
        metrics.port: "80"
        metrics.scheme: http
        metrics.scrape: "true"
        team: 'team@email.cz'
      labels:
        app: application
    spec:
      containers:
      - env:
        - name: CONF_DATABASE_USER
            valueFrom:
            secretKeyRef:
                name: db-secret
                key: user
        - name: CONF_DATABASE_PASSWORD
            valueFrom:
            secretKeyRef:
                name: db-secret
                key: password
        image: docker.com/application:1.0.0
        imagePullPolicy: IfNotPresent
        livenessProbe:
          httpGet:
            path: /liveness
            port: 80
          initialDelaySeconds: 30
          timeoutSeconds: 5
        name: application
        ports:
        - containerPort: 80
          protocol: TCP
        readinessProbe:
          httpGet:
            path: /readiness
            port: 80
          initialDelaySeconds: 30
          timeoutSeconds: 5
        resources:
          limits:
            cpu: 500m
            memory: 1Gi
          requests:
            cpu: 200m
            memory: 256Mi
```

Final product is easy to split into separate files and deploy to Kubernetes thru CI/CD pipeline.

## Conclusion

Kustomize is a great tool for templating Kubernetes resources. It is easy to
use and it is very powerful. It is also very easy to integrate with CI/CD
pipelines.

### Sources

- Kustomize - https://kustomize.io/
- Kustomize Github - https://github.com/kubernetes-sigs/kustomize
