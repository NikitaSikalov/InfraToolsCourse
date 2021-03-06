# Homework Kubernetes

## Описание приложения

В качестве приложения используется простой _nginx_ сервер, передающий в теле ответа (формат ответа `text/plain`) `hostname` и `ip` адреса пода. Дополнительно к этому, согласно условию, он передает полностью тело и загаловки запроса.

Пример:

```shell
curl http://35.228.166.231
< -- Pod info -- >
HOSTNAME: nginx-app-deployment-b79fc6b87-lckfn
IP: 10.56.0.12
< -- Request Info -- >
GET / HTTP/1.1
```

## Описание реализации

Для реализации использовались такие сущности Kubernetes, как `Deployment`, `ConfigMap`, `Service` (`type = LoadBalancer`), `Secret`. Так как чувствительные данные отсутствуют в данном приложении, то работа с ними сэмулирована через отдачу статического файла, в котором как будто расположена чувствительные данные (см подробнее описание `Secret.yaml` и `Deplyment.yaml`).
Чтобы убедиться, что секрет "передался" приложению и приложение может его читать, содержимое секрета можно получить через http get запрос:

```shell
curl http://35.228.166.231/files/secret
Secret could be here!
```

Данный пример создан исключительно для демонстрации работы с секретами в Kubernetes.

Сущность `Namespace`, не использовал в домашнем задании, так как в документации написано, что ее имеет смысл использовать, только если на физическом кластере участвует большое кол-во людей и они разворачивают там много разных приложений. В данном случае это будет overhead.

Разворачвание приложения:

```shell
kubectl apply -f homework/
kubectl get service -w
```

Ждем пока загрузится `LoadBalanser` и далее запросы уже можно пробовать совершать запросы непосредственно по адресу ExternalIP, указав нужный порт.
