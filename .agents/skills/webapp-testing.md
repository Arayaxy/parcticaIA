---
name: webapp-testing-js
description: Toolkit for interacting with and testing local web applications using Playwright (Node.js). Supports verifying frontend functionality, debugging UI behavior, and capturing browser screenshots.
---

# Web Application Testing (JavaScript / Node.js)

To test local web applications, write native JavaScript Playwright scripts executing via Node.js.

**Helper Scripts Available**:
- `scripts/with-server.js` - Manages Node server lifecycles (e.g., Vite, Express) across multiple ports.

**Rule:** Always execute scripts with `--help` first to see usage parameters. DO NOT read the raw wrapper code unless necessary to save context window tokens.

## Decision Tree: Choosing Your Approach

```text
User task → Is it static HTML?
 ├─ Yes → Read HTML file directly to identify CSS selectors
 │   ├─ Success → Write Node.js Playwright script using selectors
 │   └─ Fails/Incomplete → Treat as dynamic (below)
 │
 └─ No (dynamic webapp) → Is the server already running?
     ├─ No → Run lifecycle helper to boot server + execute Node automation
     └─ Yes → Reconnaissance-then-action workflow:
         1. Navigate and wait for networkidle
         2. Take screenshot or inspect DOM
         3. Identify selectors from rendered state
         4. Execute actions with discovered selectors