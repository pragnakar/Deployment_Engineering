# Deployment Engineering

## A Meta-Prompt for Infrastructure, Runtime Environments, and Continuous Deployment

**Version:** 1.0
**Repository:** https://github.com/pragnakar/Deployment_Engineering
**Parent Meta-Prompt:** LLM-Native Software Engineering — https://github.com/pragnakar/LLM_NATIVE_SOFTWARE_ENGINEERING
**Companion Meta-Prompts:** DevOps, Database, UI-UX, Security Engineering, MLOps, API Design, Testing Strategy, Documentation, Scrum

---

## Thesis

Deployment engineering is not the final step of development — it is the first. Before code is written, the execution environment must be defined: containerized, version-controlled, and reproducible. This meta-prompt establishes the infrastructure foundation that every other meta-prompt operates within, eliminating the gap between development and production by making them structurally identical from day one.

## When This Meta-Prompt Is Invoked

Invoke this meta-prompt when:
- Starting a new project that will run in any environment beyond a local developer machine
- A project requires containerized development, CI/CD pipelines, or cloud infrastructure
- The team needs a consistent, reproducible runtime across development, staging, and production
- Deployment configuration or infrastructure-as-code needs to be defined or audited
- A project is transitioning from local development to a cloud or container-based environment

Invoke **before** running the LLM-Native Software Engineering meta-prompt on greenfield projects. The execution environment must exist before the build protocol runs inside it.

## Scope

**This meta-prompt covers:**
- Docker-based development environments and container image standards
- Kubernetes deployment configuration and service definitions
- CI/CD pipeline definitions (GitHub Actions, GitLab CI, and equivalents)
- Infrastructure provisioning (Terraform, Pulumi, CloudFormation)
- Environment configuration and secrets management
- Observability foundations: logging pipelines, metrics collection, tracing setup
- Security of infrastructure and runtime environments

**This meta-prompt does NOT cover:**
- Runtime monitoring, alerting, and incident response — handled by the **DevOps** meta-prompt
- Database schema design and data migration — handled by the **Database** meta-prompt
- Application architecture and build workflow — handled by **LLM-Native Software Engineering**
- User interface design — handled by the **UI-UX** meta-prompt

## Principles

1. **Environment first.** The execution environment must be defined before development begins. Development that occurs outside a containerized environment creates reproducibility debt that compounds with every passing week.

2. **Parity across environments.** Development, staging, and production environments use identical container images. Configuration (environment variables, secrets) differentiates environments — not code or image content.

3. **Infrastructure is code.** All infrastructure is defined declaratively in version-controlled files. No manually configured infrastructure is permitted in staging or production. If it isn't in git, it doesn't exist.

4. **Secrets never live in code or container images.** Credentials, API keys, and configuration secrets are injected at runtime via secret management systems. They are never baked into images, committed to repositories, or stored in plaintext configuration files.

5. **Every deployment is reproducible.** Given a git SHA and an environment target, the deployment must be fully reconstructable without human intervention.

6. **Security is structural, not procedural.** Least-privilege access, network isolation, and non-root containers are built into infrastructure definitions — not applied as a post-deployment audit finding.

7. **Observability is provisioned alongside compute.** Logging, metrics, and tracing infrastructure is deployed as part of the environment. It is not added when debugging becomes necessary.

## Practices

### Containerization

- Write multi-stage Dockerfiles: a build stage and a minimal runtime stage. The runtime image must not contain build tools, source code, or test dependencies.
- Pin all base images to exact versions in production builds. Use `FROM python:3.12.3-slim` not `FROM python:latest`.
- Run containers as non-root users. Set `USER` explicitly in every Dockerfile.
- `.dockerignore` must exclude `.git`, local environment files (`.env`), secrets, test artifacts, and development tooling.
- Scan container images for known vulnerabilities (Trivy, Grype, Snyk) before promotion to staging or production. Block on critical severity findings.

### Container Orchestration

- Use Kubernetes namespaces to separate environments: `dev`, `staging`, `production`.
- Define `resources.requests` and `resources.limits` on every pod spec. Unbounded containers are not permitted in staging or production.
- Configure `readinessProbe` and `livenessProbe` on all long-running services. Services without health probes cannot be safely managed by the orchestrator.
- Configure Horizontal Pod Autoscalers (HPAs) before a service handles production traffic.
- Use `RollingUpdate` deployment strategy by default. `Recreate` only where stateful constraints require it, with documented justification.

