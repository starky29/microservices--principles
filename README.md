# Микросервисы: принципы

## Задача 1: API Gateway 

Предложите решение для обеспечения реализации API Gateway. Составьте сравнительную таблицу возможностей различных программных решений. На основе таблицы сделайте выбор решения.

Решение должно соответствовать следующим требованиям:
- маршрутизация запросов к нужному сервису на основе конфигурации,
- возможность проверки аутентификационной информации в запросах,
- обеспечение терминации HTTPS.

Обоснуйте свой выбор.

### Решение
Возьмём API Gateway, походящие под условия и чаще всего упоминаемые во всевозможных топах: Kong Gateway, Apache APISIX, Tyk, KrakenD, Amazon API Gateway.


| Критерий | Kong Gateway | Apache APISIX | Tyk | KrakenD | Amazon API Gateway |
|----------|--------------|---------------|-----|---------|-----------|
| Цена | Есть Open Source и платный Enterprise | Open Source (есть платный облачный API7) | Есть Open Source и платные тарифы в облаке | Есть Community Edition и Enterprise Edition | Есть free tier с ограничением по количеству запросов. |
| Размещение | SaaS (Kong Konnect) и размещение на хосте, в том числе с использованием контейнеров. | Размещение на хосте, в том числе с использованием контейнеров. Есть SaaS API7. | Размещение на хосте, в том числе с использованием контейнеров. Есть SaaS. | Размещение на хосте, в том числе с использованием контейнеров, в облаке. | Облако |
| Производительсность | Хвалятся производительностью | Хвалятся, что они производительнее Kong | Хвалятся, что они производительнее Kong | Быстрый и нетребовательный | Хвалится производительностью |
| Расширения | Kong Plugin Hub | Apache APISIX®️ Plugin Hub | Можно создавать свои плагины, можно найти готовые на GitHub | Можно создавать свои плагины | Есть расширения |
| Аутентификация | Basic, Key, OAuth 2.0, LDAP, OpenID Connect, HMAC, JWT | Key, HMAC, OpenID Connect, LDAP , Basic, Wolf RBAC | Basic, Bearer Token, OAuth 2.0, HMAC, JWT, OpenID Connect | Basic, JWT, OAuth 2.0, mTLS, Google Cloud, NTLM | OAuth 2.0, Basic, JWT  |
| Управление трафиком | Timeout, Retry, Circuit Breaker, Rate Limit | Rate Limit, Circuit Breaker, Retry, Timeout | Rate Limit, Circuit Breaker, Timeout, Retry | Rate Limit, Circuit Breaker, Timeout, Retry, Bot detector | Timeout, Retry, Circuit Breaker, Rate Limit |
| Мониторинг | Built-in, Prometheus, Datadog, StatsD | Prometheus | Built-in | OpenTelemetry, Prometheus, InfluxDB, Datadog, AWS X-Ray, Azure Monitor и др. | Amazon CloudWatch Alarms, Amazon CloudWatch Logs, Amazon EventBridge, AWS CloudTrail Log Monitoring |

Т.к. практически все решения предоставляют сравнение с Kong, это наиболее распространённое решение. Kong имеет развитую документацию и большое комьюнити, что является плюсом на этапе внедрения.

Так же можно попробовать KrakenD, как достаточно быстрое и нетребовательное решение.

## Задача 2: Брокер сообщений

Составьте таблицу возможностей различных брокеров сообщений. На основе таблицы сделайте обоснованный выбор решения.

Решение должно соответствовать следующим требованиям:
- поддержка кластеризации для обеспечения надёжности,
- хранение сообщений на диске в процессе доставки,
- высокая скорость работы,
- поддержка различных форматов сообщений,
- разделение прав доступа к различным потокам сообщений,
- простота эксплуатации.

Обоснуйте свой выбор.

### Решение

Рассмотрим основные брокеры сообщений, которые обычно фигурируют в списках: RabbitMQ, Kafka, ActiveMQ, Redis, Amazon SNS/SQS, Apache Pulsar, NATS. 

Критериев много, так что поэтапно отсеем выбранные решения.

* Поддержка кластеризации. <br/>
RabbitMQ, Kafka, ActiveMQ, Redis, Apache Pulsar, NATS.<br/>
Amazon SNS/SQS не сильно документирован, поэтому он не подходит.
* Хранение сообщений на диске в процессе доставки.<br/>
RabbitMQ (с 3.10.0), Kafka, ActiveMQ, Redis (при fsync every write), Apache Pulsar (при соответствующей настройке), NATS (при использовании с JetStream).
* Высокая скорость работы.<br/>
Высокую скорость отмечают у NATS, Kafka. Apache Pulsar, RabbitMQ и ActiveMQ при сравнении оказываются более медленными, чем конкуренты.
* Поддержка разных форматов сообщений.<br/>
По условию подходят RabbitMQ (AMQP 0-9-1, AMQP 1.0, RabbitMQ Stream Protocol, MQTT, STOMP), ActiveMQ (AMQP, AUTO, MQTT, OpenWire, REST, RSS and Atom, Stomp, WSIF, WS Notification, XMPP), 
Здесь мы отсекаем Kafka (использует Kafka Protocol), Redis (использует RESP), Apache Pulsar (с одноимённым протоколом), NATS (NATS Protocol).
* Разделение прав доступа к различным потокам сообщений.<br/>
У RabbitMQ есть права пользователей, у ActiveMQ есть роли.
* Простота эксплуатации.<br/>
Вроде бы никто не называл сложным RabbitMQ или ActiveMQ (как будто все шишки сыпятся в основном на Kafka).

Итого, остаются только двое – RabbitMQ и ActiveMQ. При сравнении RabbitMQ назвают решением проще и упоминается чаще , чем ActiveMQ .