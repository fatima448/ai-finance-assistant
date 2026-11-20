```mermaid
flowchart LR
    Client["React Web Application"]

    subgraph Backend["Django Modular Monolith"]
        Security["Session Authentication<br/>CSRF and Owner Authorization"]

        subgraph APIs["REST API Components"]
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
        end

        subgraph Rules["Domain Components"]
            ExpenseRules["Expense Validation<br/>and Predefined Categories"]
            BudgetRules["Budget and Alert Rules"]
            ConsentPolicy["AI Consent Policy"]
            PromptBuilder["Safe Prompt Builder"]
        end

        subgraph Persistence["Infrastructure Components"]
            UserRepo["User and Consent Repository"]
            ExpenseRepo["Expense Repository"]
            BudgetRepo["Budget Repository"]
            CSVParser["CSV Parser and<br/>Row Validator"]
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

    AccountsAPI --> AccountService --> UserRepo
    ExpensesAPI --> ExpenseService
    ExpenseService --> ExpenseRules
    ExpenseService --> ExpenseRepo

    ImportAPI --> ImportService
    ImportService --> CSVParser
    CSVParser -->|"Validated rows"| ExpenseService

    DashboardAPI --> DashboardService
    DashboardService --> ExpenseRepo
    DashboardService --> BudgetRepo
    DashboardService --> BudgetRules

    AIAPI --> AIService
    AIService --> ConsentPolicy --> UserRepo
    AIService -->|"Calculated summary"| DashboardService
    AIService --> PromptBuilder --> LLMAdapter --> LLM

    UserRepo --> DB
    ExpenseRepo --> DB
    BudgetRepo --> DB
```

Figure: Component diagram of the Django modular monolith. Each API component
delegates to a focused application service. Domain components enforce expense,
budget, alert, and consent rules. Repositories provide the only path to
PostgreSQL. The AI Service receives backend-calculated summaries,
checks consent, and uses an adapter to call the external LLM; it does not access
financial repositories directly.
