## Описание связей

| Связь | Тип | Описание |
|---|---|---|
| USERS → CHARACTERS | 1:N | У одного пользователя много персонажей |
| MASTERS → CHARACTERS | 1:N | Один мастер может подтвердить много персонажей |
| USERS → BOOKINGS | 1:N | Один пользователь может оформить много бронирований |
| TABLES → BOOKINGS | 1:N | Один столик может быть забронирован много раз |
| Rooms → BOOKINGS | 1:N | Одна комната может быть забронирована много раз |
| CATEGORIES → DISHES | 1:N | В одной категории много блюд |
| USERS ↔ ACHIEVEMENTS | M:N | Пользователь может получить много достижений |

---

## Вопросы для самопроверки

**1. Чем связь 1:N отличается от M:N? Приведите пример каждой из вашего проекта.**

1:N к примеру как в моей таблице, USERS → CHARACTERS у одного пользователя может быть много персонажей, но у каждого  персонажа только один пользователь.
M:N к примеру как в моей таблице, USERS ↔ ACHIEVEMENTS у одного пользователя может быть много достижений, так и у одного достижения может множество пользователей.

**2. Почему связь M:N нельзя реализовать двумя таблицами? Зачем нужна промежуточная?**

Потому что тогда можно будет хранить только значение у каждого, как в USERS ↔ ACHIEVEMENTS если не сделать промежуточную таблицу то у пользователя может быть только одно достижение, так и у достижение может быть только у одного пользователя. Поэтому промежуточная таблица хранит в себе пары и множество к множеству может работать.

**3. Что будет, если удалить запись, на которую ссылается FK? (Подумайте, мы разберём это на лекции)**

Тогда появятся ошибки, поэтому у *ON DELETE* есть *CASCADE* - который удаляет все что связано с этой записью, *SET NULL* - поставить во всех связанных NULL, и *RESTRICT* - не удаляет если есть связи.

**4. Может ли FK быть NULL? Когда это полезно?**

Да, может. Если NULL то это значит что связь необязательна, к примеру персонажу нужно подтверждение, которое может поставить мастер, а без мастера персонаж и так может существовать.

---

## ERD-диаграмма

```mermaid
erDiagram
    USERS {
        SERIAL id PK
        VARCHAR(255) email
        VARCHAR(255) password_hash
        VARCHAR(100) display_name
        VARCHAR(20) role
        TIMESTAMP created_at
    }

    CHARACTERS {
        SERIAL id PK
        INTEGER user_id FK
        VARCHAR(100) name
        VARCHAR(50) race
        VARCHAR(50) class
        INTEGER level
        JSONB stats
        TEXT backstory
        BOOLEAN is_public
        BOOLEAN is_verified
        INTEGER verified_by FK
        TIMESTAMP created_at
    }

    MASTERS {
        SERIAL id PK
        INTEGER user_id FK
        TEXT bio
        TIMESTAMP created_at
    }

    TABLES {
        SERIAL id PK
        INTEGER number
        INTEGER capacity
        VARCHAR(50) zone
        VARCHAR(100) location
        BOOLEAN is_available
    }

    ROOMS {
        SERIAL id PK
        VARCHAR(100) name
        INTEGER capacity
        TEXT description
        BOOLEAN is_available
    }

    BOOKINGS {
        SERIAL id PK
        INTEGER user_id FK
        DATE booking_date
        TIME start_time
        INTEGER duration_min
        INTEGER guests_count
        INTEGER table_id FK
        INTEGER room_id FK
        VARCHAR(20) status
        TIMESTAMP created_at
    }

    CATEGORIES {
        SERIAL id PK
        VARCHAR(100) name
        VARCHAR(50) type
    }

    DISHES {  
        SERIAL id PK
        VARCHAR(200) name
        TEXT description
        NUMERIC price
        INTEGER category_id FK
        JSONB tags
        BOOLEAN is_available
    }

    ACHIEVEMENTS {
        SERIAL id PK
        VARCHAR(200) name
        TEXT description
        TEXT condition
    }

    NEWS { 
        SERIAL id PK
        VARCHAR(300) title
        TEXT content
        DATE published_at
        TIMESTAMP created_at
    }

    USERS ||--o{ CHARACTERS : "создаёт"
    MASTERS ||--o{ CHARACTERS : "подтверждает"
    USERS ||--o{ BOOKINGS : "оформляет"
    TABLES ||--o{ BOOKINGS : "бронируется через"
    ROOMS ||--o{ BOOKINGS : "бронируется через"
    CATEGORIES ||--o{ DISHES : "содержит"
    USERS ||--o{ ACHIEVEMENTS : "получает"
```