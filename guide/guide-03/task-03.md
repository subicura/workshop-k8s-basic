# Replicaset

ReplicaSet -> Find pod by labels -> Create pod from template

## 예제

### 기본 예제

guide-03/task-03/whoami-rs.yml

```yml
apiVersion: apps/v1beta2
kind: ReplicaSet
metadata:
  name: whoami-rs
spec:
  replicas: 1
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
        image: subicura/whoami:1
        livenessProbe:
          httpGet:
            path: /
            port: 4567
```

```
kubectl get pods --show-labels
kubectl label pod/whoami-rs-<xxxx> service-
kubectl label pod/whoami-rs-<xxxx> service=whoami
kubectl scale --replicas=3 -f whoami.yml
```

### 스케일 아웃 예제

guide-03/task-03/whoami-rs-scaled.yml

```yml
apiVersion: apps/v1beta2
kind: ReplicaSet
metadata:
  name: whoami-rs
spec:
  replicas: 4
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
        image: subicura/whoami:1
        livenessProbe:
          httpGet:
            path: /
            port: 4567
```

## Exam 1. 다음 조건을 만족하는 replicaset을 만들어 보세요.

guide-03/task-03/exam-1.yml

- Name: nginx
- Labels: app => nginx
- Container Name: nginx
- Image: nginx
- Replicas: 3

## 정리

```
kubectl delete -f ./
```
