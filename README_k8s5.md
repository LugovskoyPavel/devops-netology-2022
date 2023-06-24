 # Домашнее задание к занятию «Сетевое взаимодействие в K8S. Часть 2»

------

### Задание 1. Создать Deployment приложений backend и frontend

1. Создать Deployment приложения _frontend_ из образа nginx с количеством реплик 3 шт.
2. Создать Deployment приложения _backend_ из образа multitool. 
3. Добавить Service, которые обеспечат доступ к обоим приложениям внутри кластера. 
4. Продемонстрировать, что приложения видят друг друга с помощью Service.
5. Предоставить манифесты Deployment и Service в решении, а также скриншоты или вывод команды п.4.

Ответ:

1. Deployment приложения _frontend_ создан

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: depfront
  name: depfront
spec:
  selector:
    matchLabels:
      app: depfront
  replicas: 3
  template:
    metadata:
      labels:
        app: depfront
    spec:
      containers:
        - name: depfront
          image: nginx:1.14.2
          ports:
            - name: http
              containerPort: 80
```

2. Deployment приложения _backend_ создан

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: depback
  name: depback
spec:
  selector:
    matchLabels:
      app: depback
  replicas: 3
  template:
    metadata:
      labels:
        app: depback
    spec:
      containers:
        - name: depback-mult
          image: praqma/network-multitool
          ports:
            - name: http
              containerPort: 1180
```
   
3. Service, которые обеспечат доступ к обоим приложениям внутри кластера создан

```
kind: Service
apiVersion: v1
metadata:
  name: servfront
spec:
  selector:
    app: depfront
  ports:
    - protocol: TCP
      port: 80
      nodePort: 30080
  type: NodePort
```
```
kind: Service
apiVersion: v1
metadata:
  name: servback
spec:
  selector:
    app: depback
  ports:
    - protocol: TCP
      port: 1180
      targetPort: http
```
   
4. приложения видят друг друга

```
PS C:\Users\lugy1\.kube> kubectl exec --stdin --tty services/servback -- /bin/bash       
bash-5.1# curl http://servfront
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
5. Скрин

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/b8a5aec0-ec6e-4d2e-bd44-f64b8094e65f)


------

### Задание 2. Создать Ingress и обеспечить доступ к приложениям снаружи кластера

1. Включить Ingress-controller в MicroK8S.
2. Создать Ingress, обеспечивающий доступ снаружи по IP-адресу кластера MicroK8S так, чтобы при запросе только по адресу открывался _frontend_ а при добавлении /api - _backend_.
3. Продемонстрировать доступ с помощью браузера или `curl` с локального компьютера.
4. Предоставить манифесты и скриншоты или вывод команды п.2.

Ответ:
1. Контроллер включен
```
microk8s enable ingress
Infer repository core for addon ingress
Enabling Ingress
ingressclass.networking.k8s.io/public created
ingressclass.networking.k8s.io/nginx created
namespace/ingress created
serviceaccount/nginx-ingress-microk8s-serviceaccount created
clusterrole.rbac.authorization.k8s.io/nginx-ingress-microk8s-clusterrole created
role.rbac.authorization.k8s.io/nginx-ingress-microk8s-role created
clusterrolebinding.rbac.authorization.k8s.io/nginx-ingress-microk8s created
rolebinding.rbac.authorization.k8s.io/nginx-ingress-microk8s created       
configmap/nginx-load-balancer-microk8s-conf created
configmap/nginx-ingress-tcp-microk8s-conf created
configmap/nginx-ingress-udp-microk8s-conf created
daemonset.apps/nginx-ingress-microk8s-controller created
Ingress is enabled
```

2. Ingress создан
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: lugnginx
  annotations: 
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx-ingress
  rules:
  - http:
      paths:
      - path: /test
        pathType: Prefix
        backend:
          service:
            name: servfront
            port:
              number: 30080

```
3. Доступ получен
```
bash-5.1# curl -kL http://localhost:80/test
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx/1.18.0</center>
</body>
</html>
bash-5.1#
```
4. Скрин

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/e9c51979-083e-4fec-8ef6-efcbec9691ae)


------
