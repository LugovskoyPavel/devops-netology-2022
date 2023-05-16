# Домашнее задание к занятию «Базовые объекты K8S»

------

### Задание 1. Создать Pod с именем hello-world

1. Создать манифест (yaml-конфигурацию) Pod.
2. Использовать image - gcr.io/kubernetes-e2e-test-images/echoserver:2.2.
3. Подключиться локально к Pod с помощью `kubectl port-forward` и вывести значение (curl или в браузере).

Ответ:

1. Создан yaml файл c pod с использованием указанного образа:

```
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
spec:
  containers:
  - name: hello-world
    image: gcr.io/kubernetes-e2e-test-images/echoserver:2.2
    ports:
    - containerPort: 85
 ```
2. Вывод pod (уже с pod из второго задания)
```
PS C:\Users\lugy1\.kube> kubectl get pods   
NAME           READY   STATUS    RESTARTS   AGE
hello-world    1/1     Running   0          58m
netology-web   1/1     Running   0          39m
```

4. Произведено подключение к pod

```
PS C:\Users\lugy1\.kube> kubectl port-forward hello-world 85:8080
Forwarding from 127.0.0.1:85 -> 8080
Forwarding from [::1]:85 -> 8080
Handling connection for 85
Handling connection for 85
```

![k8s_pod](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/6d36cf42-d234-45e1-a030-b9c07a8cac2e)

------

### Задание 2. Создать Service и подключить его к Pod

1. Создать Pod с именем netology-web.
2. Использовать image — gcr.io/kubernetes-e2e-test-images/echoserver:2.2.
3. Создать Service с именем netology-svc и подключить к netology-web.
4. Подключиться локально к Service с помощью `kubectl port-forward` и вывести значение (curl или в браузере).

Ответ:

1+2. Создан  pod с использованием указанного образа:
```
apiVersion: v1
kind: Pod
metadata:
  name: netology-web
  labels:
    app.kubernetes.io/name: mynetology
spec:
  containers:
  - name: netology-web
    image: gcr.io/kubernetes-e2e-test-images/echoserver:2.2
    ports:
    - containerPort: 8886
```
3. Создан сервис netology-svc и подключён к netology-web
```
apiVersion: v1
kind: Service
metadata:
  name: netology-svc
spec:
  selector:
    app.kubernetes.io/name: mynetology
  ports:
    - protocol: TCP
      port: 8886
      targetPort: 8080
```
```
PS C:\Users\lugy1\.kube> kubectl describe service netology-svc
Name:              netology-svc
Namespace:         default
Labels:            <none>
Annotations:       <none>
Selector:          app.kubernetes.io/name=mynetology
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.152.183.23
IPs:               10.152.183.23
Port:              <unset>  8886/TCP
TargetPort:        8080/TCP
Endpoints:         10.1.254.125:8080
Session Affinity:  None
Events:            <none>
```

4. Произведено подключение к services. пришлось чуток помучиться с пониманием port-forward, но в итоге вроде получилось.
```
PS C:\Users\lugy1\.kube> kubectl port-forward service/netology-svc 8886:8886
Forwarding from 127.0.0.1:8886 -> 8080
Forwarding from [::1]:8886 -> 8080
Handling connection for 8886
```
```
PS C:\Users\lugy1\.kube> curl http://localhost:8886

Hostname: netology-web

Pod Information:
        -no pod information available-

Server values:
        server_version=nginx: 1.12.2 - lua: 10010

Request Information:
        client_address=127.0.0.1
        method=GET
        real path=/
        query=
        request_version=1.1
        request_scheme=http
        request_uri=http://localhost:8080/

Request Headers:
        accept=*/*
        host=localhost:8886
        user-agent=curl/8.0.1

Request Body:
        -no body in request-
```
------

