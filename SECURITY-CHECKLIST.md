# Denny's Arcade — Security Checklist
© 2026 Dennis Richards / Denny's Arcade (dennys-arcade.com)

Pre-commit security audit checklist. Run through this before every `git add .`
Severity levels: 🔴 Critical — 🟡 Important — 🟢 Good Practice

---

## 1. Frontend — HTML / CSS / JS

### Credentials & Secrets
- [ ] 🔴 No API keys, passwords, or tokens hardcoded in any JS file
- [ ] 🔴 No database credentials visible in client-side code
- [ ] 🔴 No internal server paths or file structures exposed in JS
- [ ] 🟡 No commented-out credentials left in code

### Input & Output
- [ ] 🔴 All user input sanitized before display (XSS prevention)
- [ ] 🔴 No `innerHTML` used with unsanitized user data — use `textContent`
- [ ] 🟡 No `eval()` used anywhere
- [ ] 🟡 No `document.write()` used anywhere

### Game Specific
- [ ] 🔴 Score submission uses server-side validation — never trust client score
- [ ] 🔴 Score values validated against reasonable maximums server side
- [ ] 🟡 No cheat-enabling debug flags left active in production
- [ ] 🟡 No console.log statements exposing game logic or user data

### Headers & Policy
- [ ] 🟡 Content Security Policy (CSP) header configured on server
- [ ] 🟡 X-Frame-Options header set to prevent clickjacking
- [ ] 🟡 X-Content-Type-Options header set to nosniff
- [ ] 🟢 Referrer-Policy header configured

---

## 2. PHP Backend

### Database
- [ ] 🔴 Every database query uses PDO prepared statements — NO exceptions
- [ ] 🔴 No raw user input concatenated into SQL strings
- [ ] 🔴 Database credentials stored in config file outside web root
- [ ] 🔴 Database user has minimum required permissions only
- [ ] 🟡 No sensitive data returned in API responses that isn't needed

### Input Validation
- [ ] 🔴 All POST/GET parameters validated and sanitized
- [ ] 🔴 Data types enforced — integers cast with (int), strings with trim/htmlspecialchars
- [ ] 🔴 File uploads validated for type, size, and content if applicable
- [ ] 🟡 Unexpected parameters ignored, not processed

### Authentication & Sessions
- [ ] 🔴 Session tokens regenerated on login
- [ ] 🔴 Passwords hashed with password_hash() — never stored plain text
- [ ] 🔴 CSRF tokens on all forms that modify data
- [ ] 🟡 Session timeout configured appropriately
- [ ] 🟡 Failed login attempts rate limited and logged

### API Endpoints
- [ ] 🔴 Rate limiting on all public API endpoints
- [ ] 🔴 Leaderboard submissions validated server side
- [ ] 🔴 Score anomaly detection — flag suspicious scores for review
- [ ] 🟡 API responses never expose stack traces or error details
- [ ] 🟡 HTTP methods validated — POST endpoints reject GET requests
- [ ] 🟢 API request logging for anomaly detection

### Error Handling
- [ ] 🔴 PHP error display OFF in production (display_errors = Off)
- [ ] 🔴 Errors logged server side, never shown to user
- [ ] 🟡 Generic error messages only — no internal details exposed
- [ ] 🟡 Custom 404/500 error pages configured

---

## 3. Infrastructure & Server

### File & Directory Security
- [ ] 🔴 Directory listing disabled in .htaccess
- [ ] 🔴 Config files with credentials not in public web root
- [ ] 🔴 .htaccess, .env, and config files blocked from public access
- [ ] 🟡 Sensitive file extensions blocked (.sql, .log, .bak, .env)
- [ ] 🟡 PHP execution disabled in upload directories
- [ ] 🟢 File permissions set correctly (644 files, 755 directories)

### HTTPS & Transport
- [ ] 🔴 SSL certificate valid and not expiring within 30 days
- [ ] 🔴 HTTP redirects to HTTPS — no mixed content
- [ ] 🟡 HSTS header configured
- [ ] 🟢 TLS 1.2 minimum, TLS 1.3 preferred

### .htaccess Hardening
- [ ] 🟡 Common attack vectors blocked (shell injection attempts)
- [ ] 🟡 User agent filtering for known malicious bots
- [ ] 🟡 Request method restrictions where appropriate
- [ ] 🟢 Server signature hidden (ServerTokens Prod)

### Backups
- [ ] 🟡 Database backup current before major deployments
- [ ] 🟡 File backup current before major deployments
- [ ] 🟢 Backup restoration tested periodically

---

## 4. Leaderboard & Score System (when built)

### Anti-Cheat
- [ ] 🔴 Scores validated against theoretical maximums
- [ ] 🔴 Time-based validation — score achievable in session duration
- [ ] 🔴 Server generates and validates session tokens per game
- [ ] 🟡 Honeypot fields to detect automated submissions
- [ ] 🟡 IP rate limiting on score submissions
- [ ] 🟡 Flagging system for anomalous scores — review queue
- [ ] 🟢 User agent validation on submissions

### Data Storage
- [ ] 🔴 No PII stored without explicit user consent
- [ ] 🔴 Player initials only — no full names required
- [ ] 🟡 Score data encrypted at rest if PII adjacent
- [ ] 🟢 Data retention policy defined and enforced

---

## 5. EdTech Platform (future — when user accounts added)

### User Accounts
- [ ] 🔴 COPPA compliance required for users under 13
- [ ] 🔴 GDPR/CCPA compliance for data collection
- [ ] 🔴 Email verification on account creation
- [ ] 🔴 Password requirements enforced (min length, complexity)
- [ ] 🔴 Account lockout after failed login attempts
- [ ] 🟡 Two factor authentication option for accounts
- [ ] 🟡 Privacy Policy and Terms of Service in place before launch

### Payment (when premium tier added)
- [ ] 🔴 Never store credit card data — use Stripe or equivalent
- [ ] 🔴 PCI compliance handled entirely by payment processor
- [ ] 🔴 HTTPS enforced on all payment flows
- [ ] 🔴 Payment confirmation emails sent and logged
- [ ] 🟡 Subscription management tested including cancellation flow

---

## 6. Pre-Deployment Final Check

- [ ] 🔴 Tested in dev environment before touching production
- [ ] 🔴 No debug or test code committed to production branch
- [ ] 🔴 All dependencies up to date — no known vulnerabilities
- [ ] 🟡 Git commit message clearly describes what changed
- [ ] 🟡 Peer review or self-review of diff before push
- [ ] 🟡 Rollback plan in place for major changes
- [ ] 🟢 Change logged in project notes

---

## Quick Reference — Most Common Vulnerabilities

| Vulnerability | Prevention |
|--------------|------------|
| SQL Injection | PDO prepared statements always |
| XSS | htmlspecialchars() on output, textContent not innerHTML |
| CSRF | Token on every state-changing form |
| Score Cheating | Server-side validation, session tokens, anomaly detection |
| Exposed Secrets | Config outside web root, no credentials in JS |
| Clickjacking | X-Frame-Options header |
| Brute Force | Rate limiting, account lockout |
| DDoS | Server-level rate limiting, upstream null routing if needed |

---

*Last updated: June 2026*
*Security is not a feature — it is a foundation.*
