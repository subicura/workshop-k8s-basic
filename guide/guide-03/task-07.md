# Ingress

nginx-ingress를 먼저 설치함 (K3S는 내장)

## wildcard DNS

ip를 기반으로 도메인을 쉽게 사용할 수 있습니다. 실습에서 사용합니다.

- [sslip.io](https://sslip.io/)
- [xip.io](http://xip.io/)
- [nip.io](https://nip.io/)

```
10.0.0.1.nip.io maps to 10.0.0.1
192-168-1-250.nip.io maps to 192.168.1.250
app.10.8.0.1.nip.io maps to 10.8.0.1
app-37-247-48-68.nip.io maps to 37.247.48.68
customer1.app.10.0.0.1.nip.io maps to 10.0.0.1
customer2-app-127-0-0-1.nip.io maps to 127.0.0.1
```

## 예제

### 기본 예제

guide-03/task-07/whoami-v1.yml

```yml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: whoami-v1
  annotations:
    ingress.kubernetes.io/rewrite-target: "/"
    ingress.kubernetes.io/ssl-redirect: "false"
spec:
  rules:
  - host: v1.whoami.13.125.41.102.sslip.io
    http:
      paths: 
      - path: /
        backend:
          serviceName: whoami-v1
          servicePort: 4567

---

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

apiVersion: v1
kind: Service
metadata:
  name: whoami-v1
spec:
  ports:
  - port: 4567
    protocol: TCP
  selector:
    type: app
    service: whoami
    version: v1
```

```
kubectl get ing
```

### 추가 예제

guide-03/task-07/whoami-v2.yml

```yml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: whoami-v2
  annotations:
    ingress.kubernetes.io/rewrite-target: "/"
    ingress.kubernetes.io/ssl-redirect: "false"
spec:
  rules:
  - host: v2.whoami.13.125.41.102.sslip.io
    http:
      paths: 
      - path: /
        backend:
          serviceName: whoami-v2
          servicePort: 4567

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

---

apiVersion: v1
kind: Service
metadata:
  name: whoami-v2
spec:
  ports:
  - port: 4567
    protocol: TCP
  selector:
    type: app
    service: whoami
    version: v2
```

## 테스트

## Exam 1. 다음 조건을 만족하는 load balancer를 만들어 보세요.

guide-03/task-07/exam-1.yml

- Name: nginx
- Labels: app => nginx
- Container Name: nginx
- Image: nginx
- Replicas: 3
- nginx.\<ip\>.sslip.io

## Exam 2. 방명록 만들기

guide-03/task-07/exam-2.yml

**ingress**

포트
- frontend:xxx

주소
- guestbook.\<ip\>.sslip.io

**frontend**

이미지
- subicura/guestbook-frontend:latest

환경변수
- PORT # ex) 8000
- GUESTBOOK_API_ADDR # ex) APISERVER:8000

**backend**

이미지
- subicura/guestbook-backend:latest

환경변수
- PORT # ex) 8000
- GUESTBOOK_DB_ADDR # ex) DB:27017

**mongodb**

이미지
- mongo:4

기본포트
- 27017

## 정리

```
kubectl delete -f ./
```
