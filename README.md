<div align="center">
  <h1>whatsapp-ai-gateway</h1>
  <p>Multi-tenant WhatsApp AI gateway with isolated session management, Cloudflare edge routing, and super admin console.</p>

  <p>
    <a href="https://github.com/itsgoharrehman/whatsapp-ai-gateway/releases"><img src="https://img.shields.io/github/v/release/itsgoharrehman/whatsapp-ai-gateway?style=flat-square&logo=github&logoColor=white" alt="Release"></a>
    <a href="LICENSE"><img src="https://img.shields.io/github/license/itsgoharrehman/whatsapp-ai-gateway?style=flat-square&logo=opensourceinitiative&logoColor=white" alt="License"></a>
    <img src="https://img.shields.io/badge/Runtime-Node.js_18+-339933?style=flat-square&logo=nodedotjs&logoColor=white" alt="Node.js">
    <img src="https://img.shields.io/badge/Protocol-Baileys-25D366?style=flat-square&logo=whatsapp&logoColor=white" alt="Baileys">
    <img src="https://img.shields.io/badge/Edge-Cloudflare_Workers-F38020?style=flat-square&logo=cloudflare&logoColor=white" alt="Cloudflare">
    <a href="https://github.com/itsgoharrehman/whatsapp-ai-gateway/stargazers"><img src="https://img.shields.io/github/stars/itsgoharrehman/whatsapp-ai-gateway?style=flat-square&logo=github&logoColor=white" alt="Stars"></a>
    <a href="https://github.com/itsgoharrehman/whatsapp-ai-gateway/issues"><img src="https://img.shields.io/github/issues/itsgoharrehman/whatsapp-ai-gateway?style=flat-square&logo=github&logoColor=white" alt="Issues"></a>
  </p>
</div>

---

## Overview

whatsapp-ai-gateway is a multi-tenant enterprise messaging gateway connecting WhatsApp networks to large language model providers (Groq SDK). Engineered with `@whiskeysockets/baileys` and Cloudflare Workers edge tunneling, it provides isolated tenant session lifecycles, automated prompt routing, anti-ban throttling, and an administrative oversight console.

Each tenant maintains independent cryptographic session stores, custom prompt configurations, and rate limits, preventing cross-tenant data bleed.

## Architecture and Message Pipeline

```mermaid
sequenceDiagram
    autonumber
    participant WA as WhatsApp Network
    participant Edge as Cloudflare Worker Edge
    participant Core as Node.js Gateway Core (Baileys)
    participant Auth as Session & Permission Manager
    participant LLM as Groq LLM API
    participant DB as SQLite / PostgreSQL Store

    WA->>Core: Inbound WhatsApp Message
    Core->>Auth: Validate Tenant Session & Anti-Ban Rate Limit
    alt Throttled / Unauthorized
        Auth-->>Core: Reject Message / Apply Cooldown
    else Authorized
        Auth->>DB: Fetch Tenant Context & Conversation History
        Core->>LLM: Dispatch Query with System Persona
        LLM-->>Core: Return Inferred Response Payload
        Core->>DB: Append Conversation Turn
        Core->>WA: Transmit Response via Baileys Socket
    end

    opt Admin Supervision
        Edge->>Core: Authenticated Super Admin WebSocket
        Core-->>Edge: Stream Live Throughput Metrics & Session States
    end
```

## Core Capabilities

### 1. Multi-Tenant Session Isolation
* Distinct credentials directory per registered tenant (`auth_info_<tenant_id>/`).
* Isolated connection sockets, QR pairing sequences, and reconnection state machines.

### 2. High-Throughput AI Pipeline
* Built-in Groq SDK integration supporting high-speed inference on LLaMA 3 models.
* Configurable system prompts, temperature controls, and context window truncation per tenant.

### 3. Edge Routing and Web Management Console
* Cloudflare Worker edge proxy handles ingress load balancing and SSL termination.
* Responsive dashboard for real-time monitoring of active sockets, throughput metrics, and user management.

### 4. Anti-Ban Throttling Engine
* Random jitter delays between message dispatches.
* Burst rate limiting to conform to WhatsApp heuristic traffic patterns.

## Prerequisites

* **Runtime**: Node.js 18.x or 20.x LTS.
* **Edge Proxy**: Cloudflare Wrangler CLI (for worker deployment).
* **AI Provider**: Groq Cloud API Key (`gsk_...`).
* **Database**: SQLite (default local) or PostgreSQL connection URI.

## Installation and Quickstart

### 1. Clone the Repository

```bash
git clone https://github.com/itsgoharrehman/whatsapp-ai-gateway.git
cd whatsapp-ai-gateway
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Configure Environment

Copy the example configuration:

```bash
cp .env.example .env
```

Edit `.env`:

```env
PORT=5000
NODE_ENV=production
JWT_SECRET=your_secure_random_jwt_secret
GROQ_API_KEY=gsk_your_groq_api_key_here
DATABASE_URL=postgresql://user:password@localhost:5432/whatsapp_gateway
CLOUDFLARE_TUNNEL_TOKEN=your_tunnel_token_if_applicable
```

### 4. Run the Gateway

```bash
# Start server
npm start

# Run test suite
npm test
```

## Configuration Reference

| Variable | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `PORT` | Number | `5000` | Port for the backend API and admin console. |
| `NODE_ENV` | String | `development` | Runtime environment (`development` or `production`). |
| `JWT_SECRET` | String | Required | Secret used to sign administrative JWT tokens. |
| `GROQ_API_KEY` | String | Required | Groq Cloud API key for LLM inference. |
| `DATABASE_URL` | String | Optional | PostgreSQL connection string (falls back to local SQLite). |
| `MAX_TENANTS` | Number | `50` | Maximum concurrent active WhatsApp Baileys sessions. |

## Security Considerations

* **Tenant Boundary Isolation**: File access routines strictly sandbox session credentials to prevent directory traversal across tenant identifiers.
* **Secret Sanitization**: Groq API keys and JWT secrets are injected via process environment and never serialized to client responses.
* **Webhook Signature Verification**: Edge worker payloads are authenticated using HMAC-SHA256 signatures before being consumed by the backend.

## License and Security Contact

This project is licensed under the [MIT License](LICENSE).

For responsible disclosure of security vulnerabilities, review [SECURITY.md](SECURITY.md) or contact `goharrehmanfsd260@gmail.com`.