# API Contract

## 1. Create Credit Application

Метод используется для создания кредитной заявки клиента.

---

## Request

**Method:** `POST`  
**Endpoint:** `/api/v1/applications`  
**Content-Type:** `application/json`  
**Authorization:** `Bearer Token`

### Request Body

```json
{
  "clientId": "12345",
  "amount": 500000,
  "term": 24,
  "productType": "consumer_credit"
}
```

### Request Fields

| Field | Type | Required | Description |
|---|---|---|---|
| clientId | string | yes | Уникальный идентификатор клиента |
| amount | number | yes | Сумма кредита |
| term | number | yes | Срок кредита в месяцах |
| productType | string | yes | Тип кредитного продукта |

---

## Success Response

**Status Code:** `201 Created`

```json
{
  "applicationId": "app-001",
  "status": "created",
  "createdAt": "2026-05-17T10:00:00Z"
}
```

### Response Fields

| Field | Type | Description |
|---|---|---|
| applicationId | string | Уникальный идентификатор заявки |
| status | string | Текущий статус заявки |
| createdAt | string | Дата и время создания заявки |

---

## Error Responses

| Status Code | Error | Description |
|---|---|---|
| 400 | Bad Request | Некорректные или неполные данные |
| 401 | Unauthorized | Пользователь не авторизован |
| 404 | Not Found | Клиент не найден |
| 500 | Internal Server Error | Внутренняя ошибка сервера |

---

## Business Rules

BR-1  
Поле `amount` должно быть больше 0.

BR-2  
Поле `term` должно быть от 3 до 84 месяцев.

BR-3  
Если обязательные поля не заполнены, система должна вернуть ошибку `400 Bad Request`.

BR-4  
После успешного создания заявки система должна присвоить ей статус `created`.
