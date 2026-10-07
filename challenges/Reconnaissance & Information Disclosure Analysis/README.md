## Challenge: Reconnaissance & Information Disclosure Analysis

- **Category:** Security Misconfiguration / Information Disclosure
- **Difficulty:** ★★★☆☆
- **Goal:** Uncover sensitive files, hidden directories, client-side source code routes, and API endpoint configurations through recon and proxy inspection.

---

### Vulnerability Explanation
Information Disclosure occurs when an application unintentionally reveals sensitive data, system configurations, hidden endpoints, or internal file structures to unauthorized users. Attackers leverage these leaks to map the attack surface and locate sensitive assets (e.g., backup files, internal APIs, or cryptographic keys).

---

### Step-by-Step Walkthrough

#### Step 1: Directory Listing in Standard Locations ('/.well-known/')
Navigating to 'http://localhost:3000/.well-known/' reveals directory listing enabled on the server, exposing available files and directories:
- `csaf/` folder
- `security.txt` file


#### Step 2: Accessing Security Policy File ('/security.txt')
Accessing 'http://localhost:3000/.well-known/security.txt' reveals the security policy, contact email ('mailto:donotreply@owasp-juice.shop'), public PGP key fingerprint link, and internal navigation paths ('/#/score-board', '/#/jobs').


#### Step 3: Discovering Exposed FTP Directory via 'robots.txt'
Inspecting 'http://localhost:3000/robots.txt' reveals a disallowed route: 'Disallow: /ftp'.


Navigating to 'http://localhost:3000/ftp' exposes sensitive backup files, configuration backups, Keepass databases, and encrypted announcements:
- 'coupons_2013.md.bak'
- 'incident-support.kdbx'
- 'package.json.bak'
- 'encrypt.pyc'


#### Step 4: Hidden Route Discovery in Client JavaScript ('main.js')
By inspecting Developer Tools ('F12') under **Sources > main.js**, searching for path routes reveals hidden application routes such as 'administration' ('path: 'administration'').


Attempting to navigate directly to 'http://localhost:3000/#/administration' as an unprivileged user triggers a '403 You are not allowed to access this page!' response, verifying the route's existence.


#### Step 5: Application Configuration Endpoint Exposure ('/rest/admin/application-configuration')
Using Burp Suite's **HTTP history** tab or navigating directly in the browser exposes the '/rest/admin/application-configuration' API endpoint.


The API response returns raw JSON containing application settings, database/server configurations, social links, feature flags, and product data.


---

### Key Takeaways & Remediation

1. **Disable Directory Listing:** Ensure Web Server configuration (e.g., NGINX, Apache, Express) disables index browsing for paths like '/.well-known/' or '/ftp'.
2. **Restrict Public File Access:** Prevent sensitive backup formats ('.bak', '.kdbx', '.pyc', '.yml') from being placed inside publicly accessible web roots.
3. **Restrict API Access:** Protect administrative endpoints like '/rest/admin/application-configuration' behind strict server-side role-based access control (RBAC).
4. **Obfuscation & Minimization in Client Scripts:** Avoid embedding administrative routes or internal API blueprints inside client-side JavaScript bundles ('main.js').
