# 🛡️ DVWA Advanced Labs – Vulnerability Detection & Exploitation

These hands-on labs use **DVWA (Damn Vulnerable Web Application)** and simulate real-world scenarios where developers or analysts might accidentally introduce security vulnerabilities. Each lab includes a walkthrough, detection tips, exploitation steps, and real-world mitigation.

### Start the DVWA environment
```sh
docker compose -f ./labs/docker-compose.yml up -d
```

Visit:
```sh
http://localhost:4280/
```

- username: `admin`
- password: `password`

1. After login click on `Create / Reset Database` button at the end of the page. 
2. Tables will be created and click on `Please login` link generated at the end of the page. credentials remains same.

### Set the DVWA Security to LOW
1. Open `DVWA Security` in left panel and set it to `"Low"`.