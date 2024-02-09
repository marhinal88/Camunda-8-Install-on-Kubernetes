
# Установка Camunda 8 на кластер Kubernetes под CentOS 9

Пошаговая инструкция по созданию отказоустойчивой продакшн-среды Camunda 8 на кластере Kubernetes, работающем на CentOS 9.

## Установка
Берем в расчет то, что кластер Kubernetes из трех воркеров и одного мастера уже создан и настроен, установлен CNI все ноды в статусе Ready. Все действия выполняются на мастере.

Устанавливаем Snap из репозитория EPEL, он понадобится нам для установки Helm:

```bash
sudo yum install epel-release
```
```bash
sudo yum install snapd
```
Включаем сервис, создаем симлинк:
```bash
sudo systemctl enable --now snapd.socket
```
```bash
sudo ln -s /var/lib/snapd/snap /snap
````

При помощи Snap устанавливаем helm:
```bash
snap install helm --classic
````
```bash
helm repo add camunda https://helm.camunda.io
````
```bash
helm repo update
````

Подготавливаем PersistentVolume (PV) в режиме local-storage:
```bash
kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/master/deploy/local-path-storage.yaml
````
```bash
kubectl patch storageclass local-path -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```
Проверяем, что storage class по умолчанию теперь local:
```bash
kubectl get storageclass
````

Устанавливаем ingress - nginx контроллер для кубернетеса c помощью helm:
```bash
helm upgrade --install ingress-nginx ingress-nginx   --repo https://kubernetes.github.io/ingress-nginx   --namespace default
````

Ну и теперь наконец переходим к установке пакета Camunda 8. Для установки используем конфиг ingress.yaml, он выложен в этом же репозитории. В нем прописываем URL компонентов Camunda, в том виде, в котором они потом будут открываться из браузера, а также указываем количество реплик zeebe, в зависимости от количества нод в кластере кубера, по одной реплике на каждую ноду.

С помощью helm устанавливаем основной пакет Camunda 8
```bash
helm install camunda-platform camunda/camunda-platform -f ingress.yaml
````







