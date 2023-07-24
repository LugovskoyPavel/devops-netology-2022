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

1. Deployment приложений создан

2. Использован образ network-multitool

3. Namespace App создан поды размещены

```
PS C:\Users\lugy1\kube-cloud> kubectl create namespace app 
namespace/app created
```
![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/9ae9fbff-14de-4137-86ca-a7f199c22167)

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/91327e4e-5c4a-4481-8c3b-fba03a3bc768)

4. Политики созданы для frontend, backend, cache
