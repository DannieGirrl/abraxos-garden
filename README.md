# DTC

Danielle's personal life-management app. Single-file PWA, no build step.

- `index.html` / `app.html` — the app (byte-identical; edit both together)
- `financial.html` — money page and the hoard
- `meals.html` — meals and shopping
- `assets/` — mascot images (required)

All data lives in the browser's localStorage. Use the in-app **Back up my data**
button before changing domains — localStorage does not follow you across origins.
