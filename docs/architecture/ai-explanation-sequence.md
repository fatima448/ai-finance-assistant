```mermaid
sequenceDiagram
    participant User
    participant WebUI
    participant DjangoAPI
    participant ConsentPolicy
    participant DashboardService
    participant PromptBuilder
    participant LLMAdapter
    participant ExternalLLM

    User->>WebUI: Request monthly explanation
    WebUI->>DjangoAPI: Send explanation request
    DjangoAPI->>ConsentPolicy: Check consent

    alt Consent is missing or withdrawn
        ConsentPolicy-->>DjangoAPI: Consent not granted
        DjangoAPI-->>WebUI: Consent required
        WebUI-->>User: Show local explanation and consent option
    else Consent is granted
        ConsentPolicy-->>DjangoAPI: Consent granted
        DjangoAPI->>DashboardService: Request monthly summary
        DashboardService-->>DjangoAPI: Calculated figures

        alt Data is insufficient
            DjangoAPI-->>WebUI: Insufficient data
            WebUI-->>User: Explain that more expenses are needed
        else Data is sufficient
            DjangoAPI->>PromptBuilder: Build aggregated request
            PromptBuilder->>LLMAdapter: Send explanation request
            LLMAdapter->>ExternalLLM: Request explanation

            alt LLM is unavailable or response is invalid
                ExternalLLM-->>LLMAdapter: Error response
                LLMAdapter-->>DjangoAPI: AI request failed
                DjangoAPI-->>WebUI: AI unavailable with local explanation
                WebUI-->>User: Show fallback explanation
            else LLM returns an explanation
                ExternalLLM-->>LLMAdapter: explanation
                LLMAdapter-->>DjangoAPI: Valid explanation
                DjangoAPI-->>WebUI: Explanation and figures
                WebUI-->>User: Display explanation and references
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
