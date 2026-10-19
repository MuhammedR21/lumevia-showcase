# Conditional HTTP ETag response

Verbatim excerpt; no code adaptation. The endpoint sets its ETag earlier. This selection omits artifact loading and error handling.

Private-source reference: `functions/src/serve.js`, starting at line 76. This is a provenance reference, not a published file link.

```javascript
  if (req.headers["if-none-match"] === artifact.etag) {
    res.status(304).end();
    return;
  }
```

Provided for portfolio review only; no general software reuse license is granted.
