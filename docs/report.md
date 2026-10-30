# Лабораторная работа №2. Node-RED

## 1. Цель работы

Изучить возможности визуальной среды Node-RED и получить практические навыки создания потоков обработки сообщений, работы с HTTP, MQTT, Dashboard, Telegram, файлами, контекстом и базой данных SQLite.

Для выполнения работы использовался Node-RED, запущенный в Docker-контейнере.

---

## 2. Используемое окружение

* ОС: macOS
* Docker Desktop
* Node-RED: `v5.0.9`
<img width="259" height="559" alt="Снимок экрана 2026-10-31 003257" src="https://github.com/user-attachments/assets/4e00487d-c1ac-4a6a-b3f3-ccc1ab75b2b0" />


* Docker image: `nodered/node-red`
* SQLite для дополнительного задания

Node-RED запускался в Docker с пробросом порта `1880:1880` и подключением каталога:

```text
~/node-red-data:/data
```

Это позволяет сохранять данные Node-RED на компьютере.

---

## 3. Выполненные задания

### 3.1. Inject и Debug

Был создан первый поток:

```text
Inject → Debug
```

С помощью Inject передавалось сообщение с фамилией студента и заданным topic. Debug использовался для просмотра полученного сообщения.

**Flow:** [flow-01-inject-debug.json](../flows/flow-01-inject-debug.json)

![Inject и Debug]<img width="1537" height="868" alt="Снимок экрана 2026-10-31 003522" src="https://github.com/user-attachments/assets/ad511e75-7408-4a19-af46-16138255b7c0" />


---

### 3.2. Function

Создан поток:

```text
Inject → Function → Debug
```

В Function выполнялась обработка числового значения, вычислялась сумма массива и выполнялась проверка значения.

**Flow:** [flow-02-function.json](../flows/flow-02-function.json)

![Function]<img width="1441" height="787" alt="Снимок экрана 2026-10-31 003607" src="https://github.com/user-attachments/assets/27e0b8ee-600e-4e53-a694-28c23043e832" />


---

### 3.3. Switch

Создан поток:

```text
Inject → Function → Switch
                         ├→ Debug 1
                         └→ Debug 2
```

Switch проверяет значение `msg.payload` и направляет сообщение на разные выходы в зависимости от условия.

Использовались условия:

* значение больше 5;
* значение меньше или равно 5.

**Flow:** [flow-03-switch.json](../flows/flow-03-switch.json)

![Switch]<img width="1500" height="700" alt="Снимок экрана 2026-10-31 003747" src="https://github.com/user-attachments/assets/2529667e-0d8d-4750-b7fd-8f8c07a5a249" />


---

### 3.4. Change

Создан поток:

```text
Inject → Change → Debug
```

В Change изменялись свойства сообщения:

* `msg.topic`;
* `msg.timestamp`;
* `msg.payload`.

**Flow:** [flow-04-change.json](../flows/flow-04-change.json)

![Change]<img width="1536" height="628" alt="Снимок экрана 2026-10-31 003809" src="https://github.com/user-attachments/assets/7b96dbc7-bcdf-48bc-bf62-adfa085e8ada" />


---

### 3.5. Template

Создан поток:

```text
Inject → Template → Debug
```

В Inject передавался JSON-объект с информацией о студенте и лабораторной работе.

Template с использованием Mustache формировал новый JSON.

**Flow:** [flow-05-template.json](../flows/flow-05-template.json)

![Template]<img width="1612" height="675" alt="Снимок экрана 2026-10-31 003844" src="https://github.com/user-attachments/assets/7a037062-18f9-4d7d-965a-a7c2f15c90aa" />

---

### 3.6. HTTP Request

Был создан поток:

```text
Inject → HTTP Request → Debug
```

HTTP Request использовался для получения данных от публичного API. Полученный ответ передавался в Debug.

**Flow:** [flow-06-http-request.json](../flows/flow-06-http-request.json)

![HTTP Request]<img width="1495" height="634" alt="Снимок экрана 2026-10-31 003915" src="https://github.com/user-attachments/assets/2d33e367-b1c6-4ca6-8208-2d1adfd433ef" />


---

### 3.7. MQTT

Был создан поток для публикации и получения MQTT-сообщений:

