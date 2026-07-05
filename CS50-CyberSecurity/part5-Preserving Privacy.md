# CS50 Introduction to Cybersecurity — Preserving Privacy

### Lecture Notes — David J. Malan

---

## 📑 Table of Contents

1. [Introduction & Framing](#1-introduction--framing)
2. [Web Browsing History](#2-web-browsing-history)
   - 2.1 [Client-Side History (Your Browser)](#21-client-side-history-your-browser)
   - 2.2 [Server-Side Logs](#22-server-side-logs)
3. [HTTP Headers That Threaten Privacy](#3-http-headers-that-threaten-privacy)
   - 3.1 [The `Referer` Header](#31-the-referer-header)
   - 3.2 [Mitigating Referrer Leakage](#32-mitigating-referrer-leakage)
   - 3.3 [Q&A: Client-Side Scrubbing Tools](#33-qa-client-side-scrubbing-tools)
   - 3.4 [Q&A: Tor/Tails and Residual Traces](#34-qa-tortails-and-residual-traces)
   - 3.5 [Q&A: Trusting Privacy Software Itself](#35-qa-trusting-privacy-software-itself)
4. [Browser Fingerprinting](#4-browser-fingerprinting)
   - 4.1 [Fingerprinting Signals](#41-fingerprinting-signals)
   - 4.2 [Q&A: VPNs and Fingerprinting](#42-qa-vpns-and-fingerprinting)
   - 4.3 [Q&A: Fingerprint Theft / Impersonation](#43-qa-fingerprint-theft--impersonation)
   - 4.4 [Q&A: Mobile vs Desktop Fingerprinting](#44-qa-mobile-vs-desktop-fingerprinting)
   - 4.5 [Q&A: Cookies vs Local Storage](#45-qa-cookies-vs-local-storage)
   - 4.6 [Aside: Phone Number Spoofing](#46-aside-phone-number-spoofing)
5. [Cookies in Detail](#5-cookies-in-detail)
   - 5.1 [Session Cookies](#51-session-cookies)
   - 5.2 [Tracking Cookies](#52-tracking-cookies)
   - 5.3 [Tracking Parameters (URL Parameters)](#53-tracking-parameters-url-parameters)
   - 5.4 [Q&A: Which Browsers Are More Privacy-Conscious?](#54-qa-which-browsers-are-more-privacy-conscious)
6. [Third-Party Cookies](#6-third-party-cookies)
   - 6.1 [How Cross-Site Tracking Works](#61-how-cross-site-tracking-works)
   - 6.2 [Why Third Parties Become More Powerful Than First Parties](#62-why-third-parties-become-more-powerful-than-first-parties)
7. [Private/Incognito Browsing](#7-privateincognito-browsing)
8. [Super Cookies](#8-super-cookies)
   - 8.1 [Q&A: Cookie Theft and Impersonation](#81-qa-cookie-theft-and-impersonation)
   - 8.2 [Q&A: SMS/Messaging Interception](#82-qa-smsmessaging-interception)
9. [DNS and Privacy](#9-dns-and-privacy)
   - 9.1 [How DNS Works](#91-how-dns-works)
   - 9.2 [Why DNS Leaks Privacy](#92-why-dns-leaks-privacy)
   - 9.3 [Encrypted DNS: DoH and DoT](#93-encrypted-dns-doh-and-dot)
   - 9.4 [Q&A: Malicious/Deceptive DNS](#94-qa-maliciousdeceptive-dns)
10. [VPNs (Virtual Private Networks)](#10-vpns-virtual-private-networks)
11. [Tor (The Onion Router)](#11-tor-the-onion-router)
12. [App Permissions](#12-app-permissions)
13. [Course Wrap-Up](#13-course-wrap-up)
14. [Key Terms Glossary](#14-key-terms-glossary)

---

## 1. Introduction & Framing

This lecture shifts focus from **securing** communications (keeping a channel between point A and point B safe from eavesdroppers) to **preserving privacy** — the question of whether point A (you) even wants point B (a website, ISP, advertiser, etc.) to have certain information **at all**, security aside.

**Core distinction:**

- **Security** = protecting data _in transit/storage_ from unauthorized third parties.
- **Privacy** = controlling what data the _legitimate_ other party (and hidden third parties) get to see or retain in the first place.

---

## 2. Web Browsing History

### 2.1 Client-Side History (Your Browser)

- Browsers keep a running history of visited URLs — visible via the URL bar's dropdown or a full "History" menu.
- **Useful features enabled by this:**
  - Quickly refinding pages visited earlier.
  - **Autocomplete** — browser predicts a URL/page from partial typing.
- **Privacy risk:** Anyone with physical access to your device (or a shared/public computer, e.g., in a lab or internet café) can see everywhere you've been.
- **Common mitigation:** "Clear browsing history" — but this is a **blunt instrument**:
  - Deletes visited-site history, cookies, and saved logins **all at once**.
  - Side effect: you may get logged out of accounts (Google, Outlook, etc.) you didn't intend to affect.

### 2.2 Server-Side Logs

Even if you clear your own local history, **the websites you visited likely kept their own logs** server-side. Clearing your browser changes nothing about what's stored on someone else's server.

**Purposes of server logs:**

- Diagnostics (troubleshooting when something breaks).
- Auditing (tracking exactly what was accessed and by whom).
- Advertising / analytics (monetizing or analyzing user behavior).

**Common log format example — the "combined" log format** typically captures:

| Field          | Description                      |
| -------------- | -------------------------------- |
| Remote address | Your IP address                  |
| Date/time      | When you accessed the page       |
| Request        | The specific file/path requested |
| Referrer       | The URL you came from            |
| User agent     | Your browser + OS information    |

**Key point:** You generally have **little to no control** over deleting this server-side data — unless a specific law or regulation mandates it. This sets up the rest of the lecture: much of what follows examines the _specific pieces of information_ leaking into these logs and how to reduce that leakage.

---

## 3. HTTP Headers That Threaten Privacy

### 3.1 The `Referer` Header

**Scenario:** Searching Google for "cats" produces a result linking to `example.com`:

```html
<a href="https://example.com">cats</a>
```

When a user clicks that link, the browser automatically sends an HTTP header to `example.com`:

```
Referer: https://www.google.com/search?q=cats
```

**Implication:** The destination website learns:

- What website you came from (Google, not Bing, etc.)
- **The exact search query** that led you there (i.e., not just "you came from Google" but "you searched for cats").

**Fun historical trivia:** The header name **`Referer`** is a well-known **misspelling** in the original HTTP specification (missing one "r" — it should be "Referrer" with 4 r's). The typo has persisted in the actual HTTP header name for decades, even though newer/related mechanisms use the correct spelling.

**Why this matters commercially (for the receiving site) vs. invasively (for the user):**

- Useful for site owners: analytics on where customers/traffic originate.
- Invasive for users: reveals search habits, browsing intent, and platform choice (e.g., "uses Google, not Bing") to every site clicked into.

### 3.2 Mitigating Referrer Leakage

**Two mechanisms to reduce/eliminate referrer information:**

**A. HTML `<meta>` tag (site-controlled, applies to that page's outbound links):**

```html
<meta name="referrer" content="origin" />
```

- Sends only the **origin** (e.g., `https://www.google.com/`) — domain + protocol, no path or query string.

```html
<meta name="referrer" content="none" />
```

- Sends **no referrer at all**.

_(Note: Google currently uses `origin`, not `none`.)_

**B. HTTP `Referrer-Policy` header (server-controlled, sent from server to browser):**

```
Referrer-Policy: origin
```

or

```
Referrer-Policy: no-referrer
```

Both approaches let a site owner voluntarily reduce the information their own outbound links leak about their users.

### 3.3 Q&A: Client-Side Scrubbing Tools

**Q: Can users scrub this information themselves as a client, rather than relying on websites to be considerate?**

- **Yes** — third-party client-side software exists (installed on your Mac/PC) that automatically strips excess header information (like full referrer URLs) from outgoing HTTP requests, so you don't have to trust that every website will voluntarily reduce what it collects.

### 3.4 Q&A: Tor/Tails and Residual Traces

**Q: Does using Tor or a live/temporary OS like Tails remove all internet traces?**

- **No** — some evidence remains on **both the client side and server side**, even with these tools. (Tor is covered in more depth later in the lecture — see [Section 11](#11-tor-the-onion-router).)

### 3.5 Q&A: Trusting Privacy Software Itself

**Q: What if the third-party "privacy scrubbing" software itself spies on you?**

- A **valid and real concern**. Mitigating factors (not guarantees):
  - **Open-source software** — more likely to be free of malicious behavior since the code can be publicly audited.
  - **Market/reputational incentives** — a company caught violating user privacy risks backlash that harms its business, discouraging bad behavior.
  - Ultimately, **installing any software requires a degree of trust** — this is an unavoidable risk inherent to using third-party tools.

---

## 4. Browser Fingerprinting

**Fingerprinting** = using a collection of request/browser characteristics to build a unique (or near-unique) profile that can re-identify the _same_ user across visits — even without cookies or explicit login, and even without literally knowing the person's identity (e.g., "David Malan"), just that "this is the same visitor as yesterday."

### 4.1 Fingerprinting Signals

| Signal                         | Notes                                                                                                 |
| ------------------------------ | ----------------------------------------------------------------------------------------------------- |
| **IP address**                 | Simplest identifier; not always unique (shared across homes/campuses/companies), but narrows the pool |
| **User-Agent header**          | A long string identifying browser type/version + OS type/version                                      |
| **Screen resolution**          | Especially identifying if you consistently use full-screen mode                                       |
| **Installed fonts**            | Queried via code (HTML/CSS/JS) sent to your browser                                                   |
| **Time zone**                  | Available to JavaScript running in-browser                                                            |
| **Browser extensions/plugins** | Another differentiating characteristic                                                                |

**Example User-Agent string (illustrative, deliberately verbose):**

```
Mozilla/5.0 (Linux; Android 10; SM-G975F) AppleWebKit/537.36 (KHTML, like Gecko)
Chrome/91.0.4472.120 Mobile Safari/537.36
```

**Retroactive identification risk:** Even if a server didn't know "who" a given fingerprint was for days/months, the moment that same fingerprint is associated with an actual login **once**, the server can retroactively infer the entire history of activity tied to that fingerprint likely belongs to that same, now-identified, person.

### 4.2 Q&A: VPNs and Fingerprinting

**Q: Will a VPN prevent fingerprinting?**

- **No.** A VPN only masks/changes your **IP address**. All other fingerprinting signals (User-Agent, screen resolution, fonts, time zone, plugins, etc.) remain exactly the same and can still be used to re-identify you.

### 4.3 Q&A: Fingerprint Theft / Impersonation

**Q: Could a hacker steal someone's fingerprint and impersonate them (making actions look like they came from the victim's computer)?**

- **Yes, if the attacker has access to the same identifying information.**
- **Mitigating factor:** If all traffic is exclusively via **HTTPS**, a man-in-the-middle attacker between you and the server **cannot see** most of this information (headers, HTML, JS, CSS are encrypted) — only your IP address remains visible in the clear.
- **If the attacker has compromised your device directly (or the server)**, however, "all bets are off" — they can access and replicate this information freely.

### 4.4 Q&A: Mobile vs Desktop Fingerprinting

**Q: Can more fingerprinting information be gathered from mobile devices than desktops?**

- Malan's view: not dramatically more, though phones have **additional hardware sensors** (GPS, accelerometer, gyroscope) that JavaScript _could_ query — typically gated behind **user opt-in permission prompts**. These additional signals could contribute to more unique identification versus a typical laptop, which usually lacks this hardware.

### 4.5 Q&A: Cookies vs Local Storage

**Q: Is it more secure to use cookies or local storage for client-side data?**

- **Local storage is generally preferable**, because:
  - Cookies are, by design, **automatically sent back and forth** between browser and server on every request — a potential (if theoretically encrypted-away) leak point, especially if a connection accidentally starts on HTTP before redirecting to HTTPS.
  - Local storage data stays local and isn't transmitted automatically.
- **Caveat:** Local storage is **not safer against physical access** — anyone with hands-on access to the device (a colleague, sibling, or adversary) can read local storage just as easily as cookies. Physical access defeats both.

### 4.6 Aside: Phone Number Spoofing

_(Raised in Q&A, tangential to but related to privacy/identity trust.)_

- Telephone numbers are **trivially easy to spoof**, which is how many international spam calls operate.
- A known tactic: spoofing a caller ID with the **same area code/prefix as the recipient** (e.g., matching "555" prefix), banking on the recipient being more likely to answer a call that looks local/familiar.
- **Reinforces an earlier course lesson:** SMS/phone-based systems are insecure and **not recommended for multi-factor authentication**, since the underlying telephone network (designed over a century ago) offers no cryptographic guarantees.

---

## 5. Cookies in Detail

A **cookie** = a piece of data a server stores on your computer (via the browser) to help re-identify you across requests — a "virtual handstamp." HTTP itself is stateless; cookies are the mechanism used to simulate persistent state.

### 5.1 Session Cookies

**Purpose:** Maintain state between browser and server — e.g., staying logged in, preserving a shopping cart, across multiple page loads.

**Example exchange:**

Server response (setting the cookie):

```
HTTP/1.1 200 OK
Set-Cookie: session=1234abcd
```

Subsequent browser request (presenting the "handstamp"):

```
GET /page HTTP/1.1
Cookie: session=1234abcd
```

**Key design intent:** Session cookies are meant to be **short-lived** — ideally expiring when the browser closes, is quit, or the device restarts. (In practice, modern browsers that preserve tabs across restarts blur this boundary somewhat, but the underlying design goal is short-term persistence.)

**Privacy upside:** Because sessions are meant to expire quickly, a site generally can't correlate today's visit with tomorrow's visit purely via a session cookie — you effectively look "new" again.

### 5.2 Tracking Cookies

**Purpose:** Deliberately **track** users, often for analytics, debugging, or (most controversially) **targeted advertising**.

**Example — Google Analytics cookie:**

```
Set-Cookie: _ga=GA1.2.XXXXXXXXX.XXXXXXXXXX; Max-Age=63072000
```

- `_ga` is the by-convention cookie name Google Analytics uses.
- `Max-Age=63072000` seconds ≈ **2 years** — vastly longer-lived than a session cookie, by design, since the point is long-term identification, not short-term convenience.

**Contrast with session cookies:** Tracking cookies are engineered for _longevity_ and _cross-visit correlation_ rather than short-term usability.

### 5.3 Tracking Parameters (URL Parameters)

Beyond cookies (which live in HTTP headers, less visible to casual users), sites/advertisers also track via **URL query parameters** — visible directly in the address bar.

**Example URL:**

```
https://example.com/ad_engagement?click_id=AbCdEfGhIjKlMnOp&campaign_id=23
```

- `campaign_id=23` — a small number identifying _which ad campaign_ to serve; not itself identifying of the individual user.
- `click_id=...` — a long, effectively random string that **uniquely identifies the specific click/user** — functionally a "tracking cookie" hiding in plain sight in the URL.

**Mitigation trend:** Modern browsers and third-party tools increasingly **auto-strip known tracking parameters** from URLs before the user even clicks anything, since the browser can inspect the page's HTML in advance. **Limitation:** This only works if the tracking parameter's _name_ is known/recognized — sites that constantly rename or obfuscate parameters can evade this filtering.

### 5.4 Q&A: Which Browsers Are More Privacy-Conscious?

**Q: What browsers are best regarding tracking parameters/privacy?** _(Note: Malan reframes this as "privacy-conscious" rather than "more secure" — encryption/HTTPS security is roughly equivalent across major browsers; the differentiator here is privacy behavior.)_

Malan's informal ranking (most → least privacy-conscious, per the lecture):

1. **Safari** — historically strong on privacy; recently announced automatic stripping of tracking parameters from URLs (the example URL used in the lecture was drawn from this Apple announcement).
2. **DuckDuckGo** (browser) — the most popular privacy-focused third-party browser.
3. **Brave** — also privacy-focused.
4. **Firefox** / **Edge** — reasonably privacy-conscious (Malan notes he "should have put them higher").
5. **Chrome** — likely the **least** privacy-conscious of the major browsers, since Google's core business model is built on monetizing user behavior data (despite Malan admitting personal use of it for ecosystem integration reasons).

---

## 6. Third-Party Cookies

**First-party** = the website you deliberately chose to visit (e.g., harvard.edu).
**Third-party** = an embedded external service on that page (e.g., Google Analytics, an ad network) that also gets to set/read its own cookies.

**Browsers increasingly let users disable third-party cookies** — a meaningful privacy improvement, though it does **not** stop tracking via URL parameters (a separate mechanism — see 5.3).

### 6.1 How Cross-Site Tracking Works

**Illustrative scenario — Harvard, Yale, and Stanford all embed the same third-party ad/analytics service (`example.com`):**

Each university's HTML includes something like:

```html
<html>
  <head>
    <title>Harvard</title>
  </head>
  <body>
    <img src="https://example.com/ad.gif" />
  </body>
</html>
```

_(Same structure repeated for Yale and Stanford, just with the page `<title>` changed.)_

**Step-by-step tracking mechanism:**

1. User visits **harvard.edu**. Browser loads Harvard's HTML, sees the `<img>` tag, and automatically fetches `ad.gif` from `example.com` — sending a `Referer` header identifying Harvard as the source.
2. `example.com` responds with the image **and** sets a cookie:
   ```
   Set-Cookie: id=1234abcd; Max-Age=31536000
   ```
   (Max-Age ≈ 1 year.)
3. Later, the user visits **yale.edu**. Yale's page also requests the same ad from `example.com`. The browser **automatically re-presents** the previously-set cookie (`id=1234abcd`) along with a new `Referer: yale.edu`.
4. Same again for **stanford.edu**.

**Result:** `example.com` now knows, via a single persistent cookie, that the _same_ user visited Harvard, then Yale, then Stanford — **without any of those three universities knowing about each other's visits.**

### 6.2 Why Third Parties Become More Powerful Than First Parties

Because a small number of third parties (e.g., major ad/analytics networks) are embedded across an enormous number of unrelated first-party sites, that third party accumulates a **cross-site view of user behavior** that no single first-party site possesses — making the "third party" effectively more omniscient about a user's overall browsing habits than any individual website they visit.

---

## 7. Private/Incognito Browsing

**What it does:**

- Opens a browsing session with **no access** to your existing history, cookies, or saved logins — effectively a fresh, isolated memory space.
- When the private/incognito window is **closed**, that session's history, cookies, and cache are **discarded** from your device.

**What it does NOT do:**

- It does **not** prevent tracking parameters, tracking cookies (during that session), or **server-side logging** from occurring during the session itself — it only prevents **persistence on your local device** afterward.
- It does **not** defeat **fingerprinting** — IP address and other browser characteristics are still exposed during the session.
- It is **entirely client-side** — the server has no awareness that you're in a private window and may still log/track you normally during that session; it simply won't recognize you as a "returning" user next time (unless fingerprinting succeeds).

**Legitimate secondary use case:** Web developers commonly use private/incognito windows during development/testing, to simulate a "fresh" user with no cached state or prior bugs/cookies interfering.

---

## 8. Super Cookies

**Super cookies** are considered the **most invasive** cookie-related privacy threat covered in the lecture — worse than ordinary tracking cookies.

**Mechanism:**

- Injected not by the website you're visiting, nor by your own browser, but by an intermediary — commonly a **mobile carrier** or **ISP** — that intercepts your (unencrypted) HTTP traffic and **inserts its own tracking header/cookie** directly into the request as it passes through their infrastructure.

```
Set-Cookie: id=1234abcd
```

_(injected by the ISP/carrier itself, mid-transit)_

**Why this is especially dangerous for privacy:**

- This is effectively a **man-in-the-middle** modification of your traffic.
- **Clearing your own cookies, browsing history, or using incognito mode has NO EFFECT** — the injected value never originated from your device in the first place; it's added externally, in transit.

**Primary defense: Always use HTTPS.**

- If the connection is encrypted end-to-end, the ISP/carrier **cannot see or modify** the contents of the request — they lack the encryption key needed to insert anything into the encrypted payload.
- **Secondary defense:** Some carriers (particularly in the US, after public backlash) offer an opt-out setting — though the lecture notes these options are often **deliberately difficult to locate** in carrier account settings/menus.

### 8.1 Q&A: Cookie Theft and Impersonation

**Q: Since cookies can store passwords/emails, could an adversary steal and reuse a cookie to impersonate someone?**

- **In principle, a real risk** — especially if cookies literally store credentials (username, email, password), which Malan advises **against** doing in the first place.
- Cookies transmitted over HTTPS are protected in transit, but **physical access** to a device makes it trivially easy to inspect stored cookies directly (e.g., via browser dev tools), exposing anything stored inside.
- **Better design practice for developers:**
  - Store only a **random opaque identifier** (the "handstamp") in the cookie.
  - Keep the actual username/email/password associated with that ID **on the server only**, not duplicated inside the cookie itself.

### 8.2 Q&A: SMS/Messaging Interception

**Q: Is it possible to intercept and alter a text message to ask for sensitive information on someone's behalf? Do messaging apps use cookies for this?**

- **Traditional SMS is generally insecure** — phone numbers are easy to spoof, and techniques like **SIM swapping/porting** can let an attacker intercept a victim's actual text messages.
- **Modern messaging apps** (iMessage, WhatsApp, Signal, Telegram, etc.) largely rely on **end-to-end encryption** (a cryptographic concept from earlier in the course) — meaning even the company operating the service (e.g., Meta/Facebook for WhatsApp) cannot read message contents, assuming correct implementation and honest operation.
- **Cookies are unrelated to this protection** — the security of end-to-end encrypted messaging comes entirely from cryptography (encryption + digital signatures), not from cookie mechanisms.

---

## 9. DNS and Privacy

### 9.1 How DNS Works

**DNS (Domain Name System)** translates human-friendly domain names (e.g., `harvard.edu`) into machine-usable **IP addresses**, sparing humans from memorizing numeric addresses — analogous to how phone mnemonics (e.g., the old US "1-800-COLLECT") were easier to remember than raw phone numbers.

**Lookup hierarchy (simplified):**

1. Your home router / local network device may have a built-in DNS resolver.
2. If it doesn't know the answer, it asks your **Internet Service Provider (ISP)**'s DNS server.
3. If the ISP doesn't know, the query continues up a **hierarchical system** of DNS servers until an authoritative answer is found.
4. Answers are typically **cached** at each level (router, ISP, device, browser) for efficiency, avoiding repeated lookups.

**Convention:** DNS traffic traditionally uses **port 53** (compare: HTTP=80, HTTPS=443, SSH=22).

### 9.2 Why DNS Leaks Privacy

**The core problem: traditional DNS traffic is unencrypted.**

- Every time your device asks "what is the IP address for `harvard.edu`?", that plaintext query — and the plaintext answer — passes through your local network and ISP **in the clear**.
- This means your **ISP (home or mobile) effectively knows every domain you've ever visited**, assuming they log DNS queries (which they typically do, absent legal restriction).
- **Important nuance:** DNS only reveals the **domain** (e.g., "harvard.edu"), not the **specific page, path, or query string** you accessed on that domain — that finer-grained detail would come from HTTP-layer information (URLs, referrers), not DNS itself. Still, knowing _which domains_ you visit is itself a meaningful privacy leak.

### 9.3 Encrypted DNS: DoH and DoT

Two modern alternatives to plaintext DNS:

| Protocol | Full Name      | Mechanism                                                                                         |
| -------- | -------------- | ------------------------------------------------------------------------------------------------- |
| **DoH**  | DNS over HTTPS | DNS queries sent using HTTP but encrypted via TLS, so the query looks like ordinary HTTPS traffic |
| **DoT**  | DNS over TLS   | DNS queries encrypted via TLS directly, without wrapping in HTTP                                  |

**What this protects against:** Your **ISP** (or a public Wi-Fi provider, coffee shop, airport, etc.) can no longer see which domains you're resolving.

**What this does NOT eliminate:** Whichever party you **do** send the encrypted DNS query to (e.g., Google, Cloudflare, or another third-party DNS resolver) **does** still know what you're looking up — you're simply choosing to trust a different, potentially more centralized, party instead of your local network provider.

**Practical relevance:** Especially useful on **untrusted networks** (coffee shops, airports, hotel Wi-Fi), where the local network operator effectively becomes your "ISP" for that session and would otherwise see all your DNS lookups.

### 9.4 Q&A: Malicious/Deceptive DNS

**Q: Can DNS be used to deceive users and steal sensitive information?**

- **Yes, absolutely** — whoever controls a DNS server does not have to answer truthfully. A malicious/compromised DNS server could return the IP address of an attacker-controlled server instead of the legitimate one for any queried domain.
- **HTTPS provides a partial safeguard:** if a browser is redirected to a fraudulent server via DNS manipulation, that server would need a **valid TLS certificate** for the real domain to avoid triggering a browser warning. A mismatched or improperly-signed certificate causes the connection to fail/warn the user — though users can sometimes click through such warnings, undermining the protection.
- **Related real-world behavior (not necessarily malicious):** Some ISPs, coffee shops, and airports intentionally return **their own server's IP address** even for **mistyped/nonexistent domains**, redirecting users to search results or advertisements rather than a proper "domain not found" error.

---

## 10. VPNs (Virtual Private Networks)

**What a VPN actually does:**

- Creates an **encrypted tunnel** between point A (you) and point B (a VPN server/endpoint), regardless of how many intermediate machines the traffic physically passes through on the internet.
- Enables secure access to restricted resources — e.g., a corporate or university network only reachable "virtually" from off-campus.
- Makes your traffic **appear to originate from point B's location** — useful for accessing geographically-restricted services or masking your real IP/location from external websites.

**What a VPN does NOT do:**

- **Does not protect against malware already on your device** — an infected computer remains infected regardless of an encrypted VPN tunnel; you may just be extending an "infected connection" to your company/campus network instead of stopping the infection.
- **Does not guarantee encryption beyond point B** — once traffic exits the VPN tunnel at its endpoint, it continues onward using whatever protocol was originally specified (HTTP vs HTTPS); the VPN only secures the A→B segment.
- **Does not defeat browser fingerprinting** — while your IP address changes, other identifying characteristics (User-Agent, screen resolution, fonts, etc.) remain unchanged and could still allow re-identification, even if your apparent location/IP has shifted.

---

## 11. Tor (The Onion Router)

**Tor** is software providing a different privacy approach: routing your traffic through multiple volunteer-operated relay nodes with **layered ("onion") encryption**.

**Mechanism:**

1. Your Tor client identifies a path through several other Tor-running computers on the network (e.g., three intermediate nodes).
2. Your request is encrypted **multiple times in layers** — once per intermediate node, using each node's respective **public key** (public key cryptography, covered earlier in the course).
3. Each node can only decrypt/"peel off" **its own outer layer** using its own **private key**, revealing just enough information to know where to forward the traffic next — no single intermediate node sees the full original content and destination together.
4. This process repeats until the final node forwards the (now fully decrypted) request to its actual destination.

**Privacy properties:**

- By design, Tor **minimizes logging** at each relay node — there isn't much historical data for any single node to retain or hand over even if compelled.
- Using a **different path** through the network for different requests/sessions further obscures long-term traceability.

**Limitations (explicitly noted in the lecture):**

- **Not absolute protection.** If a government or other authority with sufficient resources compels multiple intermediate nodes (e.g., via subpoena) and reconstructs the path _before_ that data is naturally discarded, tracing is technically possible — just significantly more laborious than with standard, unencrypted, single-hop browsing.
- **Traffic analysis can still reveal Tor usage itself**, based on IP addresses and port numbers used by the Tor protocol — meaning an observer on a local network (e.g., a home or university network administrator) can potentially tell _that_ someone is using Tor, even without knowing _what_ they're doing with it. If you're the only person on a monitored network using Tor at a suspicious time, that alone could draw scrutiny.

**Overall framing:** Tor "raises the bar" for detection and privacy invasion — it does not provide absolute, unconditional protection, consistent with the lecture's recurring theme that **no privacy technology discussed is a perfect guarantee**, only a probability reduction.

---

## 12. App Permissions

Modern operating systems (iOS, Android, desktop OSes) increasingly implement **fine-grained, explicit permission prompts** for sensitive capabilities:

- Camera access
- Microphone access
- Contacts access
- Location access

**Usability vs. privacy tension:** Denying a permission often means the app **simply won't function** for that feature (or at all), since many apps are coded to require the permission unconditionally — pushing users toward a binary "allow everything or don't use the app" choice.

**Finer-grained options (notably on iOS):** Some permissions (e.g., location) can be scoped to:

- **Always** (even when the app is closed/in background)
- **Only while using the app**
- **Never**

**Location services — a specific concern:**

- Necessary for mapping/navigation apps to function.
- **But** many apps request "Always" access **by default**, meaning your device can track your movement continuously — even when you're not actively using any map or navigation feature.
- Major platform operators (Google, Apple, etc.) can, as a result, potentially maintain a near-continuous record of users' physical locations if this default access remains enabled.

**Takeaway:** Permission systems represent genuine privacy progress (more granular control than in the past), but the _defaults_ many apps request, and the _usability pressure_ to simply "allow everything," mean users should actively review and reconsider these settings rather than accept defaults passively.

---

## 13. Course Wrap-Up

The lecture (and the course arc) is summarized as progressing through:

1. Securing your **accounts**
2. Securing your **data**
3. Securing your **systems**
4. Securing your **software**
5. Preserving your **privacy** _(this lecture)_

**Closing framing:** The specific technologies discussed (headers, cookies, DNS, VPNs, Tor, permissions) will inevitably evolve or be replaced, but the **underlying first principles** — question what data is being shared, understand the mechanisms by which it's shared, and evaluate the trade-offs of available protective measures — are intended to transfer to **future, not-yet-invented technologies and threats** as well.

---

## 14. Key Terms Glossary

| Term                              | Definition                                                                                                                                                                 |
| --------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Server log**                    | Server-side record of requests (IP, timestamp, requested resource, referrer, user agent, etc.)                                                                             |
| **Referer / Referrer**            | HTTP header indicating the URL a user came from before navigating to the current page (note: header name is historically misspelled with one "r")                          |
| **Referrer-Policy**               | HTTP header or `<meta>` tag controlling how much referrer information a browser sends (e.g., `origin`, `no-referrer`)                                                      |
| **Fingerprinting**                | Using a combination of request/browser characteristics (IP, User-Agent, screen resolution, fonts, time zone, plugins, etc.) to re-identify a user without cookies or login |
| **User-Agent**                    | HTTP header string identifying browser type/version and operating system                                                                                                   |
| **Cookie**                        | Small piece of data stored by a server on a user's browser to maintain identity/state across requests                                                                      |
| **Session cookie**                | Short-lived cookie meant to expire when the browser session ends; used for logins, shopping carts, etc.                                                                    |
| **Tracking cookie**               | Long-lived cookie designed to persistently identify/track a user, often for advertising or analytics                                                                       |
| **Tracking parameter**            | A URL query-string value (e.g., `click_id=...`) used to identify/track a specific user or click, distinct from cookies                                                     |
| **Third-party cookie**            | A cookie set by a domain other than the one the user directly navigated to (e.g., an embedded ad/analytics service)                                                        |
| **First-party**                   | The website a user deliberately/directly visits                                                                                                                            |
| **Third-party**                   | An external service embedded within a first-party page (ads, analytics, widgets) that can also set cookies                                                                 |
| **Private/Incognito browsing**    | A browser mode that avoids persisting history/cookies/logins locally after the window closes; does not prevent server-side logging or fingerprinting                       |
| **Super cookie**                  | A cookie/tracking header injected into traffic by an intermediary (e.g., ISP or mobile carrier) rather than by the visited website itself; defeated primarily by HTTPS     |
| **DNS (Domain Name System)**      | Hierarchical system translating human-readable domain names into IP addresses                                                                                              |
| **DoH (DNS over HTTPS)**          | DNS queries encrypted and transmitted using the HTTPS protocol                                                                                                             |
| **DoT (DNS over TLS)**            | DNS queries encrypted using TLS directly, without HTTP wrapping                                                                                                            |
| **VPN (Virtual Private Network)** | Encrypted tunnel between a user's device and a remote endpoint, masking IP/location and securing that specific network segment                                             |
| **Tor (The Onion Router)**        | Privacy software that layers encryption across multiple relay nodes, so no single node sees the full path and content of a request                                         |
| **End-to-end encryption**         | Encryption scheme where only the communicating endpoints (not intermediate servers/companies) can decrypt message contents                                                 |
| **App permissions**               | OS-level, user-controlled grants of access to sensitive device features (camera, microphone, contacts, location)                                                           |
| **SIM swapping/porting**          | Attack technique where an adversary transfers/hijacks a victim's phone number to a device they control, enabling interception of calls/texts                               |

---

## 📌 Overarching Lessons of the Lecture

1. **Security ≠ Privacy.** Encrypting a connection (HTTPS, VPN, Tor) protects data _in transit_ from outsiders, but doesn't stop the _legitimate_ endpoint (or embedded third parties) from collecting and retaining information about you.
2. **Every layer of the stack can leak privacy information** — browser history, HTTP headers (Referer, User-Agent), cookies, URL parameters, DNS queries, and even hardware sensors on mobile devices.
3. **No single privacy tool is a complete solution** — private browsing, VPNs, Tor, and encrypted DNS each address a _specific_ leak vector, but none eliminates all forms of tracking or fingerprinting; they _reduce probability_, not _guarantee_ anonymity.
4. **Third parties embedded across many first-party sites accumulate outsized visibility** into a user's overall behavior — often more than any individual site they interact with.
5. **Defaults matter.** Many privacy risks (referrer leakage, "Always" location access, third-party cookies) exist because of **permissive defaults**, not because users actively chose them — actively reviewing and adjusting settings is a recurring practical takeaway.
6. **HTTPS is a recurring, foundational defense** — it protects against super cookies, mitigates some fingerprinting/eavesdropping risk, and is a prerequisite for meaningful DNS privacy (DoH) and general confidentiality.

---

_End of Part 2 notes — concludes CS50's Introduction to Cybersecurity._
