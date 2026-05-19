# API Contract

## Сервис

Credit Application Service

## Назначение API

API предназначено для создания кредитной заявки, получения информации о заявке и проверки ее текущего статуса.

Контракт описывает:
- доступные методы API;
- формат запросов и ответов;
- обязательные и необязательные поля;
- коды успешных и ошибочных ответов;
- правила авторизации;
- базовые требования к версионированию.

---

# Общая информация

## Base URL

```text
https://api.example-bank.ru/api/v1
```

## Формат данных

Все запросы и ответы передаются в формате JSON.

```http
Content-Type: application/json
```

## Авторизация

Для доступа к API используется JWT-токен.

```http
Authorization: Bearer <access_token>
```

Если токен отсутствует или невалиден, система возвращает ошибку `401 Unauthorized`.

---

# Версионирование API

Текущая версия API:

```text
v1
```

Версия указывается в URL:

```text
/api/v1/applications
```

При изменениях, нарушающих обратную совместимость, должна быть создана новая версия API, например:

```text
/api/v2/applications
```

---

# Endpoints

## 1. Создание кредитной заявки

### Method

```http
POST /applications
```

### Description

Метод создает новую кредитную заявку клиента и запускает процесс скоринговой проверки.

---

## Request Body

```json
{
  "clientId": "client-12345",
  "amount": 500000,
  "term": 24,
  "productType": "consumer_credit"
}
```

---

## Request fields

| Field | Type | Required | Description |
|---|---|---|---|
| clientId | string | yes | Уникальный идентификатор клиента |
| amount | number | yes | Сумма кредита |
| term | integer | yes | Срок кредита в месяцах |
| productType | string | yes | Тип кредитного продукта |

---

## Validation rules

| Field | Rule |
|---|---|
| clientId | Не должен быть пустым |
| amount | Должен быть больше 0 |
| term | Должен быть больше 0 |
| productType | Допустимые значения: `consumer_credit`, `credit_card`, `mortgage` |

---

## Success Response

### Status code

```http
201 Created
```

### Response Body

```json
{
  "applicationId": "app-001",
  "clientId": "client-12345",
  "status": "created",
  "createdAt": "2026-05-19T10:30:00Z"
}
```

---

## Response fields

| Field | Type | Description |
|---|---|---|
| applicationId | string | Уникальный идентификатор заявки |
| clientId | string | Уникальный идентификатор клиента |
| status | string | Текущий статус заявки |
| createdAt | string | Дата и время создания заявки |

---

## Error responses

| Status Code | Error | Description |
|---|---|---|
| 400 | Bad Request | Некорректные данные запроса |
| 401 | Unauthorized | Пользователь не авторизован |
| 409 | Conflict | Заявка с такими параметрами уже существует |
| 500 | Internal Server Error | Внутренняя ошибка сервера |

### Example: 400 Bad Request

```json
{
  "errorCode": "VALIDATION_ERROR",
  "message": "Field amount must be greater than 0",
  "details": [
    {
      "field": "amount",
      "reason": "Invalid value"
    }
  ]
}
```

---

# 2. Получение информации о заявке

## Method

```http
GET /applications/{applicationId}
```

## Description

Метод возвращает информацию о кредитной заявке по ее идентификатору.

---

## Path parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| applicationId | string | yes | Уникальный идентификатор заявки |

---

## Success Response

### Status code

```http
200 OK
```

### Response Body

```json
{
  "applicationId": "app-001",
  "clientId": "client-12345",
  "amount": 500000,
  "term": 24,
  "productType": "consumer_credit",
  "status": "approved",
  "createdAt": "2026-05-19T10:30:00Z",
  "updatedAt": "2026-05-19T10:32:00Z"
}
```

---

## Response fields

| Field | Type | Description |
|---|---|---|
| applicationId | string | Уникальный идентификатор заявки |
| clientId | string | Уникальный идентификатор клиента |
| amount | number | Сумма кредита |
| term | integer | Срок кредита в месяцах |
| productType | string | Тип кредитного продукта |
| status | string | Текущий статус заявки |
| createdAt | string | Дата и время создания заявки |
| updatedAt | string | Дата и время последнего обновления заявки |

