## 🔍 Lab 1: Command Injection – Remote Code Execution in Legacy Scripts

### 💼 Scenario
A legacy admin tool takes input (host/IP) and runs a ping command using shell_exec. Developers assumed it's safe as it’s internal-only.

### 🎯 Objective
Exploit unsanitized input to execute arbitrary shell commands.

### 🧪 Steps
1. Access DVWA → Command Injection (Security: Low)
1. Input a legitimate IP: `127.0.0.1` → observe output.
1. Try: `127.0.0.1 && whoami`
1. Try other payloads:
   - `; ls -la`
   - `| cat /etc/passwd`
1. Observe server-side execution of arbitrary commands.

### 💡 Hints:
- Use `;, &&, |` depending on OS.
- Try pinging a different host and chaining output.

### 🧠 Real-World Context
- This could allow full system compromise.
- Legacy scripts or internal-only tools are often ignored during security audits, making them easy targets for lateral movement.
- Common in poorly written Bash/Python/PHP scripts that call OS commands.

### 🛡️ Mitigation
- Never pass unsanitized input to shell commands.
- Avoid using `shell_exec()` with raw input.
- Use `subprocess.run()` with list-style args or input validation libraries.
- Validate and sanitize inputs.
