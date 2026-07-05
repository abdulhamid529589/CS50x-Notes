# CS50 Introduction to Cybersecurity — Securing Software

### Lecture Notes — David J. Malan

---

## 📑 Table of Contents

1. [Introduction & Overview](#1-introduction--overview)
2. [HTML Basics for Security Context](#2-html-basics-for-security-context)
   - 2.1 [Tags and Structure](#21-tags-and-structure)
   - 2.2 [Anchor Tags and Hyperlinks](#22-anchor-tags-and-hyperlinks)
3. [Phishing via HTML Manipulation](#3-phishing-via-html-manipulation)
   - 3.1 [Mismatched Link Text and URL](#31-mismatched-link-text-and-url)
   - 3.2 [Typosquatting and Cloned Pages](#32-typosquatting-and-cloned-pages)
   - 3.3 [Q&A: IP Addresses in URLs](#33-qa-ip-addresses-in-urls)
4. [Code Injection Attacks](#4-code-injection-attacks)
   - 4.1 [Cross-Site Scripting (XSS) — Reflected](#41-cross-site-scripting-xss--reflected)
   - 4.2 [Cross-Site Scripting (XSS) — Stored](#42-cross-site-scripting-xss--stored)
   - 4.3 [Mitigating XSS: Character Escaping](#43-mitigating-xss-character-escaping)
   - 4.4 [Mitigating XSS: Content-Security-Policy Header](#44-mitigating-xss-content-security-policy-header)
   - 4.5 [Q&A: JavaScript Blocking and React/JSX](#45-qa-javascript-blocking-and-reactjsx)
5. [SQL Injection](#5-sql-injection)
   - 5.1 [How SQL Queries Are Built](#51-how-sql-queries-are-built)
   - 5.2 [Destructive Injection Example (DELETE)](#52-destructive-injection-example-delete)
   - 5.3 [Authentication Bypass Injection (`OR '1'='1'`)](#53-authentication-bypass-injection-or-11)
   - 5.4 [Mitigation: Prepared Statements](#54-mitigation-prepared-statements)
   - 5.5 [Q&A: Least-Privilege Database Accounts](#55-qa-least-privilege-database-accounts)
6. [Command Injection](#6-command-injection)
7. [Client-Side vs Server-Side Validation](#7-client-side-vs-server-side-validation)
   - 7.1 [Developer Tools and the `disabled` Attribute](#71-developer-tools-and-the-disabled-attribute)
   - 7.2 [The `required` Attribute](#72-the-required-attribute)
8. [Cross-Site Request Forgery (CSRF)](#8-cross-site-request-forgery-csrf)
   - 8.1 [GET-Based CSRF](#81-get-based-csrf)
   - 8.2 [POST-Based CSRF (Automated via JavaScript)](#82-post-based-csrf-automated-via-javascript)
   - 8.3 [Mitigation: CSRF Tokens](#83-mitigation-csrf-tokens)
   - 8.4 [Q&A: AI and Quantum Computing](#84-qa-ai-and-quantum-computing)
9. [Local/System-Level Attacks](#9-localsystem-level-attacks)
   - 9.1 [Arbitrary & Remote Code Execution](#91-arbitrary--remote-code-execution)
   - 9.2 [Buffer Overflow Mechanics](#92-buffer-overflow-mechanics)
   - 9.3 [Cracking and Reverse Engineering](#93-cracking-and-reverse-engineering)
10. [Defensive Ecosystem & Trust Models](#10-defensive-ecosystem--trust-models)
    - 10.1 [Open Source vs Closed Source](#101-open-source-vs-closed-source)
    - 10.2 [App Stores & Digital Signatures](#102-app-stores--digital-signatures)
    - 10.3 [Package Managers](#103-package-managers)
    - 10.4 [Bug Bounty Programs](#104-bug-bounty-programs)
11. [Vulnerability Tracking Standards](#11-vulnerability-tracking-standards)
12. [Key Terms Glossary](#12-key-terms-glossary)

---

## 1. Introduction & Overview

This lecture focuses on **securing software** — both software you use as an end user and software you write as a programmer. The running example used throughout is **phishing**, extended into a broader discussion of how the building blocks of the web (HTML, JavaScript, SQL, HTTP) can be _misused_ by adversaries, and how developers can defend against that misuse.

**Core theme repeated throughout the lecture:**

> Never trust user input. Always validate/escape/sanitize it — ideally on the server, not just the client.

---

## 2. HTML Basics for Security Context

### 2.1 Tags and Structure

HTML (**Hypertext Markup Language**) is the language browsers use to render pages. Structure recap:

- **Open/start tag** and **close/end tag** wrap content:
  ```html
  <p>...</p>
  ```
- The `<script>...</script>` tag tells the browser "execute this JavaScript code."
- Tags are foundational — understanding them is what allows both legitimate developers _and_ adversaries to manipulate how a browser behaves.

### 2.2 Anchor Tags and Hyperlinks

The anchor tag `<a>` creates a hyperlink:

```html
<a href="https://harvard.edu">Harvard</a>
```

- **`href`** ("hyper-reference") is an **attribute** — it tells the browser _where_ to send the user.
- The text between `<a>` and `</a>` is what the **human sees**.
- These two values (displayed text vs. actual destination) do **not** have to match — this mismatch is the root of link-based phishing.
- **Defensive tip:** Hover over a link (without clicking) to preview the real destination URL, typically shown in the browser's bottom-left corner.

---

## 3. Phishing via HTML Manipulation

### 3.1 Mismatched Link Text and URL

Because the displayed text and the `href` value are independent, an attacker can write:

```html
<a href="https://yale.edu">https://harvard.edu</a>
```

The user _sees_ "https://harvard.edu" but clicking sends them to Yale. This alone is often just a prank — the real danger emerges when combined with **typosquatting** and **cloning**.

### 3.2 Typosquatting and Cloned Pages

A more dangerous phishing setup:

1. Attacker registers a domain that is a **near-identical misspelling** of the real one (e.g., a single altered character in "harvard.edu").
2. Attacker **copies the real site's HTML** onto that fake domain.
3. Victim, trusting the visual similarity, **enters credentials** (username/password) into the fake site.
4. Attacker now has the victim's login credentials.

This applies to any high-value target: banks, PayPal, university portals, etc.

### 3.3 Q&A: IP Addresses in URLs

**Q: Could an attacker use a raw IP address instead of a domain name?**

- **Yes.** A URL can point directly to an IP address (e.g., `http://192.0.2.1`) instead of a domain.
- Such links are typically **HTTP, not HTTPS** — a possible red flag for cautious users, but many people won't notice.
- **Best practice for legitimate site owners:** Standardize on a small, consistent set of domain names. Avoid raw IP addresses or inconsistent URL formats — inconsistency trains users to expect "weirdness," making them more vulnerable to future phishing.

---

## 4. Code Injection Attacks

**Code injection** = tricking software into executing code the adversary wrote, that the original developers did not intend to run.

### 4.1 Cross-Site Scripting (XSS) — Reflected

**Scenario:** A search engine (e.g., Google) echoes the user's search query back into the rendered page:

```html
<p>About 6,420,000,000 cats</p>
```

If the server blindly inserts _whatever the user typed_ into that HTML without escaping it, an attacker can search for:

```html
<script>
  alert('attack')
</script>
```

This gets embedded directly into the page as **executable code**, not as visible text:

```html
<p>
  About
  <script>
    alert('attack')
  </script>
</p>
```

The browser doesn't know this came from the _user_ rather than the _website_ — it just executes it.

**Why this is dangerous beyond a harmless alert box:**

- Real payloads could read `document.cookie` and exfiltrate session cookies to an attacker-controlled server.
- This becomes a **reflected XSS attack** when the malicious script is embedded in a URL parameter (e.g., `?q=<script>...</script>`), URL-encoded (e.g., `%3Cscript%3E`), and distributed via a link in an email or malicious website. When the victim clicks it, the server "reflects" the attacker's script back into the victim's own browser session.

**Reflected attack construction example:**

```html
<a href="https://www.google.com/search?q=%3Cscript%3Ealert(document.cookie)%3C/script%3E">cats</a>
```

- The `%3C` / `%3E` sequences are **URL-encoded** `<` and `>` characters (browsers must encode special characters in URLs).
- The server decodes them back to `<script>...</script>` before rendering — and if it doesn't escape them again on output, the script executes in the victim's browser.

### 4.2 Cross-Site Scripting (XSS) — Stored

A **stored XSS attack** differs in that the malicious input is **saved server-side** (e.g., in a database, such as the body of an email) rather than reflected immediately via a URL.

**Example scenario (Gmail-style):**

1. Attacker sends an email containing `<script>alert('attack')</script>` in the body.
2. The email service stores this text in its database.
3. When the **recipient** opens the email, the server renders the stored HTML — if unescaped, the script executes in the recipient's browser.

**Correct behavior:** The application should display the _literal text_ the sender typed (e.g., show the raw `<script>` tag as visible text), not execute it.

### 4.3 Mitigating XSS: Character Escaping

The general solution to both reflected and stored XSS: **escape dangerous characters** before outputting user input into HTML.

**Minimum characters to escape:**

| Character | HTML Entity            |
| --------- | ---------------------- |
| `<`       | `&lt;`                 |
| `>`       | `&gt;`                 |
| `&`       | `&amp;`                |
| `"`       | `&quot;`               |
| `'`       | `&#x27;` (or `&apos;`) |

By converting these characters to their entity equivalents, the browser displays them as literal text instead of interpreting them as the start/end of HTML tags.

### 4.4 Mitigating XSS: Content-Security-Policy Header

An additional defense-in-depth layer: the **Content-Security-Policy (CSP)** HTTP header.

```
Content-Security-Policy: script-src https://example.com
```

- This instructs the browser to **only execute JavaScript** loaded from external `.js` files hosted at the specified domain.
- **Inline `<script>...</script>` blocks are blocked entirely**, even if they accidentally appear in the rendered HTML due to an escaping failure.
- Only this form is permitted:
  ```html
  <script src="https://example.com/app.js"></script>
  ```
- The same mechanism applies to CSS via `style-src`, restricting to external stylesheets:
  ```html
  <link rel="stylesheet" href="https://example.com/style.css" />
  ```
  instead of inline `<style>` blocks.

**Key idea:** CSP is a _second layer of defense_ — even if escaping fails, CSP can prevent the injected script from executing.

### 4.5 Q&A: JavaScript Blocking and React/JSX

**Q: Can users just disable JavaScript to protect themselves?**

- Not realistic — nearly all modern websites rely on JavaScript for core functionality. Disabling it broadly breaks usability rather than serving as a practical defense.

**Q: Does React's JSX (mixing JS and HTML) create a security risk?**

- No, so long as all code is loaded from proper `.js`/`.css` files (as CSP headers would require). The browser executes JavaScript from the React library that _interprets_ JSX and produces safe output — it does not bypass the same underlying script-source rules.

---

## 5. SQL Injection

**SQL (Structured Query Language)** is used to query databases. Applications typically build SQL queries dynamically by inserting user input (e.g., a login username) into a query template — usually via another language like Python, PHP, or Java.

### 5.1 How SQL Queries Are Built

Example query template (pseudocode mixing SQL + Python-style placeholders):

```sql
SELECT * FROM users WHERE username = '{username}'
```

If the user types `malan`, this becomes:

```sql
SELECT * FROM users WHERE username = 'malan'
```

**The danger:** if the input is inserted **without sanitization**, an attacker can break out of the intended string.

### 5.2 Destructive Injection Example (DELETE)

Attacker input for the username field:

```
malan'; DELETE FROM users; --
```

Resulting query (as interpolated):

```sql
SELECT * FROM users WHERE username = 'malan'; DELETE FROM users; --'
```

**Breakdown:**

- `'` — closes the intended string early.
- `;` — ends the current SQL statement (like a period).
- `DELETE FROM users;` — a **second, entirely new command** injected by the attacker.
- `--` — starts a SQL **comment**, causing the database to ignore the trailing (now-unmatched) quote that would otherwise cause a syntax error.

**Result:** the database executes the legitimate SELECT _and_ the attacker's destructive DELETE, wiping out the users table.

### 5.3 Authentication Bypass Injection (`OR '1'='1'`)

Example query template with username **and** password:

```sql
SELECT * FROM users WHERE username = '{username}' AND password = '{password}'
```

Attacker submits:

- Username: `malan`
- Password: `' OR '1'='1`

Resulting query:

```sql
SELECT * FROM users WHERE username = 'malan' AND password = '' OR '1'='1'
```

**Why this works (order of operations):**

- SQL evaluates `AND` before `OR` (similar to multiplication before addition in math).
- So logically: `(username='malan' AND password='')  OR  ('1'='1')`
- Since `'1'='1'` is **always true**, the entire WHERE clause evaluates to true regardless of whether the password matched.
- This causes the query to return **all users** — often including the very first (frequently an administrator) account — potentially letting the attacker log in as an arbitrary or privileged user.

### 5.4 Mitigation: Prepared Statements

**Prepared statements** let the database itself handle safe substitution of user input, rather than the developer manually concatenating strings.

Instead of:

```python
query = f"SELECT * FROM users WHERE username = '{username}'"
```

Use a placeholder (commonly `?`):

```sql
SELECT * FROM users WHERE username = ?
```

The database driver then:

- Automatically wraps the value in proper quotes.
- Automatically **escapes dangerous characters** — notably, a single quote `'` is escaped in SQL by **doubling it** (`''`), not with a backslash as in some other languages.

**Example:** attacker input `malan'; DELETE FROM users; --` becomes safely embedded as a literal string:

```sql
SELECT * FROM users WHERE username = 'malan''; DELETE FROM users; --'
```

The database treats the doubled `''` as an escaped literal apostrophe rather than a string terminator, so the entire attacker payload is treated as **data**, not **code**.

**Rule of thumb:** Never hand-roll your own escaping logic for SQL — use the prepared-statement functionality built into your database library. This mirrors the general security principle: don't reinvent well-solved cryptographic/security primitives.

### 5.5 Q&A: Least-Privilege Database Accounts

**Q: Couldn't you just prevent this by not running queries with admin/root DB privileges?**

- **Yes — this is a valid additional defense**, not a replacement for prepared statements. Restricting the database account used by the application (e.g., no DELETE permission) limits the blast radius of an injection.
- However, some attacks (like the `OR '1'='1'` authentication bypass) only require **SELECT** access, which most applications legitimately need — so privilege restriction alone doesn't stop every attack type.

---

## 6. Command Injection

**Command injection** occurs when software passes user input directly to:

- A **system shell** (via functions like `system()` in many languages), or
- An **`eval()`** function that executes arbitrary code in that language.

**Risk:** If dangerous characters (e.g., semicolons) aren't sanitized, an attacker can terminate the intended command and inject a new one — potentially deleting files, sending spam/email, or executing any command the vulnerable program's user account is permitted to run on the underlying OS.

**Mitigation:** Use the escaping/sanitization functionality that accompanies `system`/`eval`-style functions in your language's documentation — never pass raw, unsanitized user input to shell or eval contexts.

---

## 7. Client-Side vs Server-Side Validation

### 7.1 Developer Tools and the `disabled` Attribute

Browsers include **Developer Tools**, letting any user inspect and **edit** the HTML/CSS/JS sent to their own browser.

**Example — a disabled checkbox:**

```html
<input type="checkbox" disabled />
```

- The `disabled` attribute prevents interaction _in the browser only_.
- A user can open Developer Tools, delete the `disabled` attribute, check the box, and submit the form.
- **If the server blindly trusts** that a submitted checked box means the user was authorized to check it, this is exploitable.

### 7.2 The `required` Attribute

```html
<input type="text" required />
```

- Prevents form submission without a value — but only in the browser.
- A user can remove `required` via Developer Tools and submit an empty/missing value anyway.

**Core takeaway (repeated as the lecture's central rule for this section):**

> Client-side validation is great for **user experience** (instant feedback) but provides **zero real security**, since users fully control their own browser's copy of your HTML/JS. **Server-side validation is mandatory** — client-side validation is "icing on the cake," never the sole line of defense.

---

## 8. Cross-Site Request Forgery (CSRF)

CSRF tricks a **logged-in** user's browser into performing an unintended action on a trusted site (e.g., a purchase), without the user's knowledge or consent.

### 8.1 GET-Based CSRF

**Background:** The **GET** method embeds all request data directly in the URL, and is meant (by web standards) to be "safe" — i.e., it should never change server-side state.

**Vulnerable example — a "Buy Now" link using GET:**

```html
<a href="https://www.amazon.com/dp/B07XLQ2FSK">Buy Now</a>
```

**Escalation — using an `<img>` tag instead of a link:**

```html
<img src="https://www.amazon.com/dp/B07XLQ2FSK" />
```

- Browsers **automatically fetch image sources** without any click.
- If the "image" URL is actually a state-changing action endpoint, and the victim is logged into Amazon in another tab, visiting the attacker's page alone could trigger the purchase — no click required.

**Lesson:** GET should never be used for actions that change server state, precisely because it can be triggered passively (e.g., via an image tag).

### 8.2 POST-Based CSRF (Automated via JavaScript)

Using **POST** instead of GET seems safer since it requires an actual form submission rather than a simple URL fetch:

```html
<form action="https://www.amazon.com/buy" method="POST">
  <input type="hidden" name="dp" value="B07XLQ2FSK" />
  <button type="submit">Buy Now</button>
</form>
```

**But this is still vulnerable**, because JavaScript can **auto-submit the form** without any human clicking the button:

```html
<script>
  document.forms[0].submit()
</script>
```

If a victim merely visits the attacker's page while already logged into Amazon, the hidden form silently submits and the "purchase" (or other state-changing action) executes — the "cross-site" and "forgery" elements of CSRF: the request looks legitimate and carries valid session credentials, but its origin is fraudulent.

### 8.3 Mitigation: CSRF Tokens

**Solution:** The server embeds a **random, unpredictable token** (the "CSRF token") as a hidden field in every legitimate form it serves, and remembers what token belongs to which user/session:

```html
<input type="hidden" name="csrf_token" value="1234abcd" />
```

- Only the real server knows the correct token value for a given user at a given time.
- An attacker's forged form (hosted on their own malicious site) has **no way to know or guess** this token.
- The server rejects any submitted request whose token doesn't match what it has on record for that user.
- CSRF tokens can also be transmitted via **HTTP headers** instead of hidden form fields — common in JavaScript-heavy apps that talk directly to APIs without full page reloads.

**Further reading recommendation from the lecture:** OWASP (**Open Worldwide Application Security Project**) — documentation and recommendations for these and other web-centric attack classes.

### 8.4 Q&A: AI and Quantum Computing

**Q: How might AI and quantum computing change cybersecurity?**

- Deferred as beyond scope for this session, but noted briefly: _quantum computing is a threat primarily if adversaries have access to it and defenders do not_ — i.e., it's an asymmetry-of-capability concern (e.g., for breaking current cryptographic schemes).

---

## 9. Local/System-Level Attacks

Shifting focus from web-based attacks to attacks on **local software** running on a Mac, PC, or phone.

### 9.1 Arbitrary & Remote Code Execution

- **Arbitrary code execution (ACE):** an adversary tricks a system into running code the adversary wrote, not code that was part of the original software.
- **Remote code execution (RCE):** same concept, but the adversary triggers it from a remote location (e.g., over a network/internet connection) rather than having local access.

### 9.2 Buffer Overflow Mechanics

**Mental model of memory:**

- A running program's **machine code** (compiled instructions) sits in one region of memory.
- A region called the **stack** is used for temporary data as the program runs — by convention, the stack grows **upward** from the bottom of allocated memory as more is needed.
- Each function call typically stores a **return address** — essentially a "note to self" telling the program where to resume execution once the current function finishes (analogous to remembering where you were in a task before being interrupted).

**How a buffer overflow happens:**

1. A program reserves a fixed-size block ("buffer" or stack "frame") for expected user input (e.g., a search term).
2. If the programmer's estimate of maximum input size is wrong, and the user (or attacker) supplies **more data than the buffer can hold**, the excess data **overflows** into adjacent memory — potentially **overwriting the stored return address**.
3. If the attacker crafts the overflow carefully (through trial and error), they can:
   - Inject their own **malicious machine code** ("attack code") into memory, and
   - Overwrite the return address to **point to that injected code** instead of the legitimate next instruction.
4. When the function finishes and the program "returns," it jumps to the attacker's code instead of the intended location — executing arbitrary instructions with the same permissions as the vulnerable program/user.

**Trivia note:** The name of the well-known programmer Q&A site **Stack Overflow** is a direct allusion to this class of bug.

**Real-world consequences:** file deletion, unauthorized software activation/license bypass, spam sending, or any action the compromised program's user account is capable of performing.

### 9.3 Cracking and Reverse Engineering

- **Cracking:** commonly refers to bypassing licensing/activation requirements, or more broadly breaking into software/systems, often by manipulating compiled machine code directly.
- **Reverse engineering:** the process of analyzing compiled (binary) software to deduce its original design/logic, typically via trial and error and pattern analysis of the "zeros and ones."
  - **Defensive/beneficial use case:** _malware analysis_ — security researchers reverse-engineer malicious software to understand its behavior and build antivirus/detection signatures.

---

## 10. Defensive Ecosystem & Trust Models

### 10.1 Open Source vs Closed Source

| Aspect                     | Open Source                                                                   | Closed Source                                       |
| -------------------------- | ----------------------------------------------------------------------------- | --------------------------------------------------- |
| Code visibility            | Publicly viewable by anyone                                                   | Only accessible to the company/developer            |
| Auditability               | Community can review for backdoors/bugs                                       | No public audit possible                            |
| Risk of blueprint exposure | Adversaries can also study the code (like "giving them the Death Star plans") | Adversaries lack direct insight into implementation |
| Bug discovery              | Potentially more eyes → more bugs found (by good _and_ bad actors)            | Fewer eyes; bugs may go unnoticed longer            |

**Caveat:** Even legitimate open-source software can be **repackaged maliciously** by an attacker who tricks users (via phishing or fake sites) into downloading a tampered version — so open-source status alone isn't a complete guarantee of safety.

### 10.2 App Stores & Digital Signatures

App stores (Apple, Google, Microsoft, etc.) add a layer of trust by vetting submitted software before distribution, using **cryptographic building blocks** covered in earlier lessons:

**Developer side:**

1. Developer computes a **hash** of their software (a fixed-length, near-unique fingerprint).
2. Developer signs that hash with their **private key**, producing a **digital signature**.
3. The developer's **public key** was pre-registered with the app store, allowing signature verification (proving authorship).

**App store side:**

1. The app store independently hashes the submitted software.
2. The app store signs that hash with **its own private key**.
3. When a user installs the software, their device verifies the **app store's signature** — confirming the app was vetted/distributed by that trusted store, not tampered with afterward.

**Result:** Unsigned or unauthorized third-party software typically triggers OS-level warnings (e.g., "this app is from an unidentified developer") — a direct application of hashing + digital signatures from earlier cryptography lessons.

### 10.3 Package Managers

Similar signature-verification concepts appear in developer-focused **package managers**:

- `pip` — Python
- `gem` — Ruby
- `npm` — Node.js
- `apt` — Linux (Debian/Ubuntu-based)

These tools typically use digital signatures to let installing systems verify package authenticity before installation, and modern operating systems increasingly build in native support for such verification.

**Important caveat:** Trust is not static — software that was safe in version 1 or 2 might become malicious in a later version if:

- The developer's intent changes,
- Ownership of the project is sold to a less trustworthy party, or
- The developer's private key/account is compromised.

### 10.4 Bug Bounty Programs

**Bug bounties** are a model where companies pay independent researchers/hackers who **responsibly disclose** security vulnerabilities (privately, to the company, before public disclosure) rather than exploit or sell them.

**Rationale:** Channels the skills of people who might otherwise exploit vulnerabilities maliciously (e.g., via ransomware) into a **win-win** arrangement — the company fixes the bug, and the researcher is compensated. Payouts are often scaled to the **severity** of the discovered bug.

---

## 11. Vulnerability Tracking Standards

A set of standardized systems exists for cataloging and prioritizing software vulnerabilities industry-wide:

| Acronym  | Full Name                                 | Purpose                                                                                    |
| -------- | ----------------------------------------- | ------------------------------------------------------------------------------------------ |
| **CVE**  | Common Vulnerabilities and Exposures      | Assigns a unique identifier to a specific known bug/flaw in specific software/versions     |
| **CVSS** | Common Vulnerability Scoring System       | Standardized severity score for a vulnerability (how bad is it?)                           |
| **EPSS** | Exploit Prediction Scoring System         | Estimates the real-world probability that a given vulnerability will actually be exploited |
| **KEV**  | Known Exploited Vulnerabilities (catalog) | Tracks vulnerabilities **confirmed** to have been exploited in the wild                    |

**Purpose for IT/security professionals:** With limited time and resources, these systems help prioritize **which vulnerabilities to patch first** — balancing severity (CVSS) against real-world exploitation likelihood (EPSS) and confirmed exploitation history (KEV).

---

## 12. Key Terms Glossary

| Term                                  | Definition                                                                                                                           |
| ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| **HTML**                              | Hypertext Markup Language; language for structuring web pages using tags                                                             |
| **Attribute**                         | A property of an HTML tag (e.g., `href`, `disabled`, `required`) that modifies its behavior                                          |
| **XSS (Cross-Site Scripting)**        | Injecting attacker-controlled JavaScript into a page viewed by other users                                                           |
| **Reflected XSS**                     | Malicious script delivered via a crafted URL/request and immediately echoed back by the server                                       |
| **Stored XSS**                        | Malicious script saved server-side (e.g., in a database) and later served to other users                                             |
| **Character escaping**                | Converting dangerous characters (like `<`, `>`, `'`) into safe representations (HTML entities) before rendering                      |
| **Content-Security-Policy (CSP)**     | HTTP header restricting which sources of scripts/styles a browser will execute                                                       |
| **SQL Injection**                     | Manipulating a SQL query by injecting unsanitized input containing SQL syntax                                                        |
| **Prepared statement**                | A parameterized SQL query where the database safely substitutes/escapes user input, rather than raw string concatenation             |
| **Command Injection**                 | Injecting OS-level shell commands via unsanitized input passed to functions like `system()` or `eval()`                              |
| **Client-side validation**            | Input validation performed in the browser (JS/HTML); improves UX but is not trustworthy for security                                 |
| **Server-side validation**            | Input validation performed on the server; the only validation that can be trusted for security                                       |
| **CSRF (Cross-Site Request Forgery)** | Tricking a logged-in user's browser into unknowingly performing a state-changing action on another site                              |
| **CSRF token**                        | A random, per-user/session secret embedded in forms to verify a request genuinely originated from the legitimate site                |
| **GET / POST**                        | HTTP methods; GET should be "safe" (no state change, data in URL); POST is used for state-changing/larger data submissions           |
| **Buffer overflow**                   | Writing more data into a fixed-size memory region than it can hold, potentially overwriting adjacent memory (e.g., a return address) |
| **Arbitrary Code Execution (ACE)**    | Successfully tricking a system into executing attacker-supplied code                                                                 |
| **Remote Code Execution (RCE)**       | ACE triggered remotely, e.g., over a network                                                                                         |
| **Reverse engineering**               | Analyzing compiled/binary software to deduce its internal logic or design                                                            |
| **Digital signature**                 | Cryptographic proof (via private key + hashing) that a piece of data/software originated from a specific, verified source            |
| **CVE / CVSS / EPSS / KEV**           | Standardized systems for identifying, scoring, and tracking real-world exploitation of software vulnerabilities                      |
| **OWASP**                             | Open Worldwide Application Security Project — a resource for web application security documentation and best practices               |

---

## 📌 Overarching Lessons of the Lecture

1. **Never trust user input** — anywhere it flows into HTML, SQL, shell commands, or memory buffers.
2. **Escape/sanitize on the server**, not just the client — client-side checks are cosmetic, not security.
3. **Defense in depth** — combine multiple layers (e.g., escaping _and_ CSP headers; prepared statements _and_ least-privilege DB accounts; digital signatures _and_ app store review).
4. **Don't reinvent security primitives** — use established libraries/frameworks (prepared statements, escaping functions, CSRF token libraries) instead of writing custom sanitization logic.
5. **Trust is layered and revocable** — open source, app stores, and package managers all _reduce_ risk but never eliminate it; a previously safe piece of software can become malicious later.
6. **Standardized vulnerability tracking (CVE/CVSS/EPSS/KEV)** helps organizations prioritize limited security resources against a very large threat landscape.

---

_End of Part 1 notes. Part 2 will cover privacy._
