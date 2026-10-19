# Architecture

## Website and administration boundaries

The public website is a responsive Arabic RTL HTML/CSS/JavaScript application built with Vite. It presents product information, ingredients, offers, usage guidance and other content. It is not a React application. Legal pages are separate Vite build inputs.

The administration application is a separate React/TypeScript project with its own Vite configuration. React Router selects browser routing on the web and hash routing in the Capacitor native container. Screens cover product, offers, content, reviews, site settings and media management.

## Data flow

~~~text
Administration UI → Firebase Authentication
                  → Firestore, subject to active-admin rules
Firestore writes  → Cloud Functions snapshot rebuild
                  → selected public fields + content revision
                  → generated JSON object in Storage
Public website    → Hosting /site-data.json rewrite
                  → HTTP Function → published Storage snapshot
                  ← cached JSON / conditional ETag response

Admin media selection → image preparation → Storage upload
                      → metadata attachment in Firestore
~~~

## Authentication and active-admin authorization

Email/password sign-in uses Firebase Authentication. The admin app resolves the signed-in user's membership and rejects sessions without an active admin profile. Protected routes depend on that resolved session. Membership is checked again when the app becomes visible.

Firestore rules independently require authentication and active membership. Clients cannot create, change or delete admin memberships; signed-in users can read their own membership document. This is a binary active-admin gate, not a verified granular owner/editor permission model. A callable manual snapshot-rebuild function also checks active-admin membership. Local rules were inspected; deployed rules and Firebase Console settings were not verified.

## Firestore data management

Repository modules separate database operations from screen rendering. The data model includes products, offers, site content, public settings, reviews and admin membership. Implemented operations include product updates, offer scheduling, featured selection, display ordering, content editing and review visibility.

Featured-offer changes and offer reordering use write batches. A batch commits its selected updates together; it does not guarantee uniqueness under concurrent editor sessions. Several writes record server timestamps and editor identifiers. No actual records are included here.

Admin TypeScript models, public JavaScript normalizers and a shared domain definition describe related shapes. They should not be presented as one automatically enforced shared schema.

## Cloud Functions snapshot generation

Cloud Functions v2 respond to relevant Firestore document writes. Rebuild code reads current canonical state and explicitly selects fields for the public snapshot. A SHA-256-derived revision tracks content; generation time is separate. A Firestore transaction claims a rebuild lock, and dirty-state handling requests follow-up work when additional changes arrive. This does not establish exactly-once processing or measured throughput.

The serializer excludes administrative fields such as editor identifiers and Storage paths. The public payload still intentionally includes some public-facing settings and visible review fields. Allowlisting does not replace consent or privacy review.

## Storage publication, caching and ETag responses

The snapshot is published as a generated JSON object in Storage. Local rules deny client access to that snapshot path; the serving Function reads it with the Admin SDK.

Firebase Hosting is configured to serve the website build and rewrite /site-data.json to the HTTP Function. The serving path reads an existing artifact rather than rebuilding or reading Firestore. It accepts GET/HEAD, returns Cache-Control and ETag headers, and sends HTTP 304 for an exactly matching If-None-Match value. A short per-instance memory cache complements browser/CDN caching.

The public client validates snapshots and supports a time-bounded last-known browser cache. Without usable configuration or snapshot data, production loading enters an unavailable state. Local development has separate fallback content and an optional legacy Firestore-loading path. Caching can delay administrative changes; immediate consistency is not claimed.

## Media handling and Android integration

Media handling includes image type/size validation, optional canvas resizing, uploads, metadata, display ordering, primary selection, fit/position controls and original-image restoration. Native photo selection uses Capacitor Camera's Photos source, with a browser file-input fallback.

The Android wrapper also integrates external browser opening, status-bar setup and hardware-back navigation. An Android project and local debug artifacts were observed in the audit. No APK, compiled output, signing material or environment configuration is included. Signed release, device behavior and Play Store distribution remain unverified.

## Authorization limitations

**Storage media writes are not admin-only.** Current rules allow any authenticated user to upload permitted image types within the size limit, overwrite images and delete objects in product, offer and site media paths. Firestore restricts attachment of media metadata to active admins, but does not protect underlying Storage objects from modification by other authenticated users. Public image reads are permitted.

This limitation should be resolved and verified before making stronger production-security claims. Nothing was changed during showcase preparation.

Android App Check currently reports setup guidance; verified Play Integrity attestation is not implemented by that module. Web App Check initialization is conditional. Console enforcement was not inspected.

## Ordering and deployment scope

Ordering produces a WhatsApp handoff. A payment gateway, transaction ledger or order-processing backend was not established by the audit. Firebase deployment scripts and Hosting/Functions configuration exist, but do not prove live deployment. No backend access, authentication or deployment was performed for this showcase.

[Back to showcase](../README.md) · [Visual provenance](../screenshots/README.md)
