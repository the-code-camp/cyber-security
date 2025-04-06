## 🔍 Lab 1: Command Injection – Remote Code Execution in Legacy Scripts

If an application appears to be executing system commands, it may be vulnerable to command injection attacks. A command injection attack is a high-risk vulnerability that enables an attacker to execute arbitrary commands on the remote system. In order to attempt to exploit a vulnerable endpoint, you can chain commands via the web API by using a semicolon and another Linux command (such as `whoami`) in the payload. 

### 💼 Scenario
A legacy admin tool takes input (host/IP) and runs a ping command using shell_exec. Developers assumed it's safe as it’s internal-only.

### 🎯 Objective
Exploit unsanitized input to execute arbitrary shell commands.

### 🧪 Steps
1. Access DVWA → Command Injection (Security: Low)
2. Input a legitimate IP: `127.0.0.1` → observe output.
In the response, you can see that the ping command has executed and the result has been included in the response.

3. Try: `127.0.0.1 && whoami`
You will see the response as above, but at the end of the response, you can also see the `whoami` command has executed, and you have `www-data` in the response.

4. When attempting to identify and exploit a command injection vulnerability, the following payloads can be used to chain Linux commands: 

   1. `;`
   2. `&`
   3. `&&`
   4. `|`
   5. `||`

Use all 5 of these different payloads to chain commands to exploit the command injection vulnerability.

5. Try other payloads:
   - `; ls -la`
   - `| cat /etc/passwd`

6. Observe server-side execution of arbitrary commands.

### 🧠 Real-World Context
- This could allow full system compromise.
- Legacy scripts or internal-only tools are often ignored during security audits, making them easy targets for lateral movement.
- Common in poorly written Bash/Python/PHP scripts that call OS commands.

### 🛡️ Mitigation
- Never pass unsanitized input to shell commands.
- Avoid using `shell_exec()` with raw input.
- Use `subprocess.run()` with list-style args or input validation libraries.
- Validate and sanitize inputs.
