# Домашнее задание к занятию «Хранение в K8s. Часть 1»

### Задание 1 

**Что нужно сделать**

Создать Deployment приложения, состоящего из двух контейнеров и обменивающихся данными.

1. Создать Deployment приложения, состоящего из контейнеров busybox и multitool.
2. Сделать так, чтобы busybox писал каждые пять секунд в некий файл в общей директории.
3. Обеспечить возможность чтения файла контейнером multitool.
4. Продемонстрировать, что multitool может читать файл, который периодоически обновляется.
5. Предоставить манифесты Deployment в решении, а также скриншоты или вывод команды из п. 4.

------
Ответ:
1. Deployment создан
```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: busmult
  name: busmult
spec:
  selector:
    matchLabels:
      app: busmult
  replicas: 1 
  template:
    metadata:
      labels:
        app: busmult
    spec:
      containers:
      - name: multlug
        image: praqma/network-multitool
        volumeMounts:
        - name: vol
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
        command: ['sh', '-c', 'while true; do echo Success! >> /output/success.txt; sleep 5; done;']
        volumeMounts:
        - name: vol
          mountPath: /output
      volumes:
      - name: vol
        hostPath:
          path: /Data
```
2. Сделано - busybox писал каждые пять секунд в некий файл в общей директории
```
 command: ['sh', '-c', 'while (true); do echo Success! > /output/success.txt; sleep 5; done;']
```
3. Multitool читает из файла
```
PS C:\Users\lugy1\.kube> kubectl exec busmult-5676f585d-t4fqn -c multlug -it -- sh
/ # cat /input/success.txt
Success!
Success!
Success!
Success!
Success!
Success!
```
4. Скрин

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/8dfc7139-8d0f-47d9-a583-7a6f2f8d207a)

------
### Задание 2

**Что нужно сделать**

Создать DaemonSet приложения, которое может прочитать логи ноды.

1. Создать DaemonSet приложения, состоящего из multitool.
2. Обеспечить возможность чтения файла `/var/log/syslog` кластера MicroK8S.
3. Продемонстрировать возможность чтения файла изнутри пода.
4. Предоставить манифесты Deployment, а также скриншоты или вывод команды из п. 2.

------
Ответ:

1. DaemonSet создан

```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: daymlug
  labels:
    app: daymlug
spec:
  selector:
    matchLabels:
      app: daymlug
  template:
    metadata:
      labels:
        app: daymlug
    spec:
      containers:
      - name: multlug
        image: praqma/network-multitool
        volumeMounts:
        - name: vollog
          mountPath: /output
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
      volumes:
      - name: vollog
        hostPath:
          path: /var/log
```

2. Чтение файла `/var/log/syslog` кластера MicroK8S

```
volumes:
      - name: vollog
        hostPath:
          path: /var/log
```

3. Чтение файла из контейнера multlug

