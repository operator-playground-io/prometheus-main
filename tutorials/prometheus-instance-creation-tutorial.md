---
title: Prometheus Instance Creation tutorial
description: This tutorial explains how create Instances for your Prometheus Operator.
---
### Create this CR which will create a Prometheus Instance along with a ServiceAccount and a Service of Type NodePort  

```execute
cat <<'EOF' > prometheusInstance.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: prometheus
---
apiVersion: monitoring.coreos.com/v1
kind: Prometheus
metadata:
  name: prometheus
  labels:
    prometheus: prometheus
spec:
  replicas: 2
  serviceAccountName: prometheus
  securityContext: {}
  serviceMonitorSelector: {}
  ruleSelector: {}
  alerting:
    alertmanagers:
      - namespace: monitoring
        name: alertmanager-main
        port: web
---
apiVersion: v1
kind: Service
metadata:
  name: prometheus
spec:
  type: NodePort
  ports:
  - name: web
    nodePort: 30100
    port: 9090
    protocol: TCP
    targetPort: web
  selector:
    prometheus: prometheus
EOF
```

Execute below command to create Prometheus instance 

```execute
kubectl create -f prometheusInstance.yaml -n operators
```

You will see the following resources to be created:

```output
serviceaccount/prometheus created
prometheus.monitoring.coreos.com/prometheus created
service/prometheus created
```

Get the associated Pods:

```execute
kubectl get pods -n operators
```
You will be able to see the below output:

```output
NAME                                   READY   STATUS    RESTARTS   AGE
prometheus-operator-6f7589ff7f-hswnz   1/1     Running   0          23m
prometheus-prometheus-0                3/3     Running   1          68s
prometheus-prometheus-1                3/3     Running   1          68s
```

Click on the <a href="http://##DNS.ip##:30100" target="_blank">http://##DNS.ip##:30100</a> to access Prometheus Service from your browser.

You will see the Prometheus metrics page as below :

![prometheus-page](_images/prom.png)

This is the minimal steps of getting the Prometheus Operator Instances. You need to modify the instance accordingly for your application.

