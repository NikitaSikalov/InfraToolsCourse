## Docker

Написать систему на docker-compose и (3 dockerfiles), реализующие следующий набор задач.

**Сервис-1**. База данных (какая именно - не имеет значения). Сервис запускается, ожидает входящих соединений на своем порту. можно использовать готовый контейнер из https://hub.docker.com/.

**Сервис-2**. Контейнер со скриптом для наполнения базы. Сервис запускается, подключается по порту к базе данных (можно с помощью нативного клиента к базе) и заполняет базу набором данных. Набор данных представлен CSV-файлом из 5 строк. В каждой строке 2 поля: текст и число (данные могут быть любые). Сервис-2 стартует после старта сервиса-1 и завершается (убивается контейнер) после выполнения.

**Сервис-3**. Контейнер с демоном отдающим из базы эти данные по http-запросу. Демон умеет принимать http запрос, соединяется с контейнером базы через порт базы, делает запрос, получает ответ, отдает данные в http ответе. Для реализации REST API по умолчанию можно использовать flask. В README или в комментариях в Dockerfile нужно указать, по какому порту должен подключиться пользователь чтоб получить данные.

### Критерии оценивания
Домашка оценивается в 2 балла. Баллы (даже частичные выставляются только в том случае если система работает и отдает данные).

#### Сервис-1. 1/3 балла
|Feature|Оценка|
|---|---|
|Данные заполняются сразу при старте БД, без участия сервиса-2 |-100%|

#### Сервис-2. 2/3 балла
|Feature|Оценка|
|---|---|
|Проверка заполнения данных. Например, простой SELECT к базе и вывод в stdout лога о том, что данные заполнены.| +30%|
|Монтирование файла с данными в качестве VOLUME, а не просто добавление файла в контейнер через ADD|+30%|
|Контейнер не удаляется после того, как отработал| -50%|
|Данные забиты хардкодом в контейнере и изменить их, не меняя Dockerfile, невозможно|-100%|

*При оценивании штрафы и доп. баллы выставляются независимо. Например, если не удаляется и не стопается контейнер, но при этом есть VOLUME, то будет -50% + 30% = -20%*.

#### Сервис-3. 1 балл
|Feature|Оценка|
|---|---|
|REST API из двух элементов: (1) `GET /` возвращает данные в виде JSON или plain text, (2) `GET /health` возвращает `200`. Все остальные запросы к сервису возвращают 404 или какое-нибудь custom-сообщение (не дефолтную ошибку) | +20%|
|Вместо вывода данных по REST, реализован вывод данных в stdout|-50%|

Использование MacVLAN (см. лекцию Анатолия Жестова): +70% **ко всем сервисам**.

### Как сдать домашку
1. Сдача происходит точно также, как и в предыдущей ДЗ. Вы заливаете код в ветку (не мастер) репозитория `***-hw2` в gitlab.atp-fivt.org и делаете merge request с default-параметрами. Assignee ставить не нужно.
2. Проверяющий комментирует код, а вы делаете новые коммиты с правками, а также отвечаете на комментарии.
3. В конце проверки проверяющий делает merge.

### Как будет происходить проверка
1. В качестве файла для заполнения БД преподаватель подкладывает свой файл.
2. Далее на сервере ваша система. При отсутствии инструкции она будет запускаться командой `docker-compose up`.
3. Проверяется что по GET-запросу выаются загруженные данные.