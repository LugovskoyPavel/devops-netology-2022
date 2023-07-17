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

2. Установлен k8s по этой инструкции https://computingforgeeks.com/install-kubernetes-cluster-ubuntu-jammy/

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/338f0146-fe04-4470-b743-fdade1313ede)

3. К кластеру добавлены ноды

```
lugy1@kubcontrol:~$ kubectl get nodes
NAME         STATUS     ROLES           AGE     VERSION
kubcontrol   Ready   control-plane   86m     v1.27.3
kubworker1   Ready   <none>          54m     v1.27.3
kubworker2   Ready   <none>          33m     v1.27.3
kubworker3   Ready   <none>          18m     v1.27.3
kubworker4   Ready   <none>          6m22s   v1.27.3
```

-----
