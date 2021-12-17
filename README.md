## Задача 1: Работа с сервис-аккаунтами через утилиту kubectl в установленном minikube </br>
Выполните приведённые команды в консоли. Получите вывод команд. Сохраните </br>
задачу 1 как справочный материал. </br>
### Как создать сервис-аккаунт? </br>
```
kubectl create serviceaccount netology
```
![](https://github.com/murzinvit/screen_1/blob/aaf55209818e3278598623e0567858c98a1a4343/Kuber_create_service_account.jpg) </br>

### Как просмотреть список сервис-акаунтов?
```
kubectl get serviceaccounts
kubectl get serviceaccount
```
![](https://github.com/murzinvit/screen_1/blob/437aaaf710f207daf28fb4bd979aca68fc58fe3e/Kuber_get_serviceaccount.jpg) </br>

### Как получить информацию в формате YAML и/или JSON?
```
kubectl get serviceaccount netology -o yaml
kubectl get serviceaccount default -o json
```
![](https://github.com/murzinvit/screen_1/blob/5e549782e5eda9df92a7008ffade5ea6c3c77172/Kuber_get_svc_account_yaml.jpg) </br>

### Как выгрузить сервис-акаунты и сохранить его в файл?
```
kubectl get serviceaccounts -o json > serviceaccounts.json
kubectl get serviceaccount netology -o yaml > netology.yml
```
![](https://github.com/murzinvit/screen_1/blob/949a77db03b3c5d9e81130dbfebfe6d0ca375405/Kuber_svcacc_download_yaml.jpg) </br>

### Как удалить сервис-акаунт?
```
kubectl delete serviceaccount netology
```
![](https://github.com/murzinvit/screen_1/blob/68ab3ba9ff281896208fa0d8a25fd35de530e64e/Kuber_delete_svc_accounts.jpg) </br>

### Как загрузить сервис-акаунт из файла?
```
kubectl apply -f netology.yml
```
![](https://github.com/murzinvit/screen_1/blob/75c0de340d327d1627f06fdad54530db2c9dc85c/Kuber_upload_secretaccount.jpg) </br>


## Задача 2 (*): Работа с сервис-акаунтами внутри модуля

Выбрать любимый образ контейнера, подключить сервис-акаунты и проверить
доступность API Kubernetes
```
kubectl run -i --tty fedora --image=fedora --restart=Never -- sh
```

Просмотреть переменные среды
```
env | grep KUBE
```
![](https://github.com/murzinvit/screen_1/blob/54b7059f18674d09c45d47175bac28f46ca6cedc/Kuber_get%20_env_fedora_pod.jpg) </br>

Получить значения переменных
```
K8S=https://$KUBERNETES_SERVICE_HOST:$KUBERNETES_SERVICE_PORT
SADIR=/var/run/secrets/kubernetes.io/serviceaccount
TOKEN=$(cat $SADIR/token)
CACERT=$SADIR/ca.crt
NAMESPACE=$(cat $SADIR/namespace)
```
![](https://github.com/murzinvit/screen_1/blob/68445fdde5075ddfec4308d0337bbe02dc101894/Kuber_export_env_in_pod.jpg) </br>

Подключаемся к API
```
curl -H "Authorization: Bearer $TOKEN" --cacert $CACERT $K8S/api/v1/
```
![](https://github.com/murzinvit/screen_1/blob/126f2e27637f9a14fc5a9df983f23e9e507d3378/Kuber_get_token_inside_pod.jpg) </br>

В случае с minikube может быть другой адрес и порт, который можно взять здесь
```
cat ~/.kube/config
```

или здесь
```
kubectl cluster-info
```
---
User Accounts – профили обычных пользователей, используемые для доступа к клатеру снаружи кластера </br> 
Service Accounts - используются для аутентификации сервисов внутри кластера </br>

#### Создание serviceaccount с правами просмотра: </br>
`kubectl -n kube-system create serviceaccount netology-user` </br>
`kubectl -n kube-system create clusterrolebinding netology-log-viewer --clusterrole=view --serviceaccount=kube-system:netology-user`</br>
`export USERNETTOKEN=$(kubectl -n kube-system get serviceaccount/netology-user -o jsonpath='{.secrets[0].name}')`</br> 
`export USERTOKEN=$(kubectl -n kube-system get secret $USERNETTOKEN -o jsonpath='{.data.token}' | base64 --decode)` </br> 
`curl -k -H "Authorization: Bearer $USERTOKEN" -X GET "https://k8s-master1:6443/api/v1/pods" | jq` </br>
Просмотр api/v1/pods для netology-user возможен: 
![](https://github.com/murzinvit/screen_1/blob/c7be3c7211ac82c9e4d0dcf02e71f4da839b286e/Kuber_get_pod_user_account.png) </br>
Просмотр api/v1/secrets и api/v1/nodes  для netology-user заблокирован:</br>
`curl -k -H "Authorization: Bearer $USERTOKEN" -X GET "https://k8s-master1:6443/api/v1/nodes" | jq` </br>
![](https://github.com/murzinvit/screen_1/blob/e80cda666421d88e0d6e55853094a396adf3e1bb/Kuber_user_token_forbidden.jpg) </br>


#### Создание serviceaccount с правами админа: </br>
`kubectl -n kube-system create serviceaccount netology-admin`</br>
`kubectl -n kube-system create clusterrolebinding netology-admin-bind --clusterrole=cluster-admin --serviceaccount=kube-system:netology-admin`</br>
`export ADMNETTOKEN=$(kubectl -n kube-system get serviceaccount/netology-admin -o jsonpath='{.secrets[0].name}')`</br>
`export ADMTOKEN=$(kubectl -n kube-system get secret $ADMNETTOKEN -o jsonpath='{.data.token}' | base64 --decode)` </br>
`curl -k -H "Authorization: Bearer $ADMTOKEN" -X GET "https://k8s-master1:6443/api/v1/nodes" | jq` </br>
![](https://github.com/murzinvit/screen_1/blob/4dda07586a574f51f532bf4010deda6b34f55eeb/Kuber_get_from_adm_account_ok.jpg) </br>

---
work notes: </br>
https://rtfm.co.ua/kubernetes-serviceaccounts-jwt-tokeny-autentifikaciya-i-rbac-avtorizaciya/ </br>
https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/ </br>
https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/ </br>
