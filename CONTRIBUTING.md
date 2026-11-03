# Contributing to whatsapp-ai-gateway

Thank you for your interest in contributing. We welcome contributions that adhere to our multi-tenant security architecture.

## Core Architectural Invariants
All contributions must preserve these system guarantees:
1. **Multi-Tenant Boundary Integrity**: Code changes must never assume a single global WhatsApp session. All session handling must be scoped to `tenantId`.
2. **Zero Secret Leakage**: Never log, commit, or serialize `GROQ_API_KEY`, `JWT_SECRET`, or Baileys authentication files.
3. **Anti-Ban Throttling**: Message sending routines must route through the `antiBan.js` throttling engine to respect rate limits.
4. **Input Sanitization**: User inputs passed to LLM system prompts must be sanitized to mitigate prompt injection attacks.

## Contribution Workflow
1. Fork and clone the repository:
   ```bash
   git clone https://github.com/<your-username>/whatsapp-ai-gateway.git
   cd whatsapp-ai-gateway
   ```
2. Create a feature branch:
   ```bash
   git checkout -b feature/your-feature-name
   ```
3. Run existing tests:
   ```bash
   npm test
   ```
4. Open a Pull Request targeting `main`. Complete the mandatory security checklist in `.github/PULL_REQUEST_TEMPLATE.md`.