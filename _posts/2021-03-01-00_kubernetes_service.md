---
layout: post
title:  "Understanding kubernetes networking: Services"
---

This is the continuing part of previous post, "Understanding kubernetes networking: Pods".
We know how Pods network each other(Through overlay network), however this is not enough.
Pods created in Kubernetes has lifecycle, this means the pod can be deleted and created again at any time with different addresses. Therefore, overlay networks does not fully prove the concept of the kubernetes.<br>
<br>

This can be understood as an old problem; run the traffic through a reverse-proxy/load balancer. Client connects to the proxy and is responsible for maintaining a list of healthy servers to forward requests to.

## Services
This part, the example will come out to explain how kubernetes service enables to load balancing across a set of server pods. To create server pods, we can use deployment such as this:<br>
```aidl
kind: Deployment
apiVersion: extensions/v1beta1
metadata:
  name: service-test
spec:
  replicas: 2
  selector:
    matchLabels:
      app: service_test_pod
  template:
    metadata:
      labels:
        app: service_test_pod
    spec:
      containers:
      - name: simple-http
        image: python:2.7
        imagePullPolicy: IfNotPresent
        command: ["/bin/bash"]
        args: ["-c", "echo \"<p>Hello from $(hostname)</p>\" > index.html; python -m SimpleHTTPServer 8080"]
        ports:
        - name: http
          containerPort: 8080
```
Deployment creates two simple pods with port 8080 with the hostname of the pod they are running in. After deploy this deployment with `kubectl apply`, we can find the pod is running.<br>

```aidl
$ kubectl apply -f test-deployment.yaml
deployment "service-test" created

$ kubectl get pods
service-test-6ffd9ddbbf-kf4j2    1/1    Running    0    15s
service-test-6ffd9ddbbf-qs2j6    1/1    Running    0    15s

$ kubectl get pods --selector=app=service_test_pod -o jsonpath='{.items[*].status.podIP}'
10.0.1.2 10.0.2.2
```

We can test if pod network is working by creating a simple client pod to make a request.

```aidl
apiVersion: v1
kind: Pod
metadata:
  name: service-test-client1
spec:
  restartPolicy: Never
  containers:
  - name: test-client1
    image: alpine
    command: ["/bin/sh"]
    args: ["-c", "echo 'GET / HTTP/1.1\r\n\r\n' | nc 10.0.2.2 8080"]
```
After deploying above Pod, run `kubectl logs` and get the test result.

```aidl
$ kubectl logs service-test-client1
HTTP/1.0 200 OK
<!-- blah -->
<p>Hello from service-test-6ffd9ddbbf-kf4j2</p>
```

Okay, but what if pod die and restart? There is a possiblity for the pod to be created in different node.
Therefore, we use **Service** for the kind.
```aidl
kind: Service
apiVersion: v1
metadata:
  name: service-test
spec:
  selector:
    app: service_test_pod
  ports:
  - port: 80
    targetPort: http
```
Service **is a type of kubernetes resource that causes a proxy to be configured to a forward requests to a set of pods**. The set of pods that will receive traffic is determined by the selector, which matches labels assigned to the pods when they were created.
Once the service is created, we can see that it hase been assigned an IP address and will accept requests on port 80.
```aidl
$ kubectl get service service-test
NAME           CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service-test   10.3.241.152   <none>        80/TCP    11s
```

Request can be sent to the service IP directly, but it would be better to use hostname which resolves to the IP address. Therefore, even when the pod restarted so changed IP address too, still client pod can reach to the server pod.
```aidl
apiVersion: v1
kind: Pod
metadata:
  name: service-test-client2
spec:
  restartPolicy: Never
  containers:
  - name: test-client2
    image: alpine
    command: ["/bin/sh"]
    args: ["-c", "echo 'GET / HTTP/1.1\r\n\r\n' | nc service-test 80"]
```
Test results

```
$ kubectl logs service-test-client2
HTTP/1.0 200 OK
<!-- blah -->
<p>Hello from service-test-6ffd9ddbbf-kf4j2</p>
```
How is this works?

## The service network
If you check the IP addresses, you might notice that the service network is not under the same network boundary with pods.
```aidl
thing        IP               network
-----        --               -------
pod1         10.0.1.2         10.0.0.0/14
pod2         10.0.2.2         10.0.0.0/14
service      10.3.241.152     10.3.240.0/20
```
It's also not in the same as the private network the nodes are on.kub