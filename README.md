# mcp-gateway-bifrost

> An enterprise-grade MCP (Model Context Protocol) Gateway with federated authentication, multi-tenant governance, rate limiting, and full audit logging — the control plane for AI agent tool access at scale.

---

## Overview

`mcp-gateway-bifrost` is a platform-engineering portfolio project that solves a critical gap in enterprise AI agent deployments: **who controls what agents can do, and how do you know what they did?**

Named after Bifrost — the bridge between worlds in Norse mythology — this gateway sits between AI agents and the platform tools they consume, acting as a secure, observable, policy-enforced control plane.

It wraps any downstream MCP server (e.g., `platform-ops-mcp-server`) and adds enterprise concerns that production environments demand:

- **Federated Identity** — agents authenticate via OIDC/JWT, mapped to tenants and roles
- **Tenant Governance** — per-tenant tool allowlists, rate limits, and quota enforcement
- **Audit Logging** — every tool call is logged with caller identity, inputs, outputs, and latency
- **Policy Engine** — fine-grained allow/deny rules per tool, tenant, and environment

---

## Motivation

As AI agents gain access to real infrastructure — Kubernetes clusters, Terraform state, production metrics — organizations need a control plane that enforces:

- **Zero-trust access** — no agent gets implicit trust; every call is authenticated and authorized
- **Blast radius control** — a rogue or misconfigured agent cannot take down production
- **Compliance auditability** — every action is traceable to an identity, a timestamp, and a reason
- **Multi-tenancy** — platform teams, product teams, and SREs each get scoped access

Bifrost is the answer: a single gateway that enforces these concerns uniformly, regardless of which agent or tool is in use.

---

## Key Features

- **Federated Auth** — OIDC/JWT token validation, identity federation across providers (Okta, GitHub, Google)
- **Multi-tenant Routing** — requests are scoped to a tenant context with isolated policies
- **Tool Governance** — per-tenant tool allowlists and denylists, with environment-aware rules
- **Rate Limiting** — per-tenant and per-tool call rate limits to prevent abuse
- **Audit Log Pipeline** — structured JSON logs for every tool invocation, exportable to SIEM/observability stacks
- **Proxy Architecture** — wraps any MCP server without modifying it
- **Admin API** — manage tenants, policies, and audit logs via REST

---

## Architecture

```
AI Agent (Claude / LangGraph / any MCP client)
        │
        ▼
┌─────────────────────────────┐
│      mcp-gateway-bifrost     │
│                             │
│  ┌─────────────────────┐    │
│  │  Auth Layer (OIDC)  │    │
│  └────────┬────────────┘    │
│           │                 │
│  ┌────────▼────────────┐    │
│  │  Policy Engine      │    │
│  │  (tenant + tool     │    │
│  │   allowlist/denylist│    │
│  └────────┬────────────┘    │
│           │                 │
│  ┌────────▼────────────┐    │
│  │  Rate Limiter       │    │
│  └────────┬────────────┘    │
│           │                 │
│  ┌────────▼────────────┐    │
│  │  Audit Logger       │    │
│  └────────┬────────────┘    │
└───────────┼─────────────────┘
            │
            ▼
  Downstream MCP Server(s)
  (e.g. platform-ops-mcp-server)
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Python 3.11+ |
| Protocol | Model Context Protocol (MCP) |
| Auth | OIDC / JWT (via `python-jose`) |
| Policy Engine | Custom rule engine (YAML-based policies) |
| Rate Limiting | Token bucket / sliding window |
| Audit Logging | Structured JSON → stdout / file / webhook |
| Admin API | FastAPI |
| Transport | HTTP SSE + stdio |
| Packaging | Docker |

---

## Project Status

> 🚧 In active development — portfolio project showcasing enterprise platform engineering + AI agent governance.

---

## Roadmap

- [ ] MCP proxy scaffold (passthrough mode)
- [ ] JWT/OIDC authentication middleware
- [ ] Tenant context resolution and routing
- [ ] YAML-based policy engine (allow/deny rules)
- [ ] Per-tenant tool allowlist enforcement
- [ ] Rate limiting middleware
- [ ] Structured audit log pipeline
- [ ] FastAPI admin API (tenants, policies, logs)
- [ ] Docker packaging
- [ ] Integration test suite with mock MCP server
- [ ] Example: multi-tenant demo with `platform-ops-mcp-server`

---

## Related Projects

- [`platform-ops-mcp-server`](https://github.com/skbasetti/platform-ops-mcp-server) — downstream MCP server exposing K8s, Terraform, Prometheus tools
- [`iac-audit-agent`](https://github.com/skbasetti/iac-audit-agent) — LangGraph IaC governance agent that consumes platform tools via this gateway

---

## Author

**skbasetti** — Platform Engineering | AI Agents | Cloud Infrastructure