```text
Inject → Function → MQTT Out
                       ↓
                  MQTT broker
                       ↓
                    MQTT In → Debug
```

Использовался публичный MQTT-брокер.

В Function генерировалось случайное числовое значение. Сообщение публиковалось в topic:

```text
student/Mozheiko/lab2/random
```

MQTT In подписывался на тот же topic и получал опубликованные сообщения.

**Flow:** [flow-07-mqtt.json](../flows/flow-07-mqtt.json)

![MQTT]<img width="1643" height="625" alt="Снимок экрана 2026-10-31 010028" src="https://github.com/user-attachments/assets/aa5b11e0-179d-4fa4-96ec-1ed1662ac152" />



---

### 3.8. GET API

Были реализованы GET-эндпоинты:

```text
GET /api/text
GET /api/info
GET /api/items?id=...
```

`/api/text` возвращает текстовый ответ.

`/api/info` возвращает JSON с информацией о студенте и группе.

`/api/items` принимает параметр `id` и возвращает соответствующий объект. Для отсутствующего параметра используется код `400`, для неизвестного объекта — `404`.

Подробное описание API, параметров и ответов приведено в отдельном файле:

**[api.md](api.md)**

**Flow:** [flow-08-endpoints.json](../flows/flow-08-endpoints.json)

#### GET /api/text

![GET text]<img width="564" height="312" alt="Снимок экрана 2026-10-31 004119" src="https://github.com/user-attachments/assets/b5370d0c-6abb-4276-b5b3-3334010e8259" />

#### GET /api/info

![GET info]<img width="518" height="303" alt="Снимок экрана 2026-10-31 004258" src="https://github.com/user-attachments/assets/04e57b17-18be-4a1d-82f7-59596e34d527" />


#### GET /api/items — успешный запрос

![GET items success]<img width="588" height="261" alt="Снимок экрана 2026-10-31 004334" src="https://github.com/user-attachments/assets/a010630a-ebbd-4507-8437-5e1cd5b5bf2f" />


#### GET /api/items — ошибка

![GET items error]<img width="594" height="340" alt="Снимок экрана 2026-10-31 004348" src="https://github.com/user-attachments/assets/35a405ed-43da-4ce1-a1b1-48d79e2fd581" />


---

### 3.9. Dashboard

Был создан Dashboard для отображения температуры.

Схема:

```text
Inject → Function ──→ Gauge
                    └→ Chart
```

Function генерирует случайное значение температуры.

Gauge показывает текущее значение, а Chart отображает историю полученных значений.

Для температуры использовался диапазон от 0 до 40 °C.

**Flow:** [flow-09-dashboard.json](../flows/flow-09-dashboard.json)

#### Dashboard

![Dashboard]<img width="1903" height="692" alt="Снимок экрана 2026-10-31 004751" src="https://github.com/user-attachments/assets/2e22e40d-e7fe-4cfa-9ca1-1190e8bd6d64" />


#### Flow Dashboard

![Dashboard flow]<img width="1255" height="479" alt="Снимок экрана 2026-10-31 004757" src="https://github.com/user-attachments/assets/22518c5d-4d4c-42d3-a274-a500749a05bf" />


---

### 3.10. Telegram

Для работы с Telegram была предусмотрена дополнительная Node-RED-нода Telegram.

Основная схема:

```text
Telegram Receiver
       ↓
    Function
       ↓
Telegram Sender
```

В Function выполнялась обработка команд и обычных текстовых сообщений.

**Flow:** [flow-10-telegram.json](../flows/flow-10-telegram.json)

![Telegram]<img width="1431" height="583" alt="Снимок экрана 2026-10-31 005241" src="https://github.com/user-attachments/assets/d1c1b670-1548-43c7-9c2d-a4f0a7aacd75" />
<img width="732" height="918" alt="Снимок экрана 2026-10-31 005122" src="https://github.com/user-attachments/assets/5a21177c-3aec-463f-b0e2-94fd07040c01" />


---

### 3.11. Работа с файлами

Были созданы два потока.

Запись:

```text
Inject → Function → File Write
```

Чтение:

```text
Inject → File Read → Debug
```

В файл записывалась информация о студенте, лабораторной работе и времени выполнения.

Файл сохранялся в каталоге `/data`, который связан с каталогом Node-RED на компьютере.

