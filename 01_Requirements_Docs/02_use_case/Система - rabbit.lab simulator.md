# Система: «rabbit.lab» — интерактивный конструктор RabbitMQ (Simulator-standalone)

**Границы системы (область):** веб-приложение симулятора, работающее в браузере без регистрации. Внутри системы: холст топологии (producers, exchanges, queues, bindings, consumers), движок симуляции доставки сообщений (routing по типам exchange, TTL, nack/DLX, redelivered), метрики и журнал событий, проверки заданий, аудит топологии, сохранение состояния в localStorage, экспорт артефактов.
**За границей системы:** реальный брокер RabbitMQ (симулятор его эмулирует; подключение к живому брокеру — вне области), учебные материалы курса (страницы course/architecture/operations — вне области UC), сервер (GitHub Pages — статическое хостинг).

**Акторы системы (2):**
- **Студент (Student)** — человек, изучающий маршрутизацию RabbitMQ; первичный актор почти всех сценариев.
- **Локальное хранилище браузера (localStorage)** — системный актор: долговременное хранение профиля (`rl_user`) и состояний песочницы между сессиями.

## Actor–Goal List (точность 1)

| # | Актор | Цель | Уровень |
|---|---|---|---|
| 1 | Студент | Пройти учебный сценарий (direct/fanout/topic/DLQ/retry/headers) | Primary task |
| 2 | Студент | Опубликовать сообщение и увидеть маршрутизацию | Primary task |
| 3 | Студент | Подтвердить доставку / отклонить (nack) сообщение | Primary task |
| 4 | Студент | Собрать свою топологию в свободном режиме | Primary task |
| 5 | Студент | Проверить топологию аудитом | Supporting |
| 6 | Студент | Следить за потоком сообщений и метриками | Supporting |
| 7 | Студент | Сохранить и восстановить состояние песочницы | Supporting |
| 8 | Студент | Экспортировать топологию (definitions.json и др.) | Supporting |
| 9 | Студент | Зарегистрировать профиль | Supporting |

## Наборы Use Case

**Набор A — учебные сценарии (guided):** UC-01 Select Learning Scenario, UC-02 Publish Message, UC-03 Acknowledge Message Delivery (общие для всех 6 guided-топологий: direct 01, fanout 02, topic 03, dlq 04, retry 05, headers 06 — различия только в данных биндингов, т.е. в Variation UC-01).
**Набор B — конструктор (free):** UC-04 Add Topology Element, UC-05 Audit Topology.
**Набор C — состояние и артефакты:** UC-06 Restore Sandbox State, UC-07 Save Sandbox State, UC-08 Export Topology, UC-09 Register Profile.
**Набор D — наблюдение:** UC-10 Monitor Message Flow.

## СВЯЗИ МЕЖДУ USE CASE (матрица)

| Базовый UC | Связанный UC | Тип | Точка срабатывания |
|---|---|---|---|
| Select Learning Scenario (01) | Restore Sandbox State (06) | extend | 1a — при наличии сохранённого состояния сценария |
| Publish Message (02) | Acknowledge Message Delivery (03) | extend | 6a — доставка потребителю с autoAck=false |
| Publish Message (02) | Add Topology Element (04) | extend | 3a — producer не создан |
| Add Topology Element (04) | Audit Topology (05) | extend | 5a — автопроверка после изменения схемы |
| Monitor Message Flow (10) | Save Sandbox State (07) | extend | шаг 1 — при включённом авто-сохранении |

Вложенность include/extend — не более 1 уровня.

Диаграмма системы: [`Диаграмма Use Case системы.png`](Диаграмма%20Use%20Case%20системы.png)