---

## Error responses

| Status Code | Error | Description |
|---|---|---|
| 401 | Unauthorized | Пользователь не авторизован |
| 403 | Forbidden | Нет доступа к заявке |
| 404 | Not Found | Заявка не найдена |
| 500 | Internal Server Error | Внутренняя ошибка сервера |

---

# 3. Получение статуса заявки

## Method

```http
GET /applications/{applicationId}/status
```

## Description

Метод возвращает текущий статус кредитной заявки.

---

## Path parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| applicationId | string | yes | Уникальный идентификатор заявки |

---

## Success Response

### Status code

```http
200 OK
```

### Response Body

```json
{
  "applicationId": "app-001",
  "status": "approved",
  "updatedAt": "2026-05-19T10:32:00Z"
}
```

---

## Status values

| Status | Description |
|---|---|
| created | Заявка создана |
| scoring_pending | Заявка ожидает скоринговой проверки |
| scoring_in_progress | Заявка находится на скоринговой проверке |
| approved | Заявка одобрена |
| rejected | Заявка отклонена |
| canceled | Заявка отменена |

---

# 4. Отмена заявки

## Method

```http
PATCH /applications/{applicationId}/cancel
```

## Description

Метод отменяет кредитную заявку, если она еще не была обработана окончательно.

---

## Path parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| applicationId | string | yes | Уникальный идентификатор заявки |

---

## Request Body

```json
{
  "reason": "client_request"
}
```

---

## Request fields

| Field | Type | Required | Description |
|---|---|---|---|
| reason | string | yes | Причина отмены заявки |

---

## Success Response

### Status code

```http
200 OK
```

### Response Body

```json
{
  "applicationId": "app-001",
  "status": "canceled",
  "updatedAt": "2026-05-19T10:40:00Z"
}
```

---

## Business rules

- заявку можно отменить только в статусах `created`, `scoring_pending`, `scoring_in_progress`;
- заявку нельзя отменить, если она уже находится в статусе `approved` или `rejected`.

---

# Общая модель ошибки

Для всех методов используется единый формат ошибки.

```json
{
  "errorCode": "ERROR_CODE",
  "message": "Описание ошибки",
  "details": [
    {
      "field": "fieldName",
      "reason": "Описание причины ошибки"
    }
  ]
}
```

---

# Error codes

| Error Code | Description |
|---|---|
| VALIDATION_ERROR | Ошибка валидации данных |
| UNAUTHORIZED | Пользователь не авторизован |
| FORBIDDEN | Недостаточно прав |
| APPLICATION_NOT_FOUND | Заявка не найдена |
| APPLICATION_ALREADY_EXISTS | Заявка уже существует |
| INTERNAL_ERROR | Внутренняя ошибка сервера |

---

# Интеграции

## Синхронное взаимодействие

REST API используется для синхронного взаимодействия между клиентским приложением и сервисом кредитных заявок.

Пример:

```text
Client Application → Credit Application Service
```

Клиент отправляет запрос и ожидает ответ от сервиса.

---

## Асинхронное взаимодействие

RabbitMQ используется для асинхронной передачи событий об изменении статуса заявки.

Пример:

```text
Credit Application Service → RabbitMQ → Notification Service
Credit Application Service → RabbitMQ → Analytics Service
```

После изменения статуса заявки сервис публикует событие, а другие сервисы обрабатывают его независимо.

---

# Требования к контракту

API-контракт должен:
- быть единым источником правды для аналитиков, разработчиков и тестировщиков;
- поддерживаться в актуальном состоянии;
- содержать описание всех методов, параметров и ошибок;
- включать примеры запросов и ответов;
- учитывать версионирование API;
- описывать авторизацию;
- быть пригодным для переноса в Swagger/OpenAPI.

---

# Notes for Development Team

- Все даты передаются в формате ISO 8601.
- Все идентификаторы передаются как строки.
- Все суммы передаются в рублях.
- Все обязательные поля должны валидироваться на backend.
- При изменении статуса заявки должно публиковаться событие в RabbitMQ.
- Ошибки должны возвращаться в едином формате.
