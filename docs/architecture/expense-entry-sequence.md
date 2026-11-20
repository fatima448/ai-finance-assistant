```mermaid
sequenceDiagram
    actor User
    participant UI as Web UI
    participant API as Django API
    participant Auth as Auth and CSRF
    participant Expense as Expense Service
    participant DB as PostgreSQL
    participant Dashboard as Dashboard Service

    User->>UI: Enter expense details
    User->>UI: Select Save Expense
    UI->>API: POST expense request
    API->>Auth: Validate session and CSRF token

    alt Session is missing or expired
        Auth-->>API: Authentication failed
        API-->>UI: 401 response
        UI-->>User: Request sign in and retain values
    else CSRF validation fails
        Auth-->>API: CSRF failed
        API-->>UI: 403 response
        UI-->>User: Show safe retry message
    else Authenticated request
        Auth->>Expense: Forward request with owner
        Expense->>Expense: Validate amount, date, and category
        alt Expense fields are invalid
            Expense-->>API: Invalid request details
            API-->>UI: 400 response with field errors
            UI-->>User: Show errors and retain values
        else Expense data is valid
            Expense->>DB: Save owner scoped expense
            DB-->>Expense: Stored expense
            Expense->>Dashboard: Recalculate totals and alerts
            Dashboard->>DB: Read expenses and budgets
            DB-->>Dashboard: Matching records
            Dashboard-->>Expense: Updated dashboard state
            Expense-->>API: Created expense
            API-->>UI: 201 response with expense and alerts
            UI-->>User: Show saved expense and dashboard
        end
    end
```

Figure: Sequence diagram for recording an expense. The browser submits an
authenticated, CSRF-protected request. Django validates the session and
expense fields before storing an owner-scoped expense. After a successful save,
the system recalculates dashboard totals, budget status, and alerts. Invalid or
unauthenticated requests do not create an expense and preserve entered values
for retry.
