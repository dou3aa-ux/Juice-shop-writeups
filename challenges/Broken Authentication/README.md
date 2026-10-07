## Challenge: JWT Inspection & Token Payload Analysis

- **Category:** Broken Authentication / Sensitive Data Exposure
- **Difficulty:** ★☆☆☆☆
- **Goal:** Inspect authentication mechanisms and analyze client-stored JSON Web Tokens (JWT).

---

### Vulnerability Explanation
JSON Web Tokens (JWT) are commonly used to handle authentication in modern single-page applications (SPAs). A JWT consists of three base64url-encoded parts:
1. **Header:** Specifies the algorithm and token type.
2. **Payload:** Contains claims about the user (e.g., user ID, role, email) and metadata.
3. **Signature:** Used by the server to verify token integrity.

Because the payload is **encoded, not encrypted**, anyone who intercepts or accesses the token can decode its content to read user data. Furthermore, storing sensitive information (such as password hashes or internal user IDs) inside JWT claims leads to **Sensitive Data Exposure**.

---

### Step-by-Step Walkthrough

#### Step 1: Locate Stored Authentication Token
Upon logging in or interacting with the application, open Developer Tools (`F12`) and navigate to **Application > Local Storage .
Under the storage keys, locate the `token` key holding the bearer JWT.


#### Step 2: Extract & Decode Token via JWT Debugger
Copy the raw JWT string from Local Storage and paste it into a JWT decoder tool.


#### Step 3: Analyze Exposed Claims
Examining the decoded **Payload** reveals sensitive internal details about the authenticated user session:
- **User Identifier (`data.id`):** `25`
- **Email (`data.email`):** `azerty@gmail.com`
- **User Role (`data.role`):** `customer`
- **Exposed Password Hash (`data.password`):** `ab4f63f9ac65152575886860dde480a1` (MD5 hash)
- **Basket ID (`bid`):** `6`

---

### Key Takeaways & Remediation

1. **Do Not Store Secrets in JWT Claims:** Never include password hashes, internal secrets, or excessive personal data in a JWT payload.
2. **JWTs are Encoded, Not Encrypted:** Anyone with access to the token string can read its payload using basic base64 decoding.
3. **Use Strong Hashing & Salting:** Storing MD5 password hashes (even server-side) is insecure due to susceptibility to lookup tables and fast collision attacks.
