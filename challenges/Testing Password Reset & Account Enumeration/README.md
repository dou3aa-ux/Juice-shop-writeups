## Challenge: Testing Password Reset & Account Enumeration

- **Category:** Broken Authentication / Insecure Password Recovery
- **Difficulty:** ★★☆☆☆
- **Goal:** Identify user enumeration vulnerabilities and analyze security question password recovery mechanisms.

---

### Vulnerability Explanation
Insecure password recovery mechanisms often suffer from two major flaws:
1. **User Enumeration:** The application reveals whether an email address exists in the system based on different error responses or dynamic UI changes (e.g., loading security questions for valid accounts vs. showing errors for non-existent ones).
2. **Weak Security Questions:** Relying on static, easily guessable, or low-entropy security answers allows attackers to perform targeted account takeovers via social engineering or brute-forcing.

---

### Step-by-Step Walkthrough

Step 1: Account Registration & Security Question Setup
During user registration (`testuser2@test.com`), a security question is selected (*"Your eldest siblings middle name?"*) and set to a simple answer (`test`).


 Step 2: Triggering Account Enumeration via Forgot Password
Navigating to the `/forgot-password` endpoint:
- Entering a valid registered email (`testuser2@test.com`) causes the application to dynamically fetch and display that user's assigned security question.



- Entering an unregistered email (`doesnotexist12345@test.com`) leaves the security question field blank or unresponsive. This functional difference allows an attacker to enumerate valid email addresses on the target system.


 Step 3: Security Question Validation Error
Providing an incorrect answer to the security question triggers a clear error message: *"Wrong answer to security question."*


 Step 4: Successful Password Reset
Entering the correct answer (`test`) along with a new password bypasses standard authentication and successfully resets the account password.


---

### Key Takeaways & Remediation:

1. **Prevent Account Enumeration:** Password recovery forms should return generic, consistent responses regardless of whether the submitted email exists in the database (e.g., *"If an account exists with that email, a password reset link has been sent."*).
2. **Avoid Knowledge-Based Authentication (KBA):** Static security questions are vulnerable to social engineering, OSINT, and brute-forcing.
3. **Use Out-of-Band Reset Links:** Secure password resets should rely on time-limited, cryptographically secure tokens sent directly to the user's verified email or primary authentication device.
