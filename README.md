<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="images/flai-logo-dark.svg?v=2" />
    <source media="(prefers-color-scheme: light)" srcset="images/flai-logo-light.svg?v=2" />
    <img width="180" alt="flAI Logo" src="images/flai-logo-dark.svg?v=2" />
</picture>
  <br/>
  <br/>
  <a href="https://flai.gh.nos.pt/">https://flai.gh.nos.pt</a>
</p>

# Build and test a multi-tier application with GitHub Copilot

_Use GitHub Copilot agent mode to implement, test, and review a React + Express + MongoDB application from executable contracts._

<img src=".github/images/octofit-tracker.png" alt="GitHub Skills banner for Mergington High School: Build and test a multi-tier application with GitHub Copilot" width="720"/>

## Welcome

- **Who is this for**: Developers comfortable with GitHub, VS Code, JavaScript/TypeScript, and basic GitHub Copilot workflows.
- **What you will learn**: How to use Copilot agent mode as an engineering partner while preserving architecture boundaries, working from tests, validating generated code, and reviewing a multi-tier change.
- **What you will build**: OctoFit Tracker with activity tracking, team management, a competitive leaderboard, and personalized workout suggestions.
- **Prerequisites**:
  - [Getting Started with GitHub Copilot](https://github.com/nosportugal/flai-workshop-github-copilot-100)
  - Familiarity with React, HTTP APIs, and automated tests.
  - Access to [GitHub Codespaces](https://github.com/features/codespaces).
- **How long**: 90–120 minutes, depending on your GitHub Copilot familiarity.

## Application Architecture

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
    
    style User stroke-width:3px
    style Frontend stroke-width:3px,fill:#b3e5fc,color:#000
    style Backend stroke-width:3px,fill:#c8e6c9,color:#000
    style Database stroke-width:3px,fill:#ffe0b2,color:#000
    style Codespaces stroke-width:3px
    style App stroke-width:3px,stroke:#ff6b35
    
    linkStyle default stroke-width:3px
```

In this exercise, you will:

1. Explain and preserve presentation, logic, and data-tier boundaries.
1. Use failing tests and explicit contracts to bound Copilot's work.
1. Implement and test Mongoose data models and representative seed data.
1. Implement and test Express API behavior, including validation and error states.
1. Implement and test React routes and API-driven user experiences.
1. Verify the integrated system and review a multi-tier pull request before merging.

### How to start this exercise

Simply copy the exercise to your account, then give your favorite Octocat (Mona) **about 20 seconds** to prepare the first lesson, then **refresh the page**.

[![Copy Exercise](https://img.shields.io/badge/Copy%20Exercise-%E2%86%92-1f883d?style=for-the-badge&logo=github&labelColor=197935)](https://github.com/new?template_owner=nosportugal&template_name=flai-workshop-github-copilot-800&owner=%40me&name=flai-workshop-github-copilot-800&description=Exercise:+Build+and+test+a+multi-tier+application+with+GitHub+Copilot&visibility=private)

<details>
<summary>Having trouble? 🤷</summary><br/>

When copying the exercise, we recommend the following settings:

- For owner, choose your personal account or an organization to host the repository.
- We recommend creating a public repository, since private repositories will use Actions minutes.
- If the exercise is not ready in 20 seconds, open the **Actions** tab in your copied repository.
- If a job failed, rerun it once. If it fails again, open an issue with the workflow log link.

</details>

---

&copy; 2025 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)
