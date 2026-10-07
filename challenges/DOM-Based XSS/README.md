## Challenge: DOM-Based XSS - Bonus Payload (SoundCloud Embed)

- **Category:** Cross-Site Scripting (XSS) / DOM-Based XSS
- **Difficulty:** ★★☆☆☆
- **Goal:** Perform a DOM XSS attack using an '<iframe>' payload that dynamically embeds and auto-plays an external SoundCloud audio widget inside the search results view.

---

### Vulnerability Explanation
DOM-Based Cross-Site Scripting occurs when an application processes untrusted user input on the client side (e.g., via JavaScript) and writes it directly back into the Document Object Model (DOM) without proper sanitization or context-aware encoding.

In OWASP Juice Shop, the search input field takes the user's query string and dynamically reflects it into the DOM header (*"Search Results - ..."*). Because the input is rendered directly as raw HTML rather than text, arbitrary HTML tags—such as '<iframe src="...">'—are executed in the browser context.

---

### Step-by-Step Walkthrough

#### Step 1: Identify the Sink
Navigating to the main page, we locate the search input bar at the top of the interface. The search bar updates the URL parameters and client-side JavaScript dynamically updates the search result heading using an unsafe DOM sink (such as 'innerHTML').

#### Step 2: Inject the HTML Payload
Instead of a simple '<script>' or '<img>' tag, we submit an inline frame ('<iframe>') embedding an external SoundCloud player:

```html
<iframe width="100%" height="166" scrolling="no" frameborder="no" allow="autoplay" src="[https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/771984076&color=%23ff5500&auto_play=true&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true](https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/771984076&color=%23ff5500&auto_play=true&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true)"></iframe>
