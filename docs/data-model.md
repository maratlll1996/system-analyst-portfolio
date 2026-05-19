# Data Model

## Основные сущности

## Client

| Поле | Тип | Описание |
|---|---|---|
| id | UUID | Уникальный идентификатор клиента |
| fullName | string | ФИО клиента |
| phone | string | Номер телефона |
| email | string | Email |
| createdAt | datetime | Дата создания |

## CreditApplication

| Поле | Тип | Описание |
|---|---|---|
| id | UUID | Уникальный идентификатор заявки |
| clientId | UUID | Идентификатор клиента |
| amount | decimal | Сумма кредита |
| term | integer | Срок кредита в месяцах |
| status | string | Статус заявки |
| createdAt | datetime | Дата создания заявки |
| updatedAt | datetime | Дата последнего обновления |

## ApplicationStatusHistory

| Поле | Тип | Описание |
|---|---|---|
| id | UUID | Уникальный идентификатор записи |
| applicationId | UUID | Идентификатор заявки |
| oldStatus | string | Предыдущий статус |
| newStatus | string | Новый статус |
| changedAt | datetime | Дата изменения |
| changedBy | string | Источник изменения |

## Связи

- один клиент может иметь несколько заявок;
- одна заявка может иметь несколько записей истории статусов;
- каждая запись истории относится к одной заявке.
