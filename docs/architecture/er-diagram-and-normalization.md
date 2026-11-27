```mermaid

erDiagram
    USER ||--o{ EXPENSE : records
    CATEGORY ||--o{ EXPENSE : classifies
    USER ||--o{ MONTHLY_BUDGET : sets
    CATEGORY ||--o{ MONTHLY_BUDGET : defines
    USER ||--o| AI_CONSENT : controls

    USER {
        bigint id PK
        string email UK
        string password
        datetime created_at
    }

    CATEGORY {
        bigint id PK
        string name UK
        string code UK
        boolean is_active
    }

    EXPENSE {
        bigint id PK
        bigint user_id FK
        bigint category_id FK
        decimal amount
        date occurred_on
        string description "Nullable"
        datetime created_at
        datetime updated_at
    }

    MONTHLY_BUDGET {
        bigint id PK
        bigint user_id FK
        bigint category_id FK
        date budget_month
        decimal amount
        datetime created_at
        datetime updated_at
    }

    AI_CONSENT {
        bigint id PK
        bigint user_id FK, UK
        boolean is_granted
        datetime granted_at "Nullable"
        datetime withdrawn_at "Nullable"
        string provider
        datetime updated_at
    }
```



## Normalization and constraints

The database schema follows Third Normal Form (3NF). Each table represents a distinct concept, and non-key attributes depend on the table's key. Category names are stored centrally rather than duplicated across expense and budget records.

- `CATEGORY` is a predefined system catalogue for Release 1; users do not
  create custom categories.
- `EXPENSE.amount` and `MONTHLY_BUDGET.amount` use fixed-precision decimals
  and must be greater than zero.
- `MONTHLY_BUDGET` requires a unique constraint on
  `(user_id, category_id, budget_month)` to enforce one monthly category budget
  per user.
- `AI_CONSENT.user_id` is unique, so it stores one current consent state per
  user. The backend must block external AI calls when `is_granted` is false.
- Recommended indexes are `(user_id, occurred_on)` for expense filtering and
  `(user_id, category_id, occurred_on)` for dashboards and budget alerts.
