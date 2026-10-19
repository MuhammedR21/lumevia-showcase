# Snapshot versioning

Verbatim excerpt; no code adaptation. The content revision is independent of generation time. The surrounding module supplies body, crypto, now and the leakage check.

Private-source reference: `functions/src/buildPublicSnapshot.js`, starting at line 76. This is a provenance reference, not a published file link.

```javascript
  const canonical = JSON.stringify(body);
  assertNoPrivateLeakage(canonical);

  const revision = crypto.createHash("sha256").update(canonical).digest("hex").slice(0, 16);
  const generatedAt = now.toISOString();
```

Provided for portfolio review only; no general software reuse license is granted.
