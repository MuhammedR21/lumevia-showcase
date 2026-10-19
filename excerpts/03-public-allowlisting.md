# Public media allowlisting

Verbatim excerpt; no code adaptation. Named public fields are selected instead of serializing the administrative object. asString and asNumber are contextual helpers.

Private-source reference: `functions/src/sanitize.js`, starting at line 85. This is a provenance reference, not a published file link.

```javascript
function publicMediaItem(raw, id = "") {
  if (!raw || typeof raw !== "object") return null;
  const url = asString(raw.url).trim();
  if (!url) return null;
  if (raw.active === false) return null;
  return {
    id: asString(raw.id || id),
    url,
    alt: asString(raw.alt),
    role: asString(raw.role, "gallery"),
    sortOrder: asNumber(raw.sortOrder, 0),
    fit: raw.fit === "cover" || raw.fit === "contain" ? raw.fit : "contain",
    position: asString(raw.position, "center") || "center",
    active: true,
  };
}
```

Provided for portfolio review only; no general software reuse license is granted.
