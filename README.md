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

Получить значения переменных

```
K8S=https://$KUBERNETES_SERVICE_HOST:$KUBERNETES_SERVICE_PORT
SADIR=/var/run/secrets/kubernetes.io/serviceaccount
TOKEN=$(cat $SADIR/token)
CACERT=$SADIR/ca.crt
NAMESPACE=$(cat $SADIR/namespace)
```

Подключаемся к API

```
curl -H "Authorization: Bearer $TOKEN" --cacert $CACERT $K8S/api/v1/
```

В случае с minikube может быть другой адрес и порт, который можно взять здесь

```
cat ~/.kube/config
```

или здесь

```
kubectl cluster-info
```

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

В качестве решения прикрепите к ДЗ конфиг файлы для деплоя. Прикрепите скриншоты вывода команды kubectl со списком запущенных объектов каждого типа (pods, deployments, serviceaccounts) или скриншот из самого Kubernetes, что сервисы подняты и работают, а также вывод из CLI.
