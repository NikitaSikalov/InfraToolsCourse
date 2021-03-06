# Kubernetes

##  Основная часть

В домашнем задании, необходимо запустить приложение в Kubernetes. Приложение которое запускаем, может быть любое. Например, курсовая с другой дисциплины или дипломный проект. Нужно использовать следующие сущности Kubernetes:

* Namespace
* Deployment
* Service
* ConfigMap
* Secret

Оценка за эту часть **4 балла**.

### Каким может быть приложение
Приложение можно писать на любом языке, если приложения нет, можете посмотреть базовые примеры на Golang здесь https://habr.com/ru/post/345332/

Описание что должно делать приложение:
+ Приложение слушает 8444 порт(нам так проще будет тестировать просто в дальнейшем)
  + при заходе туда браузером пишет на странице все headers, body, request с которыми обратился клиент, например:
    + hostname в котором запущено приложение и IP address пода в котором запустилось

+ вы можете также взять за основу любое чужое приложение которое так может делать, или часть функционала
  + ~google_containers/echoserver~ кода нет, но есть image
  + https://github.com/watson/http-echo-server
  + https://gist.github.com/solusipse/6419144
  + https://gist.github.com/sshankar/1328413/6e5cdf183d49dbd384d166b78af651f535f8ddc1
  + https://docs.oracle.com/javase/tutorial/networking/sockets/examples/EchoServer.java
  + https://gist.github.com/paulsmith/775764

### Что должно быть в в ветке репозитория

1. yaml файлы которые описывают сущности выше.
2. Коды проекта для того чтоб проверяющие смогли восстановить работоспособность сервиса.

### Как будет проверяться приложение

1. После `kubectl apply -f <folder>/` создаются все сущности и приложение не падает.
2. Размер образа приложения не должен быть больше 300MiB
3. Приложение должно слушать порт 8444
4. Приложение должно быть доступно через порт 8444 через сервис

## Дополнительные баллы

Некоторые вещи которые описаны дальше, требует существенных ресурсов на реализацию. Если сделали что-нибудь из этой части, напишите в README какие пункты сделали. Это ускориит проверку.

|Задание|Описание|Оценка|
|---|---|---|
|Readiness, Liviness пробы|статус пода отображается State: Running, Ready: True|0,2|
|horizontalpodautoscaler| на get по ресурсу horizontalpodautoscaler отвечает политикой|0,2|
|podDisruptionBudget|на get по ресурсу podDisruptionBudget отвечает политикой|0,2|
|podSecurityPolicy|контейнер настроен на работу от nobody, энфорсятся любые политики|0,4|
|open-policy-agent|то же самое что и предыдущий|0,4|
|scrape metrics endpoint| настроен в приложении интерфейс `/metrics` в котором отображаются метрики приложения|0,4|
|business metrics endpoint| настроен в приложении интерфейс который может показывать метрики как ендпойнт `/appmetrics` так и возможность писать метрики в любую бд|0,4|
|database poller| приложение после запуска опрашивает базу, если база развернута в конфугурации primary-replic-replic, приложение должно принять топологию базы, и писать в primary, а чтения производить с replic, проверка -- код, настройка драйвера\поллера|1,6|
|circuit breaker | приложение должно в случае не ответа другого приложения, сделать retry запроса в этот же ендпойнт ещё раз, до тех пор пока время на запрос целиком не истечёт|1,2|
|tracing|приложение должно уметь писать метрики в системы сбора трейсинга, например jaeger|0,6|
|logs main| всё что не ошибки в stdout, все ошибки, фаталы, ворнинги в sterr|0,4|
|logs format|приложение должно писать логи, хотя бы в формате "origin ip 2020-12-18T22:34:49+00:00 http-method status-code time-spent-on-request URI"[см. полезные ссылки](## Полезные ссылки)|0,4|
|graceful shutdown| приложение при downscale/перезапуске/апдейте правильно выключается, при получении `SIGTERM` перестаёт принимать новые запросы, отрабатывает все оставшиеся запросы и завершает работу показывая exit-code 0|0,8|

## Полезные ссылки

* https://www.masterzen.fr/2013/01/13/the-10-commandments-of-logging/#2-thou-shalt-log-at-the-proper-level
* https://12factor.net/ru/
* https://habr.com/ru/post/345332/
* https://habr.com/ru/post/441842/
* https://habr.com/ru/post/337158/
* https://habr.com/ru/company/southbridge/blog/513886/

## Техническая информация

Для того чтобы сделать ДЗ, необходим Kubernetes(k8s)

Есть множество вариантов получить Kubernetes(k8s), я рассмотрю популярные

Если у вас есть компьютер на котором есть 8+GB RAM, то можно рассмотреть следующие варианты:

+ [minikube](https://kubernetes.io/ru/docs/tasks/tools/install-minikube)
+ [microk8s](https://microk8s.io)
+ [k3s](https://k3s.io/)

### Kubernetes в инфраструктуре Яндекса
* 1 CPU, 2GB RAM на человека

Как подключиться:
```
export KUBECONFIG=~/path/to/kubeconfig
kubectl config view
kubectl config current-context
kubectl config use-context NAME
```

Как проверить что сервис жив:
```
curl -k https://84.201.144.34:14443/healthz
```
