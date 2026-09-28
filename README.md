# 🤖 AI Agent Skills Catalog

A curated collection of **355+ production-grade skills and domain playbooks** designed for AI coding assistants and autonomous agents (Antigravity, Claude Code, Gemini CLI, Cursor, Cline, and custom LLM workflows).

---

## 🚀 How to Use These Skills

### In Antigravity / Gemini Agentic Environments
To load these skills into your project workspace, place them inside your workspace root:

```bash
# Option 1: Direct copy to your workspace .agent directory
mkdir -p .agent/skills
cp -r /path/to/agent-skills/skills/* .agent/skills/

# Option 2: Symlink the skills repository
mkdir -p .agent
ln -s /path/to/agent-skills/skills .agent/skills
```

### In Global User Config
To make them available across all workspaces globally on your machine:
```bash
mkdir -p ~/.gemini/config/skills
cp -r skills/* ~/.gemini/config/skills/
```

---

## 🌟 Featured Skills Spotlight

### ☁️ Cloud Computing & Multi-Cloud Architecture
* **[oci-cloud-architect](skills/oci-cloud-architect/SKILL.md)**: Oracle Cloud Infrastructure (Compartments, OKE, Autonomous Database, VCNs, IAM Policies, OCI Functions).
* **[gcp-cloud-architect](skills/gcp-cloud-architect/SKILL.md)**: Google Cloud Platform (Cloud Run, GKE, BigQuery, Vertex AI, Workload Identity Federation, VPC Service Controls).
* **[aws-solutions-architect](skills/aws-solutions-architect/SKILL.md)**: AWS Well-Architected Framework, Serverless (Lambda, EventBridge), ECS/EKS Fargate, DynamoDB Single-Table Design, IAM Least-Privilege.
* **[azure-cloud-architect](skills/azure-cloud-architect/SKILL.md)**: Microsoft Azure (Container Apps, AKS, Entra ID, Managed Identities, Cosmos DB, Service Bus, Bicep IaC).
* **[cloud-native-design-patterns](skills/cloud-native-design-patterns/SKILL.md)**: Distributed resiliency patterns (Circuit Breaker, Saga, Event Sourcing, Outbox Pattern, Caching).
* **[firebase-fullstack-pro](skills/firebase-fullstack-pro/SKILL.md)**: Firestore NoSQL data modeling, complex Security Rules, Cloud Functions v2, and Local Emulator Suite.

### 🧠 Artificial Intelligence & Multi-Agent Systems
* **[agentic-workflow-architect](skills/agentic-workflow-architect/SKILL.md)**: Multi-Agent design, LangGraph state machines, CrewAI role orchestration, structured tool calling, and memory hierarchies.
* **[rag-implementation](skills/rag-implementation/SKILL.md)**: Retrieval-Augmented Generation, vector embeddings, chunking strategies, and hybrid semantic search.
* **[llm-evaluation](skills/llm-evaluation/SKILL.md)**: Benchmarking, automated LLM evaluation metrics, and guardrail validation.
* **[prompt-engineering-patterns](skills/prompt-engineering-patterns/SKILL.md)**: Advanced prompt architectures, few-shot prompting, and reasoning chains.

### 💻 Full-Stack & Software Architecture
* **[hexagonal-architecture-ddd](skills/hexagonal-architecture-ddd/SKILL.md)**: Ports and Adapters, Domain-Driven Design (DDD), decoupling domain business logic from frameworks.
* **[nextjs-fullstack-architect](skills/nextjs-fullstack-architect/SKILL.md)**: Next.js 15+ App Router, React Server Components (RSC), Server Actions, Suspense streaming, Core Web Vitals, TanStack Query.
* **[nestjs-enterprise-pro](skills/nestjs-enterprise-pro/SKILL.md)**: Enterprise NestJS architecture, microservices (RabbitMQ/gRPC), RBAC guards, Prisma/TypeORM transactions.
* **[csharp-clean-architecture](skills/csharp-clean-architecture/SKILL.md)**: C# .NET 8+, Clean Architecture, MediatR CQRS, and Entity Framework Core performance.

