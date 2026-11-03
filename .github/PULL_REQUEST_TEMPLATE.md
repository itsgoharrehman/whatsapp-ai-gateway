## Summary of Changes

Describe the proposed changes, architecture impact, and reason for the modification.

## Security Checklist

Before submitting this pull request, verify each of the following security invariants:

- [ ] **Multi-Tenant Isolation**: Verified that session state and message buffers remain strictly partitioned by `tenantId`.
- [ ] **No Hardcoded Credentials**: Verified that no WhatsApp session tokens, Groq API keys, or JWT secrets are committed.
- [ ] **Anti-Ban Throttling Preserved**: Verified that message dispatch routines respect rate limits and jitter delays.
- [ ] **Prompt Injection Defense**: Verified that user inputs injected into LLM prompts are properly sanitized.
- [ ] **Tests Pass**: Verified that `npm test` passes without errors.

## Testing Performed

Detail the local tests or manual verification steps conducted.