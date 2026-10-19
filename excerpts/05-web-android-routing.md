# Web and Android routing

Verbatim excerpt; no code adaptation. Selection from createAppRouter. Native containers select hash routing; web selects browser routing. The route tree is omitted.

Private-source reference: `apps/admin/src/App.tsx`, starting at line 64. This is a provenance reference, not a published file link.

```typescript
  const createRouter = Capacitor.isNativePlatform() ? createHashRouter : createBrowserRouter;
```

Provided for portfolio review only; no general software reuse license is granted.
