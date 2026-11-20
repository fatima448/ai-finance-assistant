```mermaid
flowchart LR
    User["User Browser"]

    subgraph Render["Render Web Service"]
        Web["React Production Build<br/>Browser Interface"]
        Django["Django and Django REST Framework<br/>Session Authentication and API"]
        Web --> Django
    engit d

    subgraph Neon["Neon Managed PostgreSQL"]
        DB[("Application Database<br/>Users, expenses, budgets,<br/>categories, and AI consent")]
    end

    LLM["External LLM Provider<br/>Advisory explanations only"]

    subgraph GitHub["GitHub"]
        Source["Source Repository"]
        Actions["GitHub Actions<br/>Tests and daily backup job"]
        Secrets["Encrypted Secrets<br/>Database URL, API keys,<br/>backup encryption key"]
    end

    Backup["Restricted Backup Storage<br/>Encrypted PostgreSQL dumps<br/>14 day retention"]

    User -->|"HTTPS"| Web
    Django -->|"TLS connection"| DB
    Django -->|"Consent checked, minimum aggregates"| LLM
    Source --> Actions
    Secrets --> Actions
    Actions -->|"Encrypted daily pg_dump"| DB
    Actions --> Backup
```

Figure: Proposed Release 1 deployment diagram. The React production build and
Django application run together in one Render web service, so browser sessions
remain same-origin. Neon hosts PostgreSQL outside the application filesystem.
The Django AI integration sends only consented, backend-calculated aggregates
to the external LLM. GitHub Actions runs tests and produces encrypted daily
database backups with restricted storage and a 14-day retention target.
