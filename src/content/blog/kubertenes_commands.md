---
author: Patrik Kaura
pubDatetime: 2023-03-12T19:17:00Z
title: ☁️ 6. Base kubernetes commands for everyday use
postSlug: kubernetes-commands-for-everyday-use
featured: false
draft: false
tags:
  - kubernetes
  - devops
ogImage: ""
description: "Base kubernetes commands for everyday use"
---

## Description

During my work I collected set of commands that I use on daily basis. I hope that this post will help you to get started with kubernetes.

## Common kubernetes commands

I use `kc` as alias for `kubectl`, `ks` for `kubens` and `kx` for `kubectx`. So In this post I will use these aliases.

### 1. Get information about deployment

```
kc get deployment

NAME         READY   UP-TO-DATE   AVAILABLE   AGE
nginx-depl   0/1     1            0           14s
```

### 2. Get detailed information about deployment

```
kc get deployment nginx-deployment -o yaml

...
status:
  availableReplicas: 2
  conditions:
  - lastTransitionTime: "2022-12-28T17:37:12Z"
    lastUpdateTime: "2022-12-28T17:37:26Z"
    message: ReplicaSet "nginx-deployment-ff6774dc6" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
	...
  observedGeneration: 2
  readyReplicas: 2
  replicas: 2
  updatedReplicas: 2
```

### 3. Delete deployment

```
kc delete deployment mongo-depl

deployment.apps "mongo-depl" deleted
```

### 4. Get information about pods

```
kc get pods

NAME                         READY   STATUS    RESTARTS   AGE
nginx-depl-c88549479-vkkpc   1/1     Running   0          76s
```

### 5. Get detailed information about pod

```
kc get pod -o wide

NAME                               READY   STATUS    RESTARTS   AGE    IP           NODE       NOMINATED NODE   READINESS GATES
nginx-deployment-ff6774dc6-lwwf7   1/1     Running   0          112m   172.17.0.6   minikube   <none>           <none>
nginx-deployment-ff6774dc6-vhzbq   1/1     Running   0          113m   172.17.0.5   minikube   <none>           <none>
```

### 6. Imidiatelly delete pod

```
kc delete pod --force --grace-period=0 <pod-name>
```

### 7. Imidiatelly delete job

```
kc delete job --force --grace-period=0 <pod-name>
```

### 8. Restart pod

```
kc rollout restart deployment/<deployment-name>
```

### 9. Get replica set information

```
kc get replicaset

NAME                   DESIRED   CURRENT   READY   AGE
nginx-depl-c88549479   1         1         1       3m12s
```

### 10. Scale deployment

```
kc scale --replicas=1 deployment/<server-name>
```

### 11. Apply changes or create new resource

```
kc apply -f nginx-deployment.yaml
kc apply -f nginx-service.yaml
```

### 12. Get information about services

```
kc describe service nginx-service

Name:              nginx-service
Namespace:         default
Labels:            <none>
Annotations:       <none>
Selector:          app=nginx
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.103.205.157
IPs:               10.103.205.157
Port:              <unset>  80/TCP
TargetPort:        8080/TCP
Endpoints:         172.17.0.5:8080,172.17.0.6:8080
Session Affinity:  None
Events:            <none>
```

### 13. Get information about ingress

```
kc get ingress

NAME                CLASS   HOSTS           ADDRESS        PORTS   AGE
dashboard-ingress   nginx   dashboard.com   192.168.49.2   80      2m5s
```

### 14. Create new namespace

```
kc create namespace my-namespace
```

### 15. Delete namespace

```
kc delete namespace my-namespace
```

### 16. Get information about secret

```
kc get secret <name> -o yaml
```

### 18. Change namespace

```
ks <namespace-name>
```

### 19. Change cluster

```
kx <cluster-name>
```

### Conclusion

This curated list of commands will be updated in future. If you have any suggestions please let me know.

### Sources

- Kubectl - https://kubernetes.io/docs/tasks/tools/
- Kubectx + kubens - https://github.com/ahmetb/kubectx
