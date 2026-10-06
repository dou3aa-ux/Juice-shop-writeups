Challenge: View Basket (Broken Access Control / BOPA)
Difficulty: ★★☆☆☆

Category:Broken Access Control / Insecure Direct Object Reference (IDOR)
Status: Solved
What the challenge asks:View another user's shopping basket

Vulnerability Explanation
Web applications often maintain session state or user identifiers in client-side storage (such as `Session Storage` or `Local Storage`). When the application trusts client-side modified values without proper server-side authorization checks, an attacker can tamper with identifiers to access data belonging to other users.

Step-by-Step Walkthrough:

Step 1: Analyze Client-Side Storage & Hints
First, open Developer Tools (`F12`) and inspect the **Application** tab under **Session Storage**. 
The challenge hint guides us to look for session keys related to the shopping baske.

Step 2: Inspect Initial Session Storage Values
We navigate to our basket page (`/#/basket`). Looking at `Session Storage` under `http://localhost:3000`, we observe key-value pairs used by the front-end application:
- `bid`: `1` (Basket ID)
- `itemTotal`: `21.94`


Step 3: Modify the Basket Identifier (`bid`):
To test for insecure object reference/broken access control, we alter the `bid` value directly in Developer Tools[cite: 4]:
1. Change `bid` from `1` to `3` (or another integer).
2. Press Enter to apply the change in Session Storage.

Step 4: Trigger Application Refresh
After updating `bid` to `3` in Session Storage, navigate to another page (such as the home screen or product listings) and return to **Your Basket** to allow the application to re-render using the updated session key.

Key Takeaways & Remediation
- **Root Cause:** The application relies on client-modifiable `Session Storage` key (`bid`) to determine which basket content to fetch, lacking proper server-side ownership authorization.
- **Prevention:** Always enforce server-side authorization checks using a secure session token (JWT/Session ID) to verify that the requesting user owns the requested resource ID before returning data.
