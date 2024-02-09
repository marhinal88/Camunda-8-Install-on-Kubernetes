
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
```

Устанавливаем helm:






