---
title: "K8S部署Redis-3.0.7"
date: 2023-05-21
draft: false
tags: ["k8s", "mysql"]
author: "张大锅"
---

## k8s部署redis 3.0.7

### Deployment

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
  labels:
    app: redis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis
        image: redis:3.0.7
        command:
          - "sh"
          - "-c"
          - "redis-server /usr/local/redis/redis.conf"
        ports:
        - containerPort: 6379
        resources:
          limits:
            cpu: 1000m
            memory: 1024Mi
          requests:
            cpu: 1000m
            memory: 1024Mi
        livenessProbe:
          tcpSocket:
            port: 6379
          initialDelaySeconds: 300
          timeoutSeconds: 1
          periodSeconds: 10
          successThreshold: 1
          failureThreshold: 3
        readinessProbe:
          tcpSocket:
            port: 6379
          initialDelaySeconds: 5
          timeoutSeconds: 1
          periodSeconds: 10
          successThreshold: 1
          failureThreshold: 3
        volumeMounts:
        - name: config
          mountPath:  /usr/local/redis/redis.conf
          subPath: redis.conf
      volumes:
      - name: config
        configMap:
          name: redis-config
```