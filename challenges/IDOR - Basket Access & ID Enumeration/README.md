## Challenge: BOPA / IDOR - Basket Access & ID Enumeration

- **Category:** Broken Object Level Authorization (BOLA / IDOR)
- **Difficulty:** ★★☆☆☆

- **Goal:** Access and enumerate shopping baskets belonging to other accounts by manipulating client-side state parameters.

---

### Vulnerability Explanation
Insecure Direct Object Reference (IDOR) occurs when an application uses client-supplied input or client-side storage keys to access objects directly (such as database IDs) without verifying if the authenticated user owns that resource.

In OWASP Juice Shop, the front-end stores the active shopping basket ID (`bid`) inside **Session Storage**. Because the application trusts the `bid` value provided by the browser without validating ownership on the back-end, an attacker can increment or decrement `bid` to view and interact with arbitrary users' shopping carts.

---

### Step-by-Step Walkthrough

#### Step 1: Inspect Current User Basket (`bid: 6`)
Logged in as `testuser2@test.com`, we navigate to **Your Basket** (`/#/basket`). 
Opening Developer Tools (`F12`) under **Application > Session Storage **, we observe:
- **`bid` (Basket ID):** `6`
- **`itemTotal`:** `4.87`

#### Step 2: Access Basket ID `5` (BOPA Vulnerability)
We modify `bid` in Session Storage from `6` to `5` and trigger a page re-render. 
The application fetches Basket ID `5`, revealing a different set of items:
- **`bid`:** `5`
- **`itemTotal`:** `54.93`


#### Step 3: Test Non-Existent or Empty Basket ID (`bid: 7`)
Changing `bid` to `7` displays an empty cart, indicating Basket ID `7` has no items assigned or does not exist.


#### Step 4: Access Basket ID `4`
Changing `bid` to `4` renders another user's cart containing Raspberry Juice (x2) with a total price of `9.98`.


---

### Key Takeaways & Remediation

1. **Never Trust Client-Side Session Storage for Access Control:** Identifiers stored in `Session Storage` or `Local Storage` can be modified freely by users
2. **Server-Side Authorization Checks:** The server must map the authenticated session token (e.g., JWT) to the user's allowed resources and reject requests for `bid` values that do not belong to the logged-in user.
3. **Use Non-Sequential Identifiers:** Using sequential integer IDs (`1`, `2`, `3`, `4`...) makes resource enumeration easy. Using UUIDs (e.g., `f47ac10b-58cc-4372-a567-0e02b2c3d479`) mitigates simple guessable ID enumeration.
