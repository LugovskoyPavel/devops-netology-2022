# Домашнее задание к занятию «Управление доступом»

------

### Задание 1. Создайте конфигурацию для подключения пользователя

1. Создайте и подпишите SSL-сертификат для подключения к кластеру.
2. Настройте конфигурационный файл kubectl для подключения.
3. Создайте роли и все необходимые настройки для пользователя.
4. Предусмотрите права пользователя. Пользователь может просматривать логи подов и их конфигурацию (`kubectl logs pod <pod_id>`, `kubectl describe pod <pod_id>`).
5. Предоставьте манифесты и скриншоты и/или вывод необходимых команд.

------

### Ответ:

1. SSL-сертификаты созданы

Создание и настройку сертификата выполнил по инструкции https://habr.com/ru/companies/vk/articles/539984/

Создал yml

```
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: mycsr
spec:
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJRWFqQ0NBbElDQVFBd0pURU9NQXdHQTFVRUF3d0ZjR0YyWld3eEV6QVJCZ05WQkFvT
UNuQmhkbTVsZEc5cwpiMmN3Z2dJaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQ0R3QXdnZ0lLQW9JQ0FRQzBiUkQ3cWVOejRLVytlcjVmCk1JZDNtQi9Cb1pzWlhDd1NUVVlUT3Z2by9ZMj
ZqRDRLdXBrbXRYYjF3Y3RvdFU2V2cxZU5SbVh1Qmc3eEJQS0IKaFNwcC9UQzZBUHhadFQya2l4dUtFeENDU1F5VHpZeEw2Nlp4eC9nak5RM0JnRk5JZlZQN25wSXV2ZHp6NkZnWApQYy9m
SEczRExQK3lUNVorNE8xcWxtZnR1VWg2NlFFMjNaRTFpYitUK21iRGIvM0lNc1Jzc0hWcVlad2dNQTNzCkRyNUkvbDZDclphRFZVdWN2M2tTTGlwcDhJR2FwOVh4Z0dqTFlpR1VWSjFFclB6
S3pFR3E2aUROeU1USUVUZFEKakRUNHJqTTlFNlp0TXBIaStDUVJkSE94ZnpsMzJRVGtoUXcvMjlXZ2VyTWhoTlp2VmNQdmFtOEZLSXo0SkR4Rgp1a3ZUWXpTa2krS3gyRmtUa3Z6NEsvNTd
LbzNXUFJlOHhmcUpkLy83eStIMzVtL0hnUElObHBxdVQ0UThlL1QzCkZ3eXJPNWZhUDNrcHlySWVyRE8xVVk3UGY4WjdwTk9MQW1WbmZ4TGJNTHI2SlJNRzlrTUh4bGdBL3d4akVJTmoKR3
FLcS9xdmZHRStzc3ZySy9KWVBaenFUeDNGVkMxeVFiSkY2SkpCR0pQVTUrRU1RWHVTMUNCU2JvK0hPWVVxRgpWZ3VTMVlsNDMrZWpXajZldTJ1clB1b281QkpPWDFmbDZDWTRZcElsQ1F2T
VUrNEkzSWJFaFBEck5hTW9wNTkzClVaQXBITXFuNUtmTjhMd3kvTXNpUXcxWHFqM1RtT3lqOVJsbzhPOXFmNUQ3a05nV0kyU3l5SFBvNytXTkFxV3cKUHFwNWtpNGhyVnlVVU42OWhRay9Q
c0EvVXdJREFRQUJvQUF3RFFZSktvWklodmNOQVFFTEJRQURnZ0lCQUZNSgpGZHBWQzJtcm1lU3IxOStZNDdPS2pUL1p3K0M0STJVMmx5UHdFWVZ5ZHEwWXo5K0xwd1BreXMySCtVeGcwL3d
wCkM0bG5EZ2dqYkRwcjRiMHVjU0w4LzA1MlY4cUdyRDYxTXY4MjF3dG5FSXdXREx1UzRIaWxnRzVualh6aE5Id0IKVWNUaWgrMHRuY2ZHenMxMTZFSFJTRFZaVnNETzRhMGkvTm1LS3lTNG
E5SitBYWpTUGY1NE1pTmt0MVpZMVdZSQpURGFOdnF3Z2F0YmU1VlFOYmZFYUJCek9hVEJ1Y2VicVFSMSszc2x3UzZqU1dCQnlEV3NvZkdsbENSYmJZMUxCCmRRSDRjWGczV2QyM0p1K3hZK
3VweFl6NW1rTEVPUGhZcnRpbXEzTndKMDNMb2E0MFdaTjdxRWg2b3FnOUV2NkcKRGJTWDJ1TDM5TDQ3NzBRVms3ZEx5S1JxSU53U1IrOVNpbWFyUndIbFRkY0VoTFdvcTZZNElGcFRHbU01
N3pwNQplQldaN2hIWXZUZ2pkTTN0M1FMVW5LTlp3NlBpbnQzUkpMcytuVEVxc05VL20rWGFzYXBnTFhoRTIxM3BVTzB2CjF6Rks2N3VQa0NDcEFxUnlHS1JJV0g2M2JhbnBOSE5tZVU2b2g3
QXRnTkNqSGF5MHBRcXFMSHk2RnBscFBHeTQKZ1ZSQ2FBS1JWaDFCbUJtdlB5UStxOHlTS1lwUnRvWFVwSFFIY2txM243aVd3ZVAveU1JemE1U1Blbjc5NjVvYgp4OEN3U1BLR1c0eDRDYmMy
NzdVd1hvNk93ZDY4SS9HNGxnNVFrNlp6NGNHSC80ZmtwTTNxWmxkS0lmcS8zM05KCklsZDViU0hncSthTUFkWlFaaElXR3gybjVZYVltWXhLbmlnTnlhSXgKLS0tLS1FTkQgQ0VSVElGSUNBVEUgUkVRVUVTVC0tLS0tCg==
  signerName: example.com/serving
  usages:
  - digital signature
  - key encipherment
  - server auth
  - client auth
```
Подписал сертификат на сервере