### Infrastructure as Code

- All cloud infrastructure is defined in Terraform (or equivalent IaC tool). No infrastructure is created via cloud console or CLI without a corresponding IaC representation.
- Terraform state is stored remotely (S3 + DynamoDB lock, GCS + lock, or equivalent). Local state is prohibited in shared or production environments.
- IaC modules are versioned and pinned. Floating version references are not used in production.
- Infrastructure changes go through `plan → review → apply`. No direct `terraform apply` in production without a reviewed and approved plan artifact.

### CI/CD Pipelines

- Every pull request triggers: lint, test, build, and container image scan.
- Promotion to staging requires: all tests passing, image scan with no critical vulnerabilities, and at least one approval.
- Promotion to production requires: successful staging deployment and an explicit human approval gate. Production auto-deploy is not permitted.
- Rollback must be automated: a failed health check post-deployment triggers automatic rollback to the last known-good deployment.
- Pipeline definitions live in the repository alongside application code and are reviewed like application code.

### Configuration and Secrets Management

- Configuration is injected via environment variables or mounted config files at runtime. It is never hardcoded in application code or container images.
- Secrets are stored in a secrets manager: AWS Secrets Manager, HashiCorp Vault, GCP Secret Manager, or Kubernetes Secrets with encryption at rest enabled.
- Rotate secrets on a defined schedule. Automate rotation where the platform supports it.
- Maintain strict environment separation: a staging secret must not be usable in production, and vice versa. Use separate secret namespaces or vaults per environment.

### Observability Setup

- Structured logging (JSON) is configured at the infrastructure level. Log format is consistent across all services in the environment.
- Log aggregation (Fluentd, Vector, CloudWatch Logs, Datadog, or equivalent) is provisioned with the environment — not added as a debugging afterthought.
- Metrics collection (Prometheus, Datadog agent, or cloud-native equivalent) is deployed as part of the cluster configuration.
- Distributed tracing (OpenTelemetry) is configured in the runtime environment. Applications emit traces without bespoke infrastructure work from application developers.

## Agent Instructions

When an LLM coding agent is operating under this meta-prompt:

**Do:**
- Generate Dockerfiles with multi-stage builds by default
- Include `.dockerignore` whenever generating a Dockerfile
- Reference secrets as environment variable names only — never generate actual secret values
- Generate Kubernetes manifests with `resources:` blocks, health probes, and explicit non-root security contexts
- Generate Terraform with remote state configuration and pinned provider versions
- Generate CI/CD pipelines with explicit approval gates separating staging from production
- Verify that generated configurations do not expose ports, endpoints, or credentials unnecessarily

**Do NOT:**
- Hardcode secrets, tokens, or passwords anywhere in infrastructure code
- Generate containers that run as root unless explicitly required with written justification
- Use floating base image tags (`FROM node:latest`) — always use pinned versions
- Generate Terraform that uses local state or floating module version references
- Generate CI/CD pipelines that auto-deploy to production without an approval gate
- Make decisions about database provisioning without referencing the Database meta-prompt
- Define monitoring alert thresholds — that is the DevOps meta-prompt's domain

**Verify before proceeding:**
- Does the Dockerfile specify a non-root `USER`?
- Are all base images pinned to specific versions?
- Are all secrets referenced as environment variable names, not values?
- Does the CI/CD pipeline have a human approval gate before production?
- Is Terraform state configured for remote storage?

## Integration Points

- **LLM-Native Software Engineering:** This meta-prompt initializes the environment before LLM-Native SE's build protocol runs. CI/CD pipelines defined here execute the verification steps defined in LLM-Native SE. The `.build/` directory and control documents defined by LLM-Native SE live inside the containerized environment defined here.

- **DevOps:** Deployment Engineering provisions the infrastructure on which DevOps operates. The logging pipelines, metrics collectors, and tracing agents configured here are the data sources DevOps monitors and alerts on. Deployment Engineering defines the deployment mechanism and rollback strategy; DevOps defines the operational conditions that trigger rollback.

- **Database:** The Database meta-prompt defines schema and data model requirements; this meta-prompt provisions the database instance (RDS, Cloud SQL, containerized Postgres, etc.) that implements them. Database connection credentials are managed through the secrets management system defined here.

