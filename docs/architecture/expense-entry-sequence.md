```mermaid

sequenceDiagram
    actor User
    participant UI as Web UI
    participant API as Django API
    participant Auth as Auth and CSRF
    participant Expense as Expense Service
    participant Dashboard as Dashboard Service
    participant ORM as Django ORM
    participant DB as PostgreSQL

    User->>UI: Enter expense details
    User->>UI: Select Save Expense
    UI->>API: POST /api/expenses/
    API->>Auth: Validate session and CSRF token

    alt Session is missing or expired
        Auth-->>API: Authentication failed
        API-->>UI: 403 Forbidden
        UI-->>User: Request sign in and retain values

    else CSRF validation fails
        Auth-->>API: CSRF validation failed
        API-->>UI: 403 Forbidden
        UI-->>User: Show retry message

    else Session and CSRF are valid
        Auth-->>API: Validation successful
        API->>Expense: Process expense with authenticated owner
        Expense->>Expense: Validate amount, date, and category

        alt Expense data is invalid
            Expense-->>API: Validation errors
            API-->>UI: 400 Bad Request
            UI-->>User: Show errors and retain values

        else Expense data is valid
            Expense->>ORM: Save owner-scoped expense
            ORM->>DB: Insert expense
            DB-->>ORM: Expense stored
            ORM-->>Expense: Created expense

            Expense->>Dashboard: Recalculate totals and alerts
            Dashboard->>ORM: Retrieve user expenses and budgets
            ORM->>DB: Query financial records
            DB-->>ORM: Matching records
            ORM-->>Dashboard: Financial data
            Dashboard-->>Expense: Updated totals and alerts

            Expense-->>API: Created expense and dashboard updates
            API-->>UI: 201 Created
            UI-->>User: Display saved expense and updated dashboard
        end
    end

```

Figure: Sequence diagram for recording an expense. The browser submits an
authenticated, CSRF-protected request. Django validates the session and
expense fields before storing an owner-scoped expense. After a successful save,
the system recalculates dashboard totals, budget status, and alerts. Invalid or
unauthenticated requests do not create an expense and preserve entered values
for retry.