**Flow:** [flow-11-files.json](../flows/flow-11-files.json)

![Работа с файлами]<img width="1431" height="583" alt="Снимок экрана 2026-10-31 005241" src="https://github.com/user-attachments/assets/b1b4b67d-c60f-4235-afaa-5085e5920cec" />


---

### 3.12. Flow Context

Для демонстрации контекста был создан счётчик.

Схема:

```text
Inject → Function → Debug
```

В Function использовался `flow context`:

```javascript
let counter = flow.get("counter") || 0;

counter++;

flow.set("counter", counter);
```

При каждом запуске Inject значение счётчика увеличивается.

Таким образом была проверена возможность хранения данных между сообщениями внутри одного flow.

**Flow:** [flow-12-context.json](../flows/flow-12-context.json)

![Flow Context]<img width="1420" height="802" alt="Снимок экрана 2026-10-31 005348" src="https://github.com/user-attachments/assets/5d1bbc32-8b92-47bd-8927-b58498dfe1e9" />



## 4. Освоенные Node-RED nodes

В ходе работы были изучены следующие основные nodes:

* Inject — создание и запуск сообщений;
* Debug — просмотр сообщений;
* Function — выполнение JavaScript-кода;
* Switch — условная маршрутизация сообщений;
* Change — изменение свойств сообщения;
* Template — формирование данных с использованием шаблонов;
* HTTP Request — выполнение HTTP-запросов;
* HTTP In — получение HTTP-запросов;
* HTTP Response — формирование HTTP-ответов;
* MQTT In — получение MQTT-сообщений;
* MQTT Out — отправка MQTT-сообщений;
* Dashboard nodes — отображение данных;
* Telegram nodes — получение и отправка сообщений Telegram;
* File nodes — чтение и запись файлов;
* Context — хранение состояния между сообщениями.

---

## 5. Использование AI

При выполнении лабораторной работы AI использовался как вспомогательный инструмент.

Основные типы запросов:

1. Объяснение назначения Node-RED nodes и их параметров.
2. Помощь в построении схем потоков.
3. Проверка и исправление JavaScript-кода в Function node.
4. Поиск причин ошибок при работе с HTTP, MQTT и SQLite.
5. Помощь с настройкой Docker и Node-RED.
6. Подготовка структуры документации и файлов лабораторной работы.

Примеры использованных запросов:

> Как настроить Function node в Node-RED, чтобы вычислить сумму массива и проверить число через if?

> Как сделать поток Inject → Function → Switch → Debug в Node-RED и разделить сообщения по условию?

> Как настроить MQTT Out и MQTT In через публичный MQTT-брокер?

> Как сделать GET API в Node-RED с параметром id и вернуть 400 или 404?

> Как подключить SQLite к Node-RED и сделать INSERT и SELECT через Inject?

AI использовался для получения объяснений и поиска вариантов реализации, а сами потоки, настройки и результаты проверялись непосредственно в Node-RED.

---

## 6. Результаты

В результате работы были созданы и проверены несколько независимых потоков Node-RED.

Были получены практические навыки:

* создания сообщений;
* обработки сообщений JavaScript-кодом;
* условного разделения потоков;
* работы с внешними HTTP API;
* обмена сообщениями через MQTT;
* создания HTTP API;
* отображения данных в Dashboard;
* работы с файлами;
* хранения состояния в Context;
* работы с SQLite.

Дополнительное задание с SQLite также было выполнено. После перезапуска Node-RED записи в базе данных сохранились.

Все основные потоки сохранены в формате JSON. Для каждого flow в отчёте приведён скриншот, а исходный JSON доступен по ссылке.

---

## 7. Вывод

В ходе лабораторной работы я познакомился с основными возможностями Node-RED и научился собирать из отдельных nodes полноценные потоки обработки данных.

На практике были рассмотрены Inject, Debug, Function, Switch, Change, Template, HTTP Request, HTTP API, MQTT, Dashboard, работа с файлами, Context и SQLite.

Отдельно была проверена работа с постоянным хранением данных. После перезапуска Node-RED записи SQLite не исчезли, поэтому было подтверждено сохранение данных между запусками.

Все созданные потоки сохранены в формате JSON и добавлены в репозиторий вместе со скриншотами и документацией.
