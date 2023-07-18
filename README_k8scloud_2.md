# Домашнее задание к занятию «Установка Kubernetes»

-----

### Задание 1. Установить кластер k8s с 1 master node

1. Подготовка работы кластера из 5 нод: 1 мастер и 4 рабочие ноды.
2. В качестве CRI — containerd.
3. Запуск etcd производить на мастере.
4. Способ установки выбрать самостоятельно.

-----
Ответ:

1. Подготовлен кластер

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/c0d6ff9a-6828-4823-9a11-e9553658bc67)

2. Установлен k8s по этой инструкции https://www.linuxtechi.com/install-kubernetes-on-ubuntu-22-04/

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/338f0146-fe04-4470-b743-fdade1313ede)

3. К кластеру добавлены ноды c cri containerd

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/39ce3ca6-6ddc-4f1a-830a-fd104c36ff9e)


4. ETCD запущена на мастере нода kubcontrol

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/e87b36d1-8728-491d-9a06-31db7503a179)

5. После установки calico

```
lugy1@kubcontrol:~$ kubectl get nodes
NAME         STATUS   ROLES           AGE     VERSION
kubcontrol   Ready    control-plane   68m     v1.27.3
kubworker1   Ready    <none>          9m18s   v1.27.3
```

-----