### 📈 Business, Sales, Marketing & Growth
* **[b2b-enterprise-sales-strategy](skills/b2b-enterprise-sales-strategy/SKILL.md)**: Enterprise software sales, MEDDPICC/BANT qualification, "Tell-Show-Tell" demos, and security review navigation.
* **[govtech-public-sector-strategy](skills/govtech-public-sector-strategy/SKILL.md)**: Public sector procurement, government tenders, sovereign cloud data residency, and civic technology adoption.
* **[brand-awareness-growth](skills/brand-awareness-growth/SKILL.md)**: Brand propagation for cloud-native apps, Product Hunt launches, developer marketing, and user activation funnels.
* **[google-play-console-aso](skills/google-play-console-aso/SKILL.md)**: Google Play Console lifecycle, App Store Optimization (ASO), store listing A/B experiments, policy compliance, and Android Vitals.
* **[youtube-growth-automation](skills/youtube-growth-automation/SKILL.md)**: YouTube automation pipelines, high-retention scriptwriting, thumbnail psychology, and CTR/AVD optimization.
* **[growth-copywriting-funnels](skills/growth-copywriting-funnels/SKILL.md)**: Direct-response sales letters, VSLs, email onboarding sequences, and funnel optimization.
* **[viral-growth-hacking](skills/viral-growth-hacking/SKILL.md)**: Viral loops ($K$-factor), short-form video hooks, and Product-Led Growth (PLG) mechanics.
* **[startup-finance-valuation](skills/startup-finance-valuation/SKILL.md)**: SaaS unit economics (CAC, LTV, NRR), runway/burn-rate modeling, cap tables, and valuation models.

### 🔍 Digital Investigation, OSINT & Intelligence Analysis
* **[osint-investigator-pro](skills/osint-investigator-pro/SKILL.md)**: Open Source Intelligence (OSINT), search dorking, domain & infrastructure pivoting, SOCMINT, and visual geolocation (GEOINT/IMINT).
* **[digital-investigation-forensics](skills/digital-investigation-forensics/SKILL.md)**: Digital forensics, evidence chain of custody, file metadata analysis (EXIF/documents), network infrastructure tracing, and email header analysis.
* **[data-cross-referencing-linkage](skills/data-cross-referencing-linkage/SKILL.md)**: Entity resolution, probabilistic record linkage, public dataset cross-referencing (corporate registries, gazettes, sanctions), and investigative knowledge graphs.
* **[investigative-journalism-factchecking](skills/investigative-journalism-factchecking/SKILL.md)**: Investigative journalism methods, multi-source fact-checking (IFCN standards), synthetic media / deepfake detection, and right-of-reply protocols.
* **[strategic-intelligence-analyst](skills/strategic-intelligence-analyst/SKILL.md)**: Strategic intelligence analysis, the Intelligence Cycle, Analysis of Competing Hypotheses (ACH), Admiralty System source evaluation, and BLUF executive reporting.

### ⚙️ Automation & DevOps
* **[n8n-workflow-automation](skills/n8n-workflow-automation/SKILL.md)**: Workflow automation with n8n, Queue Mode, AI Agent nodes, sub-workflows, and error handling.
* **[kubernetes-architect](skills/kubernetes-architect/SKILL.md)**: Production Kubernetes deployment, manifests, and autoscaling.
* **[terraform-specialist](skills/terraform-specialist/SKILL.md)**: Advanced Terraform module design, remote state, and policy as code.

---

## 📂 Skill Format Specification

Each skill lives in its own directory with a `SKILL.md` markdown file adhering to the agent specification:

```yaml
---
name: skill-name
description: Clear, action-oriented description of what the skill provides and when the agent should use it.
metadata:
  model: inherit
---

## Use this skill when
- Specific use cases, triggers, and tasks...

## Do not use this skill when
- Anti-triggers and scenarios outside this scope...

## Instructions
- Operational directives, step-by-step frameworks, architectural blueprints, code snippets, and anti-patterns...
```

---

## 📄 License
This repository is open-source under the [MIT License](LICENSE).
