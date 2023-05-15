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

------

