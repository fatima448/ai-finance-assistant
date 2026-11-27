```mermaid

flowchart TB
    subgraph Presentation["Presentation Layer"]
        UI["React Web Application"]
    end


        subgraph Interface["Interface Layer"]
            API["Django REST API"]
            Security["Session Authentication and CSRF"]
        end

        subgraph Application["Application Layer"]
            Accounts["Account Service"]
            Expenses["Expense Service"]
            Import["CSV Import Service"]
            Dashboard["Budget and Dashboard Service"]
            AI["AI Service"]
        end

        subgraph Domain["Domain Layer"]
            ExpenseRules["Expense Rules"]
            BudgetRules["Budget and Alert Rules"]
            ConsentRules["AI Consent Policy"]
        end

        subgraph Infrastructure["Infrastructure Layer"]
            ORM["Django ORM"]
            CSV["CSV Processing"]
            LLMAdapter["External LLM Adapter"]
        end

    DB[("PostgreSQL Database")]
    LLM["External LLM API"]

    UI --> Interface
    Interface --> Application
    Application --> Domain
    Application --> Infrastructure

    ORM --> DB
    LLMAdapter --> LLM


```

Figure: Layered architecture of the AI Personal Finance Analysis and Assistance System. The system follows a layered client-server architecture implemented as a Django modular monolith. The React web application sends HTTPS requests to the Django REST API, where session authentication, CSRF protection, and owner authorization are applied before requests reach application services. The Accounts, Expenses, Budget and Dashboard, and AI services implement the main business functions. Domain rules validate expenses, enforce predefined categories, calculate budgets and alerts, and control AI-data-sharing consent. Infrastructure components provide CSV validation, database access through Django ORM repositories, PostgreSQL storage, and controlled communication with the external LLM API. The AI service receives only consented, backend-calculated aggregate data and produces explanations; it cannot create, update, or delete financial records.
