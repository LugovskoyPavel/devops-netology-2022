# Домашнее задание к занятию «Хранение в K8s. Часть 2»

------

### Задание 1

**Что нужно сделать**

Создать Deployment приложения, использующего локальный PV, созданный вручную.

1. Создать Deployment приложения, состоящего из контейнеров busybox и multitool.
2. Создать PV и PVC для подключения папки на локальной ноде, которая будет использована в поде.
3. Продемонстрировать, что multitool может читать файл, в который busybox пишет каждые пять секунд в общей директории. 
4. Удалить Deployment и PVC. Продемонстрировать, что после этого произошло с PV. Пояснить, почему.
5. Продемонстрировать, что файл сохранился на локальном диске ноды. Удалить PV.  Продемонстрировать что произошло с файлом после удаления PV. Пояснить, почему.
6. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

------
Ответ:
1. Deployment создан

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: busmult-pvc
  name: busmult-pvc
spec:
  selector:
    matchLabels:
      app: busmult-pvc
  replicas: 1 
  template:
    metadata:
      labels:
        app: busmult-pvc
    spec:
      containers:
      - name: multlug
        image: praqma/network-multitool
        volumeMounts:
        - name: pvlug
          mountPath: /input
        env:
        - name: HTTP_PORT
          value: "80"
        - name: HTTPS_PORT
          value: "11443"
        ports:
        - containerPort: 1180 
          name: http-port
        - containerPort: 11443
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
      
      - name: busylug
        image: busybox
        command: ['sh', '-c', 'while (true); do echo K8s-yes! >> /output/success.txt; sleep 5; done;']
        volumeMounts:
        - name: pvlug
          mountPath: /output
      volumes:
      - name: pvlug
        persistentVolumeClaim:
          claimName: lugpvc2
```

2. PV и PVC созданы

```
piVersion: v1
kind: PersistentVolume
metadata:
  name: pvlug
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /data/pvlug
```

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: lugpvc2
spec:
  storageClassName: ""
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

3. multitool читает из общего файла 

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/2b15061a-e60c-4b79-aa28-9a7c633c4306)

4. Deployment и PVC удалены

```
PS C:\Users\lugy1\.kube> kubectl get deployment
NAME          READY   UP-TO-DATE   AVAILABLE   AGE
busmult-pvc   1/1     1            1           14m
PS C:\Users\lugy1\.kube> kubectl get deployment
No resources found in default namespace.
```
![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/55c3479b-73ea-40ae-a494-c6d458b1584c)


```
PS C:\Users\lugy1\.kube> kubectl get pvc       
NAME      STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
lugpvc2   Bound    pvlug    5Gi        RWO                           12m
PS C:\Users\lugy1\.kube> kubectl get pvc
No resources found in default namespace.
```
![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/e1ffa306-0232-4be3-9d39-21aaed0d57fa)

PV сохранился, поскольку не указана политика, предусматривающая, например, удаление, но находится в состоянии Released. 

5. Файл сохранился на ноде

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/74be2e3b-f2f1-4725-98eb-cd610fce5290)

А теперь удалим PV

```
PS C:\Users\lugy1\.kube> kubectl get pv 
NAME    CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS     CLAIM             STORAGECLASS   REASON   AGE
pvlug   5Gi        RWO            Retain           Released   default/lugpvc2                           22m 
PS C:\Users\lugy1\.kube> kubectl get pv
No resources found
```

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/e2f8f0cd-2470-4e09-83d7-353a67cff3f6)

Посмотрим остался ли файл на ноде. Файл остался. Так как ReclaimPolicy: Retain сохраняет файлы даже после удаления pv

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/465197b0-38d7-48f0-aaae-8111ff648193)


------

### Задание 2

**Что нужно сделать**

Создать Deployment приложения, которое может хранить файлы на NFS с динамическим созданием PV.

1. Включить и настроить NFS-сервер на MicroK8S.
2. Создать Deployment приложения состоящего из multitool, и подключить к нему PV, созданный автоматически на сервере NFS.
3. Продемонстрировать возможность чтения и записи файла изнутри пода. 
4. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

------

Ответ:
1. NFS-сервер включен

2.

3.

4.