```
PS C:\Users\lugy1\.kube> kubectl exec daymlug-n7krt -c multlug -it -- sh
/ # tail -10 /output/syslog
Jun 27 08:42:44 microk8s-vm microk8s.daemon-kubelite[22733]: E0627 08:42:44.426304   22733 manager.go:1121] Failed to create existing container: /kubepods/besteffort/pod5e493d45-e3ff-4dc9-a8a7-6a6fd688807f/6c334af72d84d405e1fa167e3d4d5aeabf131eadc76080f519c9cc024634c2bd: task 6c334af72d84d405e1fa167e3d4d5aeabf131eadc76080f519c9cc024634c2bd not found: not found
Jun 27 08:42:47 microk8s-vm microk8s.daemon-kubelite[22733]: E0627 08:42:47.537442   22733 manager.go:1121] Failed to create existing container: /kubepods/burstable/podf6d00988-b0a3-411a-9dae-a585000e73ee/13e22216be296db87d74c70f15ac4c184331f6a2ae6331baf772f1f4146f57bf: task 13e22216be296db87d74c70f15ac4c184331f6a2ae6331baf772f1f4146f57bf not found: not found
Jun 27 08:42:50 microk8s-vm microk8s.daemon-kubelite[22733]: E0627 08:42:50.652707   22733 manager.go:1121] Failed to create existing container: /kubepods/burstable/pod24327233-e30a-4c27-989d-b9aadbd36419/22947129a82643876e7a2bac07ec6b570f5a6f9524846d4568cef50b563a14b2: task 22947129a82643876e7a2bac07ec6b570f5a6f9524846d4568cef50b563a14b2 not found: not found
Jun 27 08:42:53 microk8s-vm microk8s.daemon-kubelite[22733]: E0627 08:42:53.761733   22733 manager.go:1121] Failed to create existing container: /kubepods/besteffort/pod5e493d45-e3ff-4dc9-a8a7-6a6fd688807f/f207830bc124bf6df3906b0641a3c7939d77b6c149cfd73560237b5533ee2ce4: task f207830bc124bf6df3906b0641a3c7939d77b6c149cfd73560237b5533ee2ce4 not found: not found
Jun 27 08:42:56 microk8s-vm microk8s.daemon-kubelite[22733]: E0627 08:42:56.874341   22733 manager.go:1121] Failed to create existing container: /kubepods/besteffort/pod022608cf-56bc-408f-b741-6458995af9cf/243d4e76e4f101089d4f3b3a7b478bd5563c902d29319124abb528fd12ed57a0: task 243d4e76e4f101089d4f3b3a7b478bd5563c902d29319124abb528fd12ed57a0 not found: not found
Jun 27 08:42:58 microk8s-vm microk8s.daemon-kubelite[22733]: E0627 08:42:58.833589   22733 upgradeaware.go:426] Error proxying data from client to backend: read tcp 172.29.77.3:16443->172.29.64.1:62567: read: connection reset by peer
Jun 27 08:42:59 microk8s-vm microk8s.daemon-kubelite[22733]: E0627 08:42:59.985316   22733 manager.go:1121] Failed to create existing container: /kubepods/burstable/pod434f3ccb-af20-4c32-95bd-efa453fd0d88/d83749df63192b29d9ca671b5708fbcdd1ea61ece52fc0b7097a4770d995313d: task d83749df63192b29d9ca671b5708fbcdd1ea61ece52fc0b7097a4770d995313d not found: not found
Jun 27 08:43:03 microk8s-vm microk8s.daemon-kubelite[22733]: E0627 08:43:03.103701   22733 manager.go:1121] Failed to create existing container: /kubepods/burstable/podf6d00988-b0a3-411a-9dae-a585000e73ee/e727642cdb6976bcadf6c57b50c1fad5ce752eea9508787a8b2b820b7303f821: task e727642cdb6976bcadf6c57b50c1fad5ce752eea9508787a8b2b820b7303f821 not found: not found
Jun 27 08:43:06 microk8s-vm microk8s.daemon-kubelite[22733]: E0627 08:43:06.223348   22733 manager.go:1121] Failed to create existing container: /kubepods/besteffort/pode0a0cf50-e2b8-47d5-93ef-56b954a4e1f7/15f6b6a2eb61a08daf46136fddfb7df4267bfc784ebddd66a293f120cda0bb66: task 15f6b6a2eb61a08daf46136fddfb7df4267bfc784ebddd66a293f120cda0bb66 not found: not found
Jun 27 08:43:09 microk8s-vm microk8s.daemon-kubelite[22733]: E0627 08:43:09.333524   22733 manager.go:1121] Failed to create existing container: /kubepods/besteffort/poda798fb2c-cfc9-4764-a453-340674dd4c43/ff824ef78c24c5a4c70e77e08d72993e8a187847516abea6dbcdc33757d9f0d5: task ff824ef78c24c5a4c70e77e08d72993e8a187847516abea6dbcdc33757d9f0d5 not found: not found
```

4. Скрин вывода логов

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/c0d10b82-4168-4066-995a-1a4b3c440859)


------
