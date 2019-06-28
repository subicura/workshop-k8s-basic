# Horizontal Pod Autoscaler

metric-server 설치 필요

https://github.com/kubernetes-incubator/metrics-server

## 예제

guide-03/bonus/hpa-example-deploy.yml

```yml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: hpa-example-deploy
spec:
  selector:
    matchLabels:
      type: app
      service: hpa-example
  template:
    metadata:
      labels:
        type: app
        service: hpa-example
    spec:
      containers:
      - name: hpa-example
        image: k8s.gcr.io/hpa-example
        resources:
            limits:
              cpu: "0.5"
            requests:
              cpu: "0.25"
---

apiVersion: v1
kind: Service
metadata:
  name: hpa-example
spec:
  ports:
  - port: 80
    protocol: TCP
  selector:
    type: app
    service: hpa-example
```

부하 테스트

```
kubectl run -it load-generator --image=busybox /bin/sh
while true; do wget -q -O- http://hpa-example; done # after 1 minute
```

guide-03/bonus/hpa.yml

```yml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: hpa-example
spec:
  maxReplicas: 4
  minReplicas: 1
  scaleTargetRef:
    apiVersion: extensions/v1
    kind: Deployment
    name: hpa-example-deploy
  targetCPUUtilizationPercentage: 30
```

```
kubectl get hpa
```