- **UI-UX:** No direct integration with design decisions. Frontend static assets may be served via CDN infrastructure provisioned here. The build pipeline defined here includes frontend build and asset publishing steps.

- **Security Engineering:** Deployment Engineering provisions the runtime security controls that Security Engineering's threat model requires: non-root containers, network segmentation, image vulnerability scanning, and secret injection. Pre-commit hooks and CI pipeline security gates defined here enforce secrets management rules defined by Security Engineering.

- **MLOps:** When the project includes machine learning, Deployment Engineering provisions the training infrastructure (GPU clusters, managed ML platforms) and model serving endpoints. MLOps defines what needs to be deployed; this meta-prompt defines the container, orchestration, and CI/CD patterns that deploy it.

- **API Design:** API services defined by API Design are containerized, orchestrated, and delivered via the pipelines defined here. This meta-prompt ensures API services have appropriate resource limits, health probes, and rolling deployment strategies.

- **Testing Strategy:** The CI/CD pipeline defined here executes the test suite defined by Testing Strategy. Pipeline gate ordering (unit → integration → E2E → scan) follows Testing Strategy's test pyramid. Integration test environments are provisioned as containerized infrastructure here.

- **Documentation:** Deployment guides and runbooks for the infrastructure defined here are documented following Documentation meta-prompt standards. Architecture diagrams for deployment topology are maintained in `.build/docs/architecture.md`.

- **Scrum:** Deployment Engineering's sprint deliverables include environment setup, pipeline configuration, and infrastructure provisioning tasks. Release cadence and deployment freeze windows coordinate with sprint review and release milestones defined in Scrum.

## Anti-Patterns

| Anti-Pattern | Why It's Harmful |
|---|---|
| Building infrastructure manually via cloud console | Creates infrastructure drift — declared state diverges from actual state, making deployments unreliable and audits impossible |
| Hardcoding secrets in Dockerfiles or environment files | Secrets in images are extractable from any layer in the image history. Secrets committed to git are permanent, even if later removed |
| Using `:latest` or any floating image tag | The image content changes without warning, breaking reproducibility and making cross-environment debugging impossible |
| Running containers as root | A container escape vulnerability grants full host access when the container process runs as root |
| Skipping resource requests and limits | A single misbehaving container starves other containers of CPU/memory, causing cascading failures across the cluster |
| Auto-deploying to production without an approval gate | Allows test failures or misconfigurations to reach users automatically; removes human judgment from the highest-stakes deployment event |
| Storing Terraform state locally | Concurrent applies corrupt state; local state cannot be shared across team members or CI/CD systems |
| Coupling staging and production secrets | A staging credential compromise becomes a production credential compromise |
| Adding observability after a production incident | The incident that reveals the observability gap is the one you cannot diagnose; structured logging and metrics must precede production |

## Quick-Start Checklist

```
[ ] Create Dockerfile with multi-stage build and explicit non-root USER
[ ] Create .dockerignore excluding .git, .env, secrets, test artifacts
[ ] Pin all base image versions (no :latest)
[ ] Define Kubernetes manifests: Deployment, Service, ConfigMap
[ ] Add resource requests and limits to all pod specs
[ ] Add readinessProbe and livenessProbe to all services
[ ] Configure remote Terraform state (bucket + lock table)
[ ] Write Terraform for all provisioned infrastructure
[ ] Configure secrets manager and inject all secrets at runtime via environment variables
[ ] Create CI/CD pipeline: lint → test → build → scan → staging → [human gate] → production
[ ] Configure rollback trigger on failed post-deployment health check
[ ] Deploy log aggregation infrastructure
[ ] Deploy metrics collection (Prometheus/Datadog/equivalent)
[ ] Configure OpenTelemetry tracing in runtime environment
[ ] Scan container image for vulnerabilities before first deployment
[ ] Verify no secrets exist in git history or image layers
```

## Version History

- v1.0 — 2026-03-06 — Initial harmonization: full restructure to canonical meta-prompt template, added Principles, Practices, Agent Instructions, Integration Points, Anti-Patterns, and Quick-Start Checklist

---

*Part of the Meta-Prompt Ecosystem: https://github.com/pragnakar*
