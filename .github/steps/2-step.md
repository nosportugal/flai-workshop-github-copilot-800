## Step 2: Implement the MongoDB data tier

Continue on the `build-octofit-app` branch and keep each Copilot request bounded by the tests for this tier.

```mermaid
%%{init: {'theme': 'neutral', 'themeVariables': { 'primaryColor': '#4a90d9', 'primaryTextColor': '#fff', 'primaryBorderColor': '#2d5a87', 'lineColor': '#5c6bc0', 'secondaryColor': '#81c784', 'tertiaryColor': '#fff3e0'}, 'flowchart': {'nodeSpacing': 50, 'rankSpacing': 80, 'padding': 40}}}%%
flowchart LR
    User((👤 User))
    
    subgraph Codespaces [☁️ GitHub Codespaces]
        subgraph App [🏋️ OctoFit Tracker App]
            Frontend[⚛️ React<br/>Frontend]
            Backend[🟢 Express<br/>Backend]
            Database[(🍃 MongoDB<br/>Database)]
        end
    end
    
    User --> Frontend
    Frontend <--> Backend
    Backend <--> Database
    
    style User stroke-width:2px,fill:#e3f2fd40,color:#00000050,stroke:#1976d250,stroke-dasharray: 5 5
    style Frontend stroke-width:2px,fill:#b3e5fc40,color:#00000050,stroke:#0288d150,stroke-dasharray: 5 5
    style Backend stroke-width:2px,fill:#c8e6c940,color:#00000050,stroke:#2e7d3250,stroke-dasharray: 5 5
    style Database stroke-width:4px,fill:#ffe0b2,color:#000,stroke:#ef6c00
    style Codespaces stroke-width:3px
    style App stroke-width:3px,stroke:#ff6b35
    
    linkStyle 0 stroke-width:2px,stroke:#cccccc
    linkStyle 1 stroke-width:2px,stroke:#cccccc
    linkStyle 2 stroke-width:3px
```

> **🎯 Current Focus:** In this step, we're implementing the **MongoDB data tier**: Mongoose models and repeatable seed data.

### 📖 Theory: Model invariants and representative data

Mongoose schemas should express the rules the rest of the application can rely on, not merely mirror arbitrary JSON. Relationships and validation for activities, teams, leaderboard inputs, and workout suggestions make downstream API behavior predictable. Focused model tests and representative seed data expose schema mistakes before they become HTTP or UI bugs.

### ⌨️ Activity: Implement the MongoDB data tier

1. Read the data-contract tests and the existing model stubs in `octofit-tracker/backend`.

1. Ask Copilot for a bounded data-tier plan.

   > ![Static Badge](https://img.shields.io/badge/Prompt-text?style=for-the-badge&logo=github-copilot&logoColor=white&labelColor=purple&color=purple)
   >
   > ```text
   > Explain why the data-tier tests fail and propose changes limited to Mongoose models and seed data. Preserve the contracts and do not edit API or React files.
   > ```

1. Implement schemas and seed records in small changes, running the data tests after each capability. Use stable fixture keys and upserts so rerunning the seed preserves unrelated MongoDB records.

   > ![Static Badge](https://img.shields.io/badge/Terminal-text?logo=gnometerminal&labelColor=0969da&color=ddf4ff)
   >
   > ```bash
   > npm run test:data --prefix octofit-tracker/backend
   > ```

1. Load the representative records into the Codespace MongoDB instance and run the command twice to confirm it is idempotent.

   > ![Static Badge](https://img.shields.io/badge/Terminal-text?logo=gnometerminal&labelColor=0969da&color=ddf4ff)
   >
   > ```bash
   > npm run seed --prefix octofit-tracker/backend
   > npm run seed --prefix octofit-tracker/backend
   > ```

1. Review the diff for validation rules and realistic relationships, then commit and push.

<details>
<summary>Having trouble? 🤷</summary><br/>

- Use Mongoose models rather than raw MongoDB scripts.
- Seed all four capabilities and keep repeated seed runs idempotent.
- Diagnose the first failing assertion instead of regenerating every model.

</details>
