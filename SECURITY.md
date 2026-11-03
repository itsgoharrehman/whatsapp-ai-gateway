# Security Policy

## Supported Versions

| Version | Supported | Description |
| :--- | :--- | :--- |
| `2.1.x` | Yes | Current stable release branch (`main`) |
| `< 2.1.0` | No | Legacy versions |

## Multi-Tenant Security Scope and Boundaries

whatsapp-ai-gateway operates as a multi-tenant messaging gateway. Strict security invariants are enforced:
* **Tenant Isolation**: Each tenant's WhatsApp session files (`auth_info_<tenant_id>/`) must remain strictly isolated. Cross-tenant credential access is considered a critical security vulnerability.
* **API Key Protection**: External LLM keys (Groq SDK) and internal administrative JWT tokens must never be written to static files, returned in API responses, or exposed to the frontend console.
* **Edge Worker Authentication**: Edge-to-origin communication through Cloudflare Workers must enforce HMAC-SHA256 request signing and replay attack prevention.

## Reporting a Vulnerability

If you discover a security vulnerability or multi-tenant authorization bypass, please report it privately:

* **Security Contact**: Gohar Rehman
* **Email**: `goharrehmanfsd260@gmail.com`
* **Response SLA**: Initial acknowledgement within 48 hours; remediation timeline provided within 7 days.

Please do not open public GitHub issues or discussions regarding undisclosed vulnerabilities.