# Service

https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0

StaticIP와 NodePort에 대한 실습

## 예제

### 기본 예제

guide-03/task-05/redis-app.yml

```yml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: redis
spec:
  selector:
    matchLabels:
      type: db
      service: redis
  template:
    metadata:
      labels:
        type: db
        service: redis
    spec:
      containers:
      - name: redis
        image: redis
        ports:
        - containerPort: 6379
          protocol: TCP
---

apiVersion: v1
kind: Service
metadata:
  name: redis
spec:
  ports:
  - port: 6379
    protocol: TCP
  selector:
    type: db
    service: redis
```

guide-03/task-05/whoami-deploy.yml

```yml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: whoami
spec:
  selector:
    matchLabels:
      type: app
      service: whoami
  template:
    metadata:
      labels:
        type: app
        service: whoami
    spec:
      containers:
      - name: whoami
        image: subicura/whoami-redis:1
        env:
        - name: REDIS_HOST
          value: "redis"
        - name: REDIS_PORT
          value: "6379"
```

```
kubectl get ep
kubectl exec -it whoami-<xxxxx> sh
  apk add curl busybox-extras # install telnet
  curl localhost:4567
  curl localhost:4567
  telnet localhost 6379
  telnet redis 6379
    dbsize
    KEYS *
    GET count
    quit
```

### 노드 포트

guide-03/task-05/whoami-svc.yml

```yml
apiVersion: v1
kind: Service
metadata:
  name: whoami
spec:
  type: NodePort
  ports:
  - port: 4567
    protocol: TCP
  selector:
    type: app
    service: whoami
```

## 정리

```
kubectl delete -f ./
```
