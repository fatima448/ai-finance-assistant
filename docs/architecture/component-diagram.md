```mermaid

flowchart LR
    Client["React Web Application"]

    subgraph Backend["Django Modular Monolith"]

        subgraph APIs["REST API Components"]
            Security["API Security<br/>Session Authentication and CSRF"]
            AccountsAPI["Accounts API"]
            ExpensesAPI["Expenses API"]
            ImportAPI["CSV Import API"]
            DashboardAPI["Budget and Dashboard API"]
            AIAPI["AI Assistance API"]
        end

        subgraph Services["Application Components"]
            AccountService["Account Service"]
            ExpenseService["Expense Service"]
            ImportService["CSV Import Service"]
            DashboardService["Budget and Dashboard Service"]
            AIService["AI Service"]
            PromptBuilder["Safe Prompt Builder"]
        end

        subgraph Rules["Domain Components"]
            ExpenseRules["Expense Rules<br/>and Predefined Categories"]
            BudgetRules["Budget and Alert Rules"]
            ConsentPolicy["AI Consent Policy"]
        end

        subgraph Infrastructure["Infrastructure Components"]
            ORM["Django ORM<br/>Owner-Scoped Data Access"]
            CSVParser["CSV Parser"]
            LLMAdapter["External LLM Adapter"]
        end
    end

    DB[("PostgreSQL Database")]
    LLM["External LLM API"]

    Client --> Security

    Security --> AccountsAPI
    Security --> ExpensesAPI
    Security --> ImportAPI
    Security --> DashboardAPI
    Security --> AIAPI

    AccountsAPI --> AccountService
    ExpensesAPI --> ExpenseService
    ImportAPI --> ImportService
    DashboardAPI --> DashboardService
    AIAPI --> AIService

    AccountService --> ORM

    ExpenseService --> ExpenseRules
    ExpenseService --> ORM

    ImportService --> CSVParser
    ImportService --> ExpenseService

    DashboardService --> BudgetRules
    DashboardService --> ORM

    AIService --> ConsentPolicy
    AIService --> ORM
    AIService -->|"Requests financial summary"| DashboardService
    AIService --> PromptBuilder
    AIService --> LLMAdapter

    ORM --> DB
    LLMAdapter --> LLM
```

Figure: Component diagram of the Django modular monolith. Each API component
delegates to a focused application service. Domain components enforce expense,
budget, alert, and consent rules. Repositories provide the only path to
PostgreSQL. The AI Service receives backend-calculated summaries,
checks consent, and uses an adapter to call the external LLM; it does not access
financial repositories directly.
