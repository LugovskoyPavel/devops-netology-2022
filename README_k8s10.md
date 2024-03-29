# Домашнее задание к занятию «Helm»

------

### Задание 1. Подготовить Helm-чарт для приложения

1. Необходимо упаковать приложение в чарт для деплоя в разные окружения. 
2. Каждый компонент приложения деплоится отдельным deployment’ом или statefulset’ом.
3. В переменных чарта измените образ приложения для изменения версии.

------
### Ответ:

1. Приложение упаковано в чарт
Упаковал в чарт простое приложенеи состоящие из nginx

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: "nginx-deployment-clear"
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 1 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```
```
apiVersion: v1
kind: Service
metadata:
  name: ng-clear
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9999
```
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
      - path: /
        pathType: Prefix
        backend:
          service:
            name: ng-clear
            port:
              number: 80
```
2. установил приложение в microk8s

```
PS C:\Users\lugy1\.kube> helm install lugchart --generate-name
NAME: lugchart-1689418058
LAST DEPLOYED: Sat Jul 15 13:47:38 2023
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=lugchart,app.kubernetes.io/instance=lugchart-1689418058" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace default $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace default port-forward $POD_NAME 8080:$CONTAINER_PORT
```

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/12b8354d-1a00-492e-85cc-94e2808d5926)

3. Изменил образ приложения для изменения версии на 1.19.0

```
apiVersion: v2
name: lugchart
description: A Helm chart for Kubernetes

# A chart can be either an 'application' or a 'library' chart.
#
# Application charts are a collection of templates that can be packaged into versioned archives
# to be deployed.
#
# Library charts provide useful utilities or functions for the chart developer. They're included as
# a dependency of application charts to inject those utilities and functions into the rendering
# pipeline. Library charts do not define any templates and therefore cannot be deployed.
type: application

# This is the chart version. This version number should be incremented each time you make changes
# to the chart and its templates, including the app version.
# Versions are expected to follow Semantic Versioning (https://semver.org/)
version: 0.1.0

# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application. Versions are not expected to
# follow Semantic Versioning. They should reflect the version the application is using.
# It is recommended to use it with quotes.
appVersion: "1.19.0"
```
![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/44dc5868-fa07-40ae-9e78-cf2541f6601b)


------
### Задание 2. Запустить две версии в разных неймспейсах

1. Подготовив чарт, необходимо его проверить. Запуститe несколько копий приложения.
2. Одну версию в namespace=app1, вторую версию в том же неймспейсе, третью версию в namespace=app2.
3. Продемонстрируйте результат.

------
### Ответ:

1. Чарт установлен и успешно работает. Изменил количество реплик на 3

```
PS C:\Users\lugy1\.kube> helm upgrade lugchart-1689418058 lugchart --set replicaCount=3 
Release "lugchart-1689418058" has been upgraded. Happy Helming!
NAME: lugchart-1689418058
LAST DEPLOYED: Sat Jul 15 14:15:15 2023
NAMESPACE: default
STATUS: deployed
REVISION: 3
NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=lugchart,app.kubernetes.io/instance=lugchart-1689418058" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace default $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace default port-forward $POD_NAME 8080:$CONTAINER_PORT
```
![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/a1b505be-fb4a-4ff6-bb78-f63a14cea8e4)


2. Запуск чарт в разных неймспейсах

```
S C:\Users\lugy1\.kube> helm install lugchart --namespace app1 --create-namespace --wait --set replicaCount=3 --generate-name 
NAME: lugchart-1689420223
LAST DEPLOYED: Sat Jul 15 14:23:44 2023
NAMESPACE: app1
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace app1 -l "app.kubernetes.io/name=lugchart,app.kubernetes.io/instance=lugchart-1689420223" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace app1 $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace app1 port-forward $POD_NAME 8080:$CONTAINER_PORT
```
```
PS C:\Users\lugy1\.kube> helm install lugchart --namespace app2 --create-namespace --wait --generate-name      
NAME: lugchart-1689420408
LAST DEPLOYED: Sat Jul 15 14:26:48 2023
NAMESPACE: app2
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace app2 -l "app.kubernetes.io/name=lugchart,app.kubernetes.io/instance=lugchart-1689420408" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace app2 $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace app2 port-forward $POD_NAME 8080:$CONTAINER_PORT
```
3. Скриншоты установленных чартов из lens

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/ddb2fe41-46ab-495d-b758-77d0bcc1b403)

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/33042274-7aec-4e55-bab2-32733268557d)

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/ba44b4a3-bc1d-4442-b8bc-9401e55d00dc)
