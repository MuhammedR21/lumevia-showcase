# Batched featured-offer updates

Verbatim excerpt; no code adaptation. Selection from setOfferFeatured. Surrounding code resolves the target and handles disabling featured state. db, uid and target are contextual variables. This batch groups writes; it does not guarantee uniqueness under concurrent editors.

Private-source reference: `apps/admin/src/data/admin/offerAdminRepository.ts`, starting at line 151. This is a provenance reference, not a published file link.

```typescript
  const offers = await listOffers(target.productId);
  const batch = writeBatch(db);
  for (const offer of offers) {
    const nextFeatured = offer.id === offerId;
    if (Boolean(offer.featured) === nextFeatured) continue;
    batch.update(doc(db, "offers", offer.id), {
      featured: nextFeatured,
      updatedAt: serverTimestamp(),
      updatedBy: uid,
    });
  }
  await batch.commit();
```

Provided for portfolio review only; no general software reuse license is granted.
