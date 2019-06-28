# Load Balancer

AWS/GKE가 아닌경우 metallb를 설치
k3s는 klipper-lb 내장

## 예제

### 기본 예제

guide-03/task-06/whoami-app.yml

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
---

apiVersion: v1
kind: Service
metadata:
  name: whoami
spec:
  type: LoadBalancer
  ports:
  - port: 8000
    targetPort: 4567
    protocol: TCP
  selector:
    type: app
    service: whoami

---

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

### Canary 예제

guide-03/task-06/whoami-svc-v1-v2.yml

```yml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: whoami-v1
spec:
  replicas: 3
  selector:
    matchLabels:
      type: app
      service: whoami
      version: v1
  template:
    metadata:
      labels:
        type: app
        service: whoami
        version: v1
    spec:
      containers:
      - name: whoami
        image: subicura/whoami:1
        livenessProbe:
          httpGet:
            path: /
            port: 4567

---

apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: whoami-v2
spec:
  replicas: 3
  selector:
    matchLabels:
      type: app
      service: whoami
      version: v2
  template:
    metadata:
      labels:
        type: app
        service: whoami
        version: v2
    spec:
      containers:
      - name: whoami
        image: subicura/whoami:2
        livenessProbe:
          httpGet:
            path: /
            port: 4567
```

### traffic v1 예제

guide-03/task-06/whoami-svc-v1.yml

```yml
apiVersion: v1
kind: Service
metadata:
  name: whoami
spec:
  type: LoadBalancer
  ports:
  - port: 8000
    targetPort: 4567
    protocol: TCP
  selector:
    type: app
    service: whoami
    version: v1
```

### traffic all 예제

guide-03/task-06/whoami-svc-all.yml

```yml
apiVersion: v1
kind: Service
metadata:
  name: whoami
spec:
  type: LoadBalancer
  ports:
  - port: 8000
    targetPort: 4567
    protocol: TCP
  selector:
    type: app
    service: whoami
```

## 테스트

## Exam 1. 다음 조건을 만족하는 load balancer를 만들어 보세요.

guide-03/task-06/exam-1.yml

- Name: nginx
- Labels: app => nginx
- Container Name: nginx
- Image: nginx
- Replicas: 3
- 8000 -> 80

## 정리

```
kubectl delete -f ./
```