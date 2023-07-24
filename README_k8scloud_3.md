# Домашнее задание к занятию «Как работает сеть в K8s»

-----

### Задание 1. Создать сетевую политику или несколько политик для обеспечения доступа

1. Создать deployment'ы приложений frontend, backend и cache и соответсвующие сервисы.
2. В качестве образа использовать network-multitool.
3. Разместить поды в namespace App.
4. Создать политики, чтобы обеспечить доступ frontend -> backend -> cache. Другие виды подключений должны быть запрещены.
5. Продемонстрировать, что трафик разрешён и запрещён.

-----

### Ответ:

1. Deployment и services приложений создан

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontlug
  namespace: app
spec:
  selector:
    matchLabels:
      app: frontlug
  replicas: 1 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: frontlug
    spec:
      containers:
      - name: frontlug
        image: praqma/network-multitool
        env:
        - name: HTTP_PORT
          value: "1180"
        - name: HTTPS_PORT
          value: "11443"
        ports:
        - containerPort: 80 
          name: http-port
        - containerPort: 443
          name: https-port
        resources:
          requests:
            cpu: "1m"
            memory: "20Mi"
          limits:
            cpu: "10m"
            memory: "20Mi"
        securityContext:
          runAsUser: 0
          capabilities:
            add: ["NET_ADMIN"]
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backlug
  namespace: app
spec:
  selector:
    matchLabels:
      app: backlug
  replicas: 1 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: backlug
    spec:
      containers:
      - name: backlug
        image: praqma/network-multitool
        env:
        - name: HTTP_PORT
          value: "80"
        - name: HTTPS_PORT
          value: "443"
        ports:
        - containerPort: 80 
          name: http-port
        - containerPort: 443
          name: https-port
        resources:
          requests:
            cpu: "1m"
            memory: "20Mi"
          limits:
            cpu: "10m"
            memory: "20Mi"
        securityContext:
          runAsUser: 0
          capabilities:
            add: ["NET_ADMIN"]
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cashlug
  namespace: app
spec:
  selector:
    matchLabels:
      app: cashlug
  replicas: 1 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: cashlug
    spec:
      containers:
      - name: cashlug
        image: praqma/network-multitool
        env:
        - name: HTTP_PORT
          value: "80"
        - name: HTTPS_PORT
          value: "443"
        ports:
        - containerPort: 80 
          name: http-port
        - containerPort: 443
          name: https-port
        resources:
          requests:
            cpu: "1m"
            memory: "20Mi"
          limits:
            cpu: "10m"
            memory: "20Mi"
        securityContext:
          runAsUser: 0
          capabilities:
            add: ["NET_ADMIN"]
```

```
kind: Service
apiVersion: v1
metadata:
  name: servfront
  namespace: app
spec:
  selector:
    app: servfront
  ports:
    - name: servfront
      protocol: "TCP"
      port: 80
  selector:
    app: frontlug
```

```
kind: Service
apiVersion: v1
metadata:
  name: servback
  namespace: app
spec:
  selector:
    app: servback
  ports:
    - name: servback
      protocol: "TCP"
      port: 80
  selector:
    app: backlug
```

```
ind: Service
apiVersion: v1
metadata:
  name: servcash
  namespace: app
spec:
  selector:
    app: servcash
  ports:
    - name: servcash
      protocol: "TCP"
      port: 80
  selector:
    app: cashlug
```

2. Использован образ network-multitool
  
![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/db32957d-4982-4b75-8e0e-3328f9d60c79)

3. Namespace App создан поды размещены

```
PS C:\Users\lugy1\kube-cloud> kubectl create namespace app 
namespace/app created
```
![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/9ae9fbff-14de-4137-86ca-a7f199c22167)

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/91327e4e-5c4a-4481-8c3b-fba03a3bc768)

4. Политики созданы для frontend, backend, cache

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: back-np
  namespace: app
spec:
  podSelector:
    matchLabels:
      app: servback
  policyTypes:
  - Ingress
  - Egress
  ingress:
    - from:
      - podSelector:
          matchLabels:
            app: servfront
      ports:
      - protocol: TCP
        port: 80
```
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: cash-np
  namespace: app
spec:
  podSelector:
    matchLabels:
      app: servcash
  policyTypes:
  - Ingress
  - Egress
  ingress:
    - from:
      - podSelector:
          matchLabels: 
            app: servback
      ports:
      - protocol: TCP
        port: 80  
```
5. Демонстрация что трафик разрешен и запрещён

```   
PS C:\Users\lugy1\kube-cloud> kubectl exec frontlug-9d6f5d88d-sxnxx -- curl --max-time 20 servback
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0  Praqma Network MultiTool (with NGINX) - backlug-f4969c864-746s9 - 192.168.44.68 - HTTP: 80 , HTTPS: 443
<br>
<hr>
<br>

<h1>05 Jan 2022 - Press-release: `Praqma/Network-Multitool` is now `wbitt/Network-Multitool`</h1>

<h2>Important note about name/org change:</h2>
<p>
Few years ago, I created this tool with Henrik Høegh, as `praqma/network-multitool`. Praqma was bought by another company, and now the "Praqma" brand is being dismantled. This means the network-multitool's git and docker repositories must go. Since, I was the one maintaining the docker image for all these years, it was decided by the current representatives of the company to hand it over to me so I can continue maintaining it. So, apart from a small change in the repository name, nothing has changed.<br>
</p>
<p>
The existing/old/previous container image `praqma/network-multitool` will continue to work and will remain available for **"some time"** - may be for a couple of months - not sure though.
</p>
<p>
- Kamran Azeem <kamranazeem@gmail.com> <a href=https://github.com/KamranAzeem>https://github.com/KamranAzeem</a>
</p>

<h2>Some important URLs:</h2>

<ul>
  <li>The new official github repository for this tool is: <a href=https://github.com/wbitt/Network-MultiTool>https://github.com/wbitt/Network-MultiTool</a></li>

  <li>The docker repository to pull this image is now: <a href=https://hub.docker.com/r/wbitt/network-multitool>https://hub.docker.com/r/wbitt/network-multitool</a></li>
</ul>

<br>
Or:
<br>

<pre>
  <code>
  docker pull wbitt/network-multitool
  </code>
</pre>


<hr>

100  1576  100  1576    0     0   7926      0 --:--:-- --:--:-- --:--:-- 1539k
PS C:\Users\lugy1\kube-cloud> kubectl exec frontlug-9d6f5d88d-sxnxx -- curl --max-time 20 servcach
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0curl: (6) Could not resolve host: servcach
command terminated with exit code 6
```

```
PS C:\Users\lugy1\kube-cloud> kubectl exec cashlug-7d5f9d49d4-qddpd -- curl --max-time 20 servback
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:--  0:00:20 --:--:--     0
curl: (28) Connection timed out after 20000 milliseconds
command terminated with exit code 28
PS C:\Users\lugy1\kube-cloud> kubectl exec cashlug-7d5f9d49d4-qddpd -- curl --max-time 20 servfront
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:--  0:00:20 --:--:--     0
curl: (28) Connection timed out after 20001 milliseconds
command terminated with exit code 28
```
