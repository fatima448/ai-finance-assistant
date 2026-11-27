```mermaid

flowchart LR
    User["User Browser"]

    subgraph Render["Render Web Service"]
        Web["React Production Build"]
        Django["Django and Django REST Framework"]
        Env["Environment Variables"]

        Django -->|"Serves frontend"| Web
        Env -.-> Django
    end

    subgraph Neon["Neon Managed PostgreSQL"]
        DB[("Application Database")]
    end

    LLM["External LLM Provider"]

    subgraph GitHub["GitHub"]
        Source["Source Repository"]
        Actions["GitHub Actions"]
        Secrets["GitHub Actions Secrets"]

        Source --> Actions
        Secrets -.-> Actions
    end

    Backup["Restricted Backup Storage<br/>Encrypted Database Backups<br/>14-Day Retention"]

    User -->|"HTTPS - Web and API requests"| Django
    Django -->|"TLS"| DB
    Django -->|"HTTPS - Consented aggregated data"| LLM

    Source -->|"Application deployment"| Render
    Actions -->|"Run automated tests"| Source
    Actions -->|"Daily pg_dump over TLS"| DB
    Actions -->|"Encrypt and store backup"| Backup

```

Figure: Proposed Release 1 deployment diagram. The React production build and
Django application run together in one Render web service, so browser sessions
remain same-origin. Neon hosts PostgreSQL outside the application filesystem.
The Django AI integration sends only consented, backend-calculated aggregates
to the external LLM. GitHub Actions runs tests and produces encrypted daily
database backups with restricted storage and a 14-day retention target.
