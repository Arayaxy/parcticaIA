---
name: security-audit-js
description: Toolkit for auditing application source code, verifying dependency trees, and inspecting web endpoints for vulnerabilities using Node.js utilities.
---

# Security Auditing (JavaScript / Node.js)

To audit applications for security risks, use native Node packages or execute custom JavaScript scanning scripts.

**Helper Scripts Available**:
- `scripts/scan-dependencies.js` - Wraps security advisory tools to check package trees.
- `scripts/payload-tester.js` - A script to test local API endpoints for injection handling.

**Rule:** Always run helper scripts with `--help` to confirm inputs. Treat the underlying scripts as black-box utilities to minimize token consumption in the context window.

## Decision Tree: Choosing Your Approach

```text
User task → Is it an internal code/dependency review?
 ├─ Yes → Run dependency scanner or regex pattern matchers on code files
 │   ├─ Vulnerabilities found → Log risks & generate secure JS rewrite fixes
 │   └─ Clean → Proceed to configuration review
 │
 └─ No (Live endpoint test) → Is the test target running locally?
     ├─ No → Terminate or ask user for explicit local/sandbox target URI
     └─ Yes → Reconnaissance-then-action workflow:
         1. Read endpoint route definitions from Express/Node source files
         2. Craft malformed inputs (SQLi payloads, unescaped XSS scripts)
         3. Execute `payload-tester.js` against the routes
         4. Analyze HTTP response statuses and error logs