```
PS C:\Users\lugy1\.kube> kubectl certificate approve mycsr
certificatesigningrequest.certificates.k8s.io/mycsr approved
PS C:\Users\lugy1\.kube> kubectl get csr
NAME    AGE    SIGNERNAME            REQUESTOR   REQUESTEDDURATION   CONDITION
mycsr   2m9s   example.com/serving   admin       <none>              Approved
```

2. Файл kubctl config изменил 

```
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUREekNDQWZlZ0F3SUJBZ0lVSGNGM2FUeVkyT1QwdFpOYXFQRXoyRjJ2WFF3d0RRWUpLb1pJaHZjTkFRRUwKQlFBd0Z6RVZNQk1HQTFVRUF3d01NVEF1TVRVeUxqRTRNeTR4TUI0WERUSXpNRFV3T0RJd01EWTFNMW9YRFRNegpNRFV3TlRJd01EWTFNMW93RnpFVk1CTUdBMVVFQXd3TU1UQXVNVFV5TGpFNE15NHhNSUlCSWpBTkJna3Foa2lHCjl3MEJBUUVGQUFPQ0FROEFNSUlCQ2dLQ0FRRUF4VUFFVFNoMmJ3WTlMczBTcVVoMEtjWTZQYXo2VVlKMDh2dUMKRU9Ja1MxTXg0VENqdnhIblZ5QitEcTFBbU9hN1NCKzgyRnExZXNSNmxIZnlnU0dhY0kwbStyZ0w1T09kQXB2NworWGNFTmV1R25sRmJzY1ZML0VkRktDT1FOWmNTRnZ0QnRaUk45WTFoQzdGQW5Ka3c1eFdIVHBIbnRMQzZ1YXB6CkxsZXdZMUFsQmREMXIzMG5uUWJCbEtaaUthbzE2ZFZZc1UvNGhUcVdYamtITEF0S1dmZkhXanBzdGwrZ2RaNXQKQzdMU1lDMnk1RlliOFJIR2JnZ0NFKzNVVlNaOVZyTUM0bjNwOVA1SSszMm5HUzh2b0NPbzE5VlNCaWhvbmhkagpOL3FqTmdRck9jeFkza25vQUNaeFo3dnlPTVI0UlNLYmVRSFBsaWRaUUNxalhOeTJGUUlEQVFBQm8xTXdVVEFkCkJnTlZIUTRFRmdRVW0rVG1FY3RyZFVsVG1aMzIxMHRpZFE5MUcvY3dId1lEVlIwakJCZ3dGb0FVbStUbUVjdHIKZFVsVG1aMzIxMHRpZFE5MUcvY3dEd1lEVlIwVEFRSC9CQVV3QXdFQi96QU5CZ2txaGtpRzl3MEJBUXNGQUFPQwpBUUVBVE1KQmU0T3hNN2FNYS9XTVdrR2dXSmhOd1Njc2NtVWM2YmpZeDZhMlNGZlNyN054ZThEY3FSQS9MdnVWCmgxL3VmVXNmWUpvL01zNEdIYTdVVGJXKzdtaFNXK2RKM2tDOEFhYXoxRTZ6WFR0eXFmdjB6dE1WUG8yeXJtNTEKclJIbXhoT1M0d2lWbHRQOTY2NE5rL0JTL25oelE5S1dLbU9IMFFLN20yQU9uQllDaDQ1WE51eUpycC80K2h6OQowL0prelhyQ1RMRkZKeE5rK0pLSEFueTRtTS9DRUo5K1l1OHExQnBLVjkvNE9GcjlPK3dPaXlOeDJGcTJJV0t0ClRKNGZTdG12b2U3UlJCekZpY3hwTUxxVGV4YjE4eWhTa3VHRmhCZXdmaDlOdzI3eFpTaEQveFFkRmNLaWNqU3gKdUo3MXZTZ0dEVVBBdm9EdnFZU3d6Z2JsTlE9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
    server: https://172.18.83.189:16443
  name: microk8s-cluster
contexts:
- context:
    cluster: microk8s-cluster
    user: admin
  name: microk8s
- context:
    cluster: microk8s-cluster
    user: pavel
  name: pavel-microk8s
current-context: pavel-microk8s
kind: Config
preferences: {}
users:
- name: admin
  user:
    token: KzNsb0NEUmtOS0JJUlpRY1cwQ2t0NVpuT2MxZGVXaWU0dzJQeGs1K2tUTT0K
- name: pavel
  user:
    client-certificate: pavk8s.crt
    client-key: pav.key
```

