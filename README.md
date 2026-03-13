# Deployment Engineering Meta-Prompt

A structured protocol for defining and initializing the infrastructure environment — containerization, orchestration, CI/CD pipelines, secrets management, and observability foundations — before development begins.

## What Is a Meta-Prompt?

A meta-prompt is a structured protocol document that initializes an AI-assisted development environment before task execution begins. Instead of starting with an ad-hoc prompt, the meta-prompt establishes workflow structure, behavioral constraints, and quality standards so that subsequent prompts operate inside a disciplined, well-defined system.

## This Meta-Prompt

Deployment Engineering establishes the infrastructure foundation that every other meta-prompt operates within. It covers:

- **Containerization** — multi-stage Dockerfiles, pinned image versions, non-root containers, image vulnerability scanning
- **Orchestration** — Kubernetes deployment configuration, resource limits, health probes, autoscaling
- **Infrastructure as Code** — Terraform with remote state, pinned module versions, plan-before-apply discipline
- **CI/CD Pipelines** — automated build, test, scan, stage, and production promotion with human approval gates
- **Secrets Management** — runtime injection via secrets managers, strict environment separation
- **Observability Setup** — log aggregation, metrics collection, and distributed tracing provisioned with the environment

This meta-prompt is invoked **before** the LLM-Native Software Engineering meta-prompt on greenfield projects. The execution environment must exist before the build protocol runs inside it.

## Part of the Meta-Prompt Ecosystem

This meta-prompt works alongside companion protocols:

| Meta-Prompt | Repository | Purpose |
|---|---|---|
| LLM-Native Software Engineering | https://github.com/pragnakar/LLM_NATIVE_SOFTWARE_ENGINEERING | Architecture and application development |
| Deployment Engineering | https://github.com/pragnakar/Deployment_Engineering | Infrastructure, containerization, and deployment |
| DevOps | https://github.com/pragnakar/DevOps | Runtime operations, observability, and reliability |
| Database | https://github.com/pragnakar/Database | Data persistence, schema design, and management |
| UI/UX | https://github.com/pragnakar/UI-UX | Interface and experience design |
| Security Engineering | https://github.com/pragnakar/Security_Engineering | Threat modeling, secure coding, and vulnerability management |
| MLOps | https://github.com/pragnakar/MLOps | Model lifecycle, training pipelines, and production ML |
| API Design | https://github.com/pragnakar/API_Design | Interface contracts, versioning, and developer experience |
| Testing Strategy | https://github.com/pragnakar/Testing-Strategy | Test architecture, coverage strategy, and quality verification |
| Documentation | https://github.com/pragnakar/Documentation | Technical writing, knowledge management, and developer docs |
| Scrum | https://github.com/pragnakar/Scrum | Agile sprint cycles, backlog management, ceremonies, and team coordination |

## Usage

1. Load `Deployment_Engineering.md` into your AI coding agent when starting a new project or establishing infrastructure
2. The agent follows the Principles, Practices, and Agent Instructions sections to generate Dockerfiles, Kubernetes manifests, Terraform, and CI/CD pipeline definitions
3. Use the Quick-Start Checklist to verify all infrastructure baselines are in place before the first production deployment
4. Reference Integration Points to coordinate with the DevOps and Database meta-prompts

## License

MIT

## Author

Pragnakar Pedapenki
