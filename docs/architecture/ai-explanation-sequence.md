```mermaid
sequenceDiagram
    actor User
    participant UI as Web UI
    participant API as Django API
    participant Auth as Auth and CSRF
    participant AI as AI Service
    participant ORM as Django ORM
    participant Consent as Consent Policy
    participant Dashboard as Dashboard Service
    participant Prompt as Prompt Builder
    participant Adapter as LLM Adapter
    participant LLM as External LLM

    User->>UI: Request monthly explanation
    UI->>API: Send explanation request
    API->>Auth: Validate session and CSRF token

    alt Security validation fails
        Auth-->>API: Request rejected
        API-->>UI: 403 Forbidden
        UI-->>User: Show appropriate error message

    else Security validation succeeds
        Auth-->>API: Validation successful
        API->>AI: Process explanation request

        AI->>ORM: Retrieve user consent
        ORM-->>AI: Consent status
        AI->>Consent: Evaluate consent
        Consent-->>AI: Consent decision

        alt Consent is missing or withdrawn
            AI-->>API: Consent required
            API-->>UI: Consent required
            UI-->>User: Show local summary and consent option

        else Consent is granted
            AI->>Dashboard: Request monthly financial summary
            Dashboard->>ORM: Retrieve owner-scoped financial data
            ORM-->>Dashboard: Financial records
            Dashboard-->>AI: Calculated financial figures

            AI->>AI: Check data sufficiency

            alt Data is insufficient
                AI-->>API: Insufficient financial data
                API-->>UI: Insufficient data response
                UI-->>User: Explain that more data is needed

            else Data is sufficient
                AI->>Prompt: Build aggregated prompt
                Prompt-->>AI: Prepared prompt
                AI->>Adapter: Request spending explanation
                Adapter->>LLM: Send aggregated financial data
                LLM-->>Adapter: Response or provider error
                Adapter-->>AI: Result or failure

                AI->>AI: Validate AI output

                alt Provider failure or invalid output
                    AI-->>API: Local fallback explanation
                    API-->>UI: Fallback response
                    UI-->>User: Display local explanation

                else Valid AI explanation
                    AI-->>API: Explanation and calculated figures
                    API-->>UI: Successful response
                    UI-->>User: Display financial explanation
                end
            end
        end
    end
```

Figure: Sequence diagram for generating an AI spending explanation. Django
first verifies the user's consent, then calculates financial figures locally
before sending only minimum aggregated data through the LLM adapter. The LLM
returns advisory text only. Missing consent, insufficient records, provider
failure, or invalid AI output never prevents the user from using expenses,
budgets, or dashboard features.