3. Создадим пространство имен для пользователя и роль

```
apiVersion: v1
kind: Namespace
metadata:
  name: development
```
```
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: development
  name: pav
rules:
- apiGroups: [""]
  resources: ["pods", "services"]
  verbs: ["create", "get", "update", "list", "delete"]
- apiGroups: ["apps"]
  resources: ["deployments"]
  verbs: ["create", "get", "update", "list", "delete"]
```
Создадим RoleBinding и привяжем роль
```
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: pav
  namespace: development
subjects:
- kind: User
  name: dave
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pav
  apiGroup: rbac.authorization.k8s.io
```
4. Просмотр логов подов и их конфигурацию

Для добавления роли возможности чтения логов подов добавляется 

```
verbs: ["get"]
```

5. Переключимся на пользователя

```
PS C:\Users\lugy1\.kube> kubectl config use-context pavel-microk8s
Switched to context "pavel-microk8s".
PS C:\Users\lugy1\.kube> kubectl describe pod lugnginx-f65677b4-qwrcs                        
Name:             lugnginx-f65677b4-qwrcs
Namespace:        default
Priority:         0
Service Account:  default
Node:             microk8s-vm/172.18.83.189
Start Time:       Sun, 02 Jul 2023 22:48:36 +0300
Labels:           app=lugnginx
                  pod-template-hash=f65677b4
Annotations:      cni.projectcalico.org/containerID: b00646ce380e243046691cdbb28b28a83275ea099fce831ac6f7192e9b68d5a7
                  cni.projectcalico.org/podIP: 10.1.254.67/32
                  cni.projectcalico.org/podIPs: 10.1.254.67/32
Status:           Running
IP:               10.1.254.67
IPs:
  IP:           10.1.254.67
Controlled By:  ReplicaSet/lugnginx-f65677b4
Containers:
  lugnginx:
    Container ID:   containerd://f3c436864aefce202dbaa9712f34a26070c92473afdb26dbf6ce3d1f6644ff32
    Image:          nginx:1.14.2
    Image ID:       docker.io/library/nginx@sha256:f7988fb6c02e0ce69257d9bd9cf37ae20a60f1df7563c3a2a6abe24160306b8d
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Tue, 04 Jul 2023 13:24:12 +0300
    Last State:     Terminated
      Reason:       Unknown
      Exit Code:    255
      Started:      Sun, 02 Jul 2023 22:48:37 +0300
      Finished:     Tue, 04 Jul 2023 13:20:23 +0300
    Ready:          True
    Restart Count:  1
    Environment:    <none>
    Mounts:
      /usr/share/nginx/html/ from foo2 (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-f9qtt (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  foo2:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      nginx-configmap2
    Optional:  false
  kube-api-access-f9qtt:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:                      <none>
```
------

