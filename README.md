# ☁️ Azure Cloud Resume Challenge

> A cloud-native personal resume and portfolio website built entirely on Microsoft Azure demonstrating real-world skills in cloud infrastructure, serverless computing, and DevOps automation.

![CI/CD Status](https://img.shields.io/badge/CI%2FCD-Passing-brightgreen?style=flat-square&logo=github-actions)
![Azure](https://img.shields.io/badge/Azure-Blob%20Storage-0078D4?style=flat-square&logo=microsoft-azure)
![Status](https://img.shields.io/badge/Status-Work%20In%20Progress-orange?style=flat-square)

---

## 🌐 Live Site

**→ [View Live Site](https://tkresumesite.z1.web.core.windows.net/)**

> _Custom domain + HTTPS via Azure CDN — coming in next phase_

---

## 📐 Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        BROWSER                              │
└──────────────────────────┬──────────────────────────────────┘
                           │ HTTP request
                           ▼
┌─────────────────────────────────────────────────────────────┐
│              Azure Blob Storage (Static Website)            │
│                  index.html · style.css · counter.js        │
└──────────────────────────┬──────────────────────────────────┘
                           │ JavaScript fetch()
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                   Azure Functions (HTTP Trigger)            │
│                   Python · Serverless · Consumption Plan    │
└──────────────────────────┬──────────────────────────────────┘
                           │ Read / Write
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                  Azure CosmosDB (NoSQL)                     │
│                  Serverless · Visitor Counter Document       │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                   CI/CD — GitHub Actions                    │
│   git push → deploy.yml → Azure Login → Blob Upload →      │
│   Deployment Complete ✅ (42s total run time)               │
└─────────────────────────────────────────────────────────────┘
```

**Planned addition (Phase 3 — WIP):**
```
Browser → Azure CDN (HTTPS + Custom Domain) → Blob Storage
```

---

## 🛠️ Technologies Used

| Layer | Technology | Purpose |
|---|---|---|
| Frontend | HTML, CSS, JavaScript | Static resume website |
| Hosting | Azure Blob Storage (Static Website) | Serve the site publicly |
| Serverless | Azure Functions (Python, HTTP Trigger) | Visitor counter API |
| Database | Azure CosmosDB (NoSQL, Serverless) | Store visitor count |
| CDN / HTTPS | Azure CDN _(planned)_ | Custom domain + SSL |
| CI/CD | GitHub Actions | Auto-deploy on every push |
| IaC | Azure CLI | Resource provisioning |
| Version Control | GitHub | Source of truth |

---

## ✅ What's Complete

- [x] Static resume website — HTML, CSS, JavaScript
- [x] Hosted on Azure Blob Storage with static website enabled
- [x] Visitor counter logic with Azure Functions (Python HTTP Trigger)
- [x] CosmosDB NoSQL database provisioned in Serverless mode
- [x] CI/CD pipeline via GitHub Actions — auto-deploys on every push to `main`
- [x] GitHub Secrets configured for secure Azure authentication
- [x] Successful end-to-end pipeline run (42s, Status: Success ✅)

## 🚧 Work In Progress

- [ ] Azure CDN configuration for HTTPS termination
- [ ] Custom domain mapping and SSL certificate provisioning
- [ ] Full end-to-end visitor counter integration (Function → CosmosDB → Frontend)
- [ ] Automated tests for the Azure Function
- [ ] CDN cache purge step added to GitHub Actions workflow

---

## ⚙️ CI/CD Pipeline

Every push to the `main` branch automatically triggers a GitHub Actions workflow that deploys the latest site files to Azure Blob Storage.

**Pipeline: `.github/workflows/deploy.yml`**

```yaml
name: Deploy Resume to Azure

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:

    - uses: actions/checkout@v3

    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Upload to Blob Storage
      run: |
        az storage blob upload-batch \
          --account-name ${{ secrets.STORAGE_ACCOUNT_NAME }} \
          --source . \
          --destination '$web' \
          --overwrite
```

**Latest successful run:**

![CI/CD Pipeline Success](./assets/cicd-success.png)

> Pipeline run: `Update .github #1` · Status: **Success** · Duration: **42s** · Trigger: `on: push` to `main`

---

## 🧩 Project Structure

```
azure-cloud-resume/
├── index.html                  # Resume website
├── style.css                   # Styling
├── counter.js                  # Visitor counter frontend logic
├── resume-counter/             # Azure Function app
│   ├── __init__.py             # HTTP trigger — reads/writes CosmosDB
│   ├── function.json           # Function binding configuration
│   └── requirements.txt        # Python dependencies
├── .github/
│   └── workflows/
│       └── deploy.yml          # GitHub Actions CI/CD pipeline
├── assets/
│   └── cicd-success.png        # Pipeline screenshot
└── README.md
```

---

## 🔧 Azure Resources Provisioned

```
Resource Group:     resume-rg
│
├── Storage Account (Static Website enabled)
│   └── $web container → hosts index.html
│
├── Azure Function App (Consumption Plan · Python 3.9)
│   └── HttpTrigger → visitor counter API endpoint
│
└── CosmosDB Account (Serverless · NoSQL API)
    └── ResumeDB → Counter container → { id: "1", count: N }
```

---

## 🚧 Challenges & How I Solved Them

### Challenge 1: Azure Function Authentication & Tokens

**Problem:** Configuring the Azure Function to securely connect to CosmosDB without hardcoding credentials was more involved than expected. The Function App requires CosmosDB connection details passed as environment variables — getting the right combination of endpoint URI and primary key, and understanding where to set them in the Azure Portal took significant debugging.

**Status:** Function App is provisioned and deployed. CosmosDB connection via environment variables is configured. Full end-to-end integration is still being validated — this is the active work in progress.

**What I learned:** Azure's identity and secrets model, specifically the difference between connection strings, managed identities, and API keys for CosmosDB is nuanced. The correct production approach uses Managed Identity rather than API keys, which eliminates the need to store credentials entirely. This is the next implementation step.

---

## 🔑 Key Concepts Demonstrated

- **Cloud storage** — Azure Blob Storage for static website hosting
- **Serverless computing** — Azure Functions on a Consumption Plan (pay-per-execution)
- **NoSQL databases** — CosmosDB with serverless billing model
- **CI/CD automation** — GitHub Actions pipeline with Azure authentication
- **IAM & secrets management** — Azure Service Principals, RBAC, GitHub Secrets
- **CORS** — Cross-origin resource sharing in a serverless API context
- **Infrastructure as Code mindset** — provisioning resources via Azure CLI rather than only clicking through the portal

---

## 💡 What I Would Do Differently

- **Use Managed Identity from the start** — rather than CosmosDB API keys, Managed Identity eliminates credential storage entirely and is the production-correct approach
- **Implement CDN before testing the Function** — the CDN step should come before the Function integration since CORS configuration changes depending on the origin URL
- **Add automated Function tests earlier** — testing the Function locally with `func start` before deploying would have caught the CosmosDB connection issues faster

---

> _Part of a broader portfolio of cloud, cybersecurity, and AI operations projects._
> _Built to learn. Documented to teach._