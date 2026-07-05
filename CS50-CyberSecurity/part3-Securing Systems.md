# CS50 Cybersecurity — Securing Systems (Part 3)

**Instructor:** David Malan
**Course:** CS50's Introduction to Cybersecurity
**Topic:** Wi-Fi Security, HTTP/HTTPS, TLS, Certificates, VPNs, SSH, Ports, Firewalls, Proxies, Malware, Botnets, DDoS, and Antivirus

---

## 📑 Table of Contents

1. [Introduction: From Accounts and Data to Systems](#1-introduction-from-accounts-and-data-to-systems)
2. [Securing Wi-Fi: WPA](#2-securing-wi-fi-wpa)
3. [HTTP and the Machine-in-the-Middle Threat](#3-http-and-the-machine-in-the-middle-threat)
   - 3.1 [What Is HTTP?](#31-what-is-http)
   - 3.2 [The Machine-in-the-Middle (MITM) Attack Revisited](#32-the-machine-in-the-middle-mitm-attack-revisited)
   - 3.3 [HTML Injection Example](#33-html-injection-example)
4. [Packet Sniffing](#4-packet-sniffing)
   - 4.1 [What Is a Packet?](#41-what-is-a-packet)
   - 4.2 [Sniffing a Search Query](#42-sniffing-a-search-query)
   - 4.3 [Sniffing a Credit Card Checkout](#43-sniffing-a-credit-card-checkout)
5. [Cookies and Session Hijacking](#5-cookies-and-session-hijacking)
   - 5.1 [How Cookies Work](#51-how-cookies-work)
   - 5.2 [Session Hijacking](#52-session-hijacking)
6. [HTTPS, TLS, and Certificates](#6-https-tls-and-certificates)
   - 6.1 [What HTTPS/TLS Actually Does](#61-what-httpstls-actually-does)
   - 6.2 [Certificates and Certificate Authorities (CAs)](#62-certificates-and-certificate-authorities-cas)
   - 6.3 [How Your Browser Verifies a Certificate](#63-how-your-browser-verifies-a-certificate)
7. [SSL Stripping](#7-ssl-stripping)
   - 7.1 [How the Attack Works](#71-how-the-attack-works)
   - 7.2 [The Homograph Trick (example.com vs exam1e.com)](#72-the-homograph-trick-examplecom-vs-exam1ecom)
   - 7.3 [Defenses: User Habits and HSTS](#73-defenses-user-habits-and-hsts)
8. [VPNs and SSH](#8-vpns-and-ssh)
   - 8.1 [Virtual Private Networks (VPNs)](#81-virtual-private-networks-vpns)
   - 8.2 [SSH (Secure Shell)](#82-ssh-secure-shell)
   - 8.3 [VPN vs. Tor](#83-vpn-vs-tor)
9. [Ports and Port Scanning](#9-ports-and-port-scanning)
   - 9.1 [What Is a Port Number?](#91-what-is-a-port-number)
   - 9.2 [Port Scanning](#92-port-scanning)
   - 9.3 [Penetration Testing and Ethical Hacking](#93-penetration-testing-and-ethical-hacking)
10. [Firewalls](#10-firewalls)
    - 10.1 [What Firewalls Do](#101-what-firewalls-do)
    - 10.2 [Filtering Methods: IP Address, Port, and Deep Packet Inspection](#102-filtering-methods-ip-address-port-and-deep-packet-inspection)
11. [Proxies](#11-proxies)
    - 11.1 [What Is a Proxy?](#111-what-is-a-proxy)
    - 11.2 [Corporate/University Certificate Authorities: A Legitimate MITM](#112-corporateuniversity-certificate-authorities-a-legitimate-mitm)
    - 11.3 [URL Rewriting](#113-url-rewriting)
12. [Malware](#12-malware)
    - 12.1 [What Is Malware?](#121-what-is-malware)
    - 12.2 [Viruses](#122-viruses)
    - 12.3 [Worms](#123-worms)
13. [Botnets and Denial-of-Service Attacks](#13-botnets-and-denial-of-service-attacks)
    - 13.1 [Botnets](#131-botnets)
    - 13.2 [DoS vs. DDoS](#132-dos-vs-ddos)
14. [Antivirus Software, Automatic Updates, and Zero-Day Attacks](#14-antivirus-software-automatic-updates-and-zero-day-attacks)
15. [Q&A Highlights from the Lecture](#15-qa-highlights-from-the-lecture)
16. [Summary of Key Takeaways](#16-summary-of-key-takeaways)
17. [Quick-Reference Glossary](#17-quick-reference-glossary)

---

## 1. Introduction: From Accounts and Data to Systems

- Previous lectures covered securing **accounts** (passwords, MFA) and securing **data** (hashing, encryption, digital signatures).
- This lecture shifts focus to securing **systems** — particularly those that are **networked or inter-networked** — building directly on the encryption concepts already introduced.
- Core theme carried forward: **encryption is a fundamental building block** for solving many of the security problems that arise when systems communicate with one another.

[⬆ Back to top](#-table-of-contents)

---

## 2. Securing Wi-Fi: WPA

- When connecting to Wi-Fi, networks are generally either **unsecured** or **secured** — commonly signaled by a **padlock icon** on your phone, laptop, or desktop.
- **What "secured" actually means:** Traffic between your device and the **access point** (the wireless router/hotspot device) is encrypted — but only up to that point.
- **Underlying technology:** **Wi-Fi Protected Access (WPA)**, which has evolved through multiple versions over the years. Best practice: use the **latest supported version** of WPA on your devices and routers.
- **Important scope limitation:** WPA only encrypts traffic between your device and the **local access point**. From there, traffic travels onward through **routers** (computers that direct network traffic) toward its ultimate destination — and WPA alone says nothing about whether _that_ portion of the journey is encrypted.

[⬆ Back to top](#-table-of-contents)

---

## 3. HTTP and the Machine-in-the-Middle Threat

### 3.1 What Is HTTP?

> **HTTP (Hypertext Transfer Protocol)** is the language/protocol computers use to communicate on the World Wide Web.

- When you visit a URL beginning with `http://`, your browser is communicating with a remote server using this protocol.
- **Critical weakness:** HTTP is, by definition, **not encrypted**. Messages are transmitted as plain, largely English-like text — fully readable to anyone who intercepts them.

### 3.2 The Machine-in-the-Middle (MITM) Attack Revisited

- If Alice (the user) is communicating with Bob (a web server) over plain HTTP, an eavesdropper "Eve" positioned between them can:
  - **Read** every request Alice sends and every response Bob returns (simple eavesdropping).
  - **Manipulate** the content of those messages in transit — a more serious threat than mere observation.
- **Q&A note:** Malan explicitly notes that detecting a machine-in-the-middle attack is generally **not possible** for an ordinary user — it can happen entirely without your knowledge. The remedy is **prevention via encryption**, not detection.

### 3.3 HTML Injection Example

- Websites return **HTML (Hypertext Markup Language)** — the language used to structure web pages.
- Because plain HTTP traffic isn't scrambled, an eavesdropper/MITM can **inject additional HTML** into a page as it's delivered to the browser — without the user or even the original website knowing.
- **Real-world example given:** An Internet Service Provider (ISP), a coffee shop, or a hotel Wi-Fi provider might inject **advertisements** into every webpage a user visits — even websites that were never designed to display those ads. More maliciously, injected code could be designed to **steal user data**.

[⬆ Back to top](#-table-of-contents)

---

## 4. Packet Sniffing

### 4.1 What Is a Packet?

> A **packet** is a virtual "envelope" of data traveling across the internet from one point (e.g., Alice) to another (e.g., Bob) — analogous to a physical mail envelope containing a letter.

- Large messages may be broken across **multiple packets**, since not everything necessarily fits in one.

> **Packet sniffing** is the act of intercepting and inspecting the contents of these packets/envelopes as they travel across a network — possible whenever the contents are unencrypted.

### 4.2 Sniffing a Search Query

Example of what a sniffed HTTP request might contain when searching a search engine for "cats":

```
GET /search?query=cats HTTP/3
Host: example.com
```

- This reveals not just that a search occurred, but the **specific search term** ("cats") — trivially readable by anyone sniffing the packet.
- While a search for "cats" may seem harmless, the same technique exposes **any** search query, sensitive or not.

### 4.3 Sniffing a Credit Card Checkout

Example of a sniffed HTTP `POST` request during an online checkout:

```
POST /checkout HTTP/3
Host: example.com

card_number=XXXXXXXXXXXXXXXX
```

- If unencrypted, this exposes the **credit card number**, and potentially the cardholder's **name, address, and security code**, to anyone sniffing the traffic.
- **Key takeaway:** Without encryption, sensitive data like payment details is transmitted essentially "in the open."

[⬆ Back to top](#-table-of-contents)

---

## 5. Cookies and Session Hijacking

### 5.1 How Cookies Work

> A **cookie** is a small piece of data sent by a web server to a browser, which the browser then stores and automatically re-sends on subsequent requests — allowing the server to "remember" who you are across multiple page visits.

**Analogy used in the lecture:** A cookie is like getting your **hand stamped** at a venue — once stamped (logged in), you can show that same stamp repeatedly instead of re-verifying your identity from scratch each time.

**How it works technically:**

1. After you log in, the server responds with an HTTP status code `200` (meaning "OK") plus a header like:
   ```
   Set-Cookie: session=1234abcd
   ```
2. Your browser stores this **session** value.
3. On every subsequent request to that same site (clicking links, adding items to a cart, opening emails), your browser automatically re-sends:
   ```
   Cookie: session=1234abcd
   ```
4. The server checks this value against its own records to know, "yes, this is still David" — without requiring you to log in again on every click.

- Cookies may be stored **temporarily** (in memory) or **persistently** (on disk) for a configured duration (a day, a week, a year), depending on server settings.

### 5.2 Session Hijacking

> **Session hijacking** occurs when an attacker intercepts a valid session cookie and reuses it to impersonate the legitimate user — without ever needing to know that user's actual username or password.

- This is only possible when the connection is **unencrypted (HTTP)**, since the cookie value travels in plain text and can be read by anyone sniffing the traffic.
- **Attack flow:**
  1. Attacker sniffs a user's HTTP traffic and observes their session cookie (e.g., `1234abcd`).
  2. Attacker sends their own request to the same server, **including the stolen cookie value**.
  3. The server, seeing a valid session cookie, treats the attacker as the legitimate logged-in user — **even while the real user remains simultaneously logged in themselves**.
- **Defense:** Use **HTTPS**, which encrypts the entire request/response — including the `Set-Cookie` and `Cookie` header lines — preventing an eavesdropper from ever seeing the cookie value in the first place.

[⬆ Back to top](#-table-of-contents)

---

## 6. HTTPS, TLS, and Certificates

### 6.1 What HTTPS/TLS Actually Does

> **HTTPS** is HTTP secured via encryption. The modern protocol implementing this encryption is called **TLS (Transport Layer Security)** — the successor to the older **SSL (Secure Sockets Layer)**.

- HTTPS/TLS ensures the **entire connection** between browser and server is encrypted — not just the "important-looking" parts, but also lower-level details like cookies.
- Even though a machine in the middle can still see **metadata** on the "outside of the envelope" (e.g., IP addresses of sender/receiver), they **cannot read the contents inside** — the actual message is scrambled.
- **Underlying mechanism:** TLS relies on the **public key cryptography** principles covered in the previous lecture, solving the "chicken-and-egg" shared-secret problem even between parties who have never communicated before (e.g., you and a website you're visiting for the first time).

### 6.2 Certificates and Certificate Authorities (CAs)

> A **digital certificate** is essentially a website's **public key**, digitally signed by a trusted third party, along with identifying metadata (site name, validity period, etc.).

- Certificates commonly follow the **X.509** standard format.
- The trusted third parties that sign these certificates are called **Certificate Authorities (CAs)**.
- **Chain of trust:** Major browser manufacturers (Apple, Microsoft, Google, Mozilla) maintain built-in lists of CAs they trust. Because users generally trust these browser makers, and the browser makers in turn trust specific CAs, users transitively trust any certificate signed by one of those CAs.

### 6.3 How Your Browser Verifies a Certificate

When your browser visits an HTTPS website:

1. It **downloads the site's certificate**.
2. It computes a **hash value** of certain fields within that certificate, using a known hash function.
3. It looks at the **digital signature** attached to the certificate (signed by a CA).
4. It uses the **issuing CA's public key** to decrypt that signature.
5. If the decrypted result **matches** the hash computed in step 2, the certificate is confirmed as authentically signed by that CA — and, by transitive trust (per Section 6.2), the connection to that site can be trusted.

> This process directly mirrors the **digital signature verification** process covered in the previous lecture on securing data — applied here specifically to website certificates.

[⬆ Back to top](#-table-of-contents)

---

## 7. SSL Stripping

> **SSL stripping** (a term retained from the SSL era, now also applying to TLS) is an attack that tricks a user into believing they have a secure HTTPS connection to a legitimate website, when in fact they may have an unencrypted connection — or worse, an encrypted connection to an **attacker-controlled** site instead.

### 7.1 How the Attack Works

- Users often type abbreviated URLs (`example.com`, or `www.example.com`) without explicitly specifying `https://` — relying on the browser to fill in the correct protocol automatically.
- This creates a brief window where the **initial request may go out over plain HTTP** before any redirection to HTTPS occurs.
- If a **machine in the middle** intercepts this initial unencrypted request, it can respond with its own **HTTP 307 "redirect"** status code, sending the browser to a URL of the attacker's choosing — potentially one that _looks_ like it uses HTTPS, giving a false sense of security.

### 7.2 The Homograph Trick (example.com vs exam1e.com)

- A particularly subtle version of this attack involves the attacker registering a **look-alike domain name** — e.g., replacing the letter "l" with the numeral "1": `exam1e.com` instead of `example.com`.
- Depending on the font/screen, this substitution can be **visually indistinguishable** to the naked eye.
- **Consequence:** The victim may end up with a perfectly valid, properly encrypted HTTPS connection — just to the **wrong (attacker-controlled) server**, not the legitimate site they intended to visit — potentially exposing them to further **phishing** attacks (e.g., being asked to log in and unwittingly hand over credentials).

### 7.3 Defenses: User Habits and HSTS

**As a user/consumer:**

- Get in the habit of **always manually typing `https://`** before the domain name, rather than relying on the browser to fill it in — the most reliably "paranoid" defense, even if slightly tedious.

**As a website administrator/server owner**, a protocol called **HSTS (HTTP Strict Transport Security)** can be configured:

- The server sends a special HTTP header instructing browsers to **always use HTTPS** when communicating with this domain going forward — for a specified duration (commonly set to at least **one year**, i.e., roughly 31,536,000 seconds).
- **Subdomain protection:** Administrators can extend this instruction to cover subdomains as well (e.g., ensuring `www.example.com` is also always accessed via HTTPS).
- **Effect:** After the _very first_ successful HTTPS visit, the browser will automatically enforce HTTPS on **every subsequent visit** to that domain (2nd, 3rd, ... 3,000th time) for the specified duration — even refusing to load the page over plain HTTP, even if a user's URL bar or a malicious redirect tries to specify HTTP.
- **Going further — the `preload` directive:** Administrators can include a `preload` flag in the HSTS header, signaling that they want their domain **built directly into the source code of major browsers** (like Chrome). This eliminates even the very first, brief window of vulnerability, since the browser will already "know" to always use HTTPS for that domain — even before a user's first visit.

[⬆ Back to top](#-table-of-contents)

---

## 8. VPNs and SSH

### 8.1 Virtual Private Networks (VPNs)

> A **VPN (Virtual Private Network)** encrypts **all** internet traffic between your device and a VPN server — a broader guarantee than HTTPS, which only secures web traffic specifically.

- **Common use case:** Employees connecting to a company's internal servers (email, files, video conferencing, etc.) are often required, by policy, to connect through a VPN client on their laptop/phone.
- **Authentication:** Typically requires a username/password, and often additional factors (2FA code, USB security key, etc.) to establish the VPN connection.
- **How it works:** Once authenticated, an **encrypted tunnel** is established between your device (Point A) and the VPN server (Point B) — protecting all traffic traveling through that tunnel, regardless of the specific service/application being used.

**Side effects of VPN use:**

- Your traffic appears to originate from the **VPN server's IP address**, not your own actual IP address.
- **Common applications of this side effect:**
  - Appearing to be located in a different country (e.g., to access company resources while traveling internationally).
  - Accessing geographically restricted streaming media services not normally available in your actual location.

### 8.2 SSH (Secure Shell)

> **SSH (Secure Shell)** is a protocol that allows a user to **securely connect to and execute commands on a remote server**, rather than encrypting all general internet traffic like a VPN.

- Commonly used by **programmers and system administrators** to remotely control servers.
- **Illustrative example from the lecture:** Running the `date` command locally shows the local time; then using `ssh` to connect to a remote server (e.g., Stanford University's server) and running `date` again shows that server's local time instead — demonstrating that subsequent commands are now executing remotely.
- **Security guarantee:** Everything typed **after** establishing an SSH connection — even something as simple as the `date` command — is **encrypted**, so no intermediary between the two points can observe what commands are being run or what data is being exchanged.
- SSH can also be used to build the equivalent of a simple VPN-like tunnel, though its primary purpose is remote command execution.

### 8.3 VPN vs. Tor

Raised directly in student Q&A (see Section 15 for full context):

| Technology | Primary Purpose                                                             | Key Characteristic                                                                                                                                                                    |
| ---------- | --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **VPN**    | Encrypts traffic between you and a specific VPN server                      | Secures _data_, but does **not** anonymize you — if subpoenaed, a VPN provider could potentially disclose which user was connected and when                                           |
| **Tor**    | Anonymizes/obscures the origin of your traffic (covered in a later lecture) | Traffic is bounced across many volunteer servers worldwide, making it very difficult to trace back to its true origin — designed for **privacy preservation**, not just data security |

> 💡 **Key distinction:** A VPN secures _what_ you're sending, but it's still fairly obvious _where_ your data is ultimately coming from (i.e., which VPN account/user). Tor is specifically designed to obscure the origin itself.

[⬆ Back to top](#-table-of-contents)

---

## 9. Ports and Port Scanning

### 9.1 What Is a Port Number?

> A **port number** is a standardized numeric identifier on the "outside of a packet" that indicates which specific service on a destination computer should handle that packet.

**Common, standardized port numbers mentioned:**

| Port Number | Service |
| ----------- | ------- |
| 80          | HTTP    |
| 443         | HTTPS   |
| 22          | SSH     |

- These numbers are agreed-upon conventions that let computers know which software (web server, email server, chat server, etc.) should open and process a given packet.
- A computer/server may be **listening** on one or more of these ports for incoming connections, or may have none open at all (fully disconnected from inbound traffic).

### 9.2 Port Scanning

> **Port scanning** is the technique of systematically checking every possible port number (roughly 0 to 65,000+) on a target server to discover which ones are "open" (actively listening for connections) and which services are running there.

- Conceptually similar to the earlier brute-force password-cracking demonstrations — a simple loop trying every value in a range.
- **Why "hiding" a service on a non-standard port isn't a real solution:** Running a service on an unusual, non-standard port number ("security through obscurity") does not provide meaningful protection on its own, since port scanning can still discover it. Genuine protection requires actually using **proper encryption and authentication** on whatever service is exposed — not merely hoping attackers won't find the right port number.

### 9.3 Penetration Testing and Ethical Hacking

> **Penetration testing ("pen testing")** is the practice — and profession — of deliberately probing an organization's systems and network (e.g., via port scanning, brute-forcing passwords, or even social engineering employees) in order to find security weaknesses **before** malicious actors do.

- Also referred to as **ethical hacking**: using hacking skills for legitimate, authorized, and compensated purposes.
- **Key ethical distinction:** A penetration tester reports discovered vulnerabilities **privately, to the organization that hired them** — allowing the organization to fix the issue — rather than exploiting or publicly disclosing it.
- **Gamified organizational structure mentioned:**
  - **Red team** — plays the role of the attacker, actively trying to penetrate the network/systems.
  - **Blue team** — plays the role of the defender, trying to detect and repel the red team's simulated attacks.
  - This adversarial exercise helps organizations proactively discover and fix weaknesses before real-world adversaries can exploit them.

[⬆ Back to top](#-table-of-contents)

---

## 10. Firewalls

### 10.1 What Firewalls Do

> A **firewall** is a piece of software (or hardware/software combination) that sits between a network and the outside world, controlling what data is allowed to **enter** and **leave** that network.

- **Physical-world analogy:** A literal firewall between adjoining building units, designed to prevent a fire in one unit from spreading to another.
- **Digital equivalent:** Prevents unwanted data from leaving a private network (e.g., internal company chat/intercom traffic that should never reach the public internet) and prevents unwanted external traffic from entering (e.g., blocking random internet users from connecting to home computers/servers).
- Firewalls are **not absolute** — they can be selectively configured ("holes poked") to allow specific necessary traffic through (e.g., permitting outbound connections for video conferencing tools like Zoom).

### 10.2 Filtering Methods: IP Address, Port, and Deep Packet Inspection

Firewalls can filter/block traffic using several methods:

1. **By IP address** — blocking traffic to/from specific known addresses.
   - Example given: A parent configuring a home firewall to block known social-media IP addresses, preventing children's laptops/desktops from accessing those sites over the home Wi-Fi network.
   - **Limitation:** Easily circumvented by using an **out-of-band device** on a different network (e.g., a smartphone using cellular data instead of home Wi-Fi).
2. **By port number** — blocking or allowing traffic based on the destination port (e.g., blocking all ports except port 22 for SSH, or whatever port a VPN client requires).
3. **Deep packet inspection (DPI)** — the most sophisticated method: actually opening and examining the **contents** of packets, not just their outer metadata (IP address, port number).
   - Enables filtering by **domain name** (not just numeric IP address), and even by the actual message content.
   - **Real-world corporate use case:** Companies concerned about protecting intellectual property may use DPI to monitor whether employees are emailing sensitive product information to the press, competitors, or other unauthorized recipients.
   - Can also be used defensively — e.g., scanning email attachments for known **malware** signatures.

[⬆ Back to top](#-table-of-contents)

---

## 11. Proxies

### 11.1 What Is a Proxy?

> A **proxy** is a server (or piece of software) that sits between two communicating parties, effectively implementing a **sanctioned machine-in-the-middle** — by explicit design, rather than as a malicious attack.

- Functionally similar to a firewall: it can inspect traffic passing through it and decide whether to allow it through, modify it, or drop it entirely.
- Often required for **all** outbound/inbound traffic within an organization — e.g., a company or university network may route all traffic through a single proxy point by design/configuration.

### 11.2 Corporate/University Certificate Authorities: A Legitimate MITM

- Organizations that issue employees/students company-owned devices may install their **own Certificate Authority (CA)** onto those devices — in addition to the standard CAs already trusted by major browsers.
- **Implication:** Even though a user believes they are securely connecting via HTTPS to a legitimate external site (e.g., `gmail.com`), their device might actually be establishing a secure connection to the **organization's own proxy server** instead — which then may forward (or inspect, log, or filter) the traffic before relaying it onward.
- This is, functionally, a machine-in-the-middle attack — but a **sanctioned** one, made possible because the organization has installed additional trust (their own CA) directly onto a device they control.
- **Key lesson:** Any device that has been configured or issued by another party (employer, school) with administrative/root access should not be presumed fully private — such devices may have monitoring or interception software or certificates installed, and "all bets are off" regarding your assumed privacy on that device.

### 11.3 URL Rewriting

> **URL rewriting** is a technique — often applied to corporate or academic email systems — where links contained in incoming emails are automatically replaced with a **rewritten URL** that first routes through the organization's own proxy/security service before redirecting to the original destination.

- Example: An email link that appears to go to `gmail.com` might actually be rewritten as something like:
  ```
  https://example.com?url=gmail.com
  ```
- **Purpose:** Allows the organization's security system to:
  - **Check the destination** against known malware/phishing databases before allowing the redirect to proceed.
  - **Log** which external sites/links users are clicking on.
  - **Block** access entirely if the destination is flagged as malicious.
- **Trade-off:** While this provides a genuine security/anti-phishing benefit, it also means the organization gains visibility into **every link a user clicks** — a privacy implication worth being aware of, even when the underlying intent is protective.

[⬆ Back to top](#-table-of-contents)

---

## 12. Malware

### 12.1 What Is Malware?

> **Malware ("malicious software")** is simply software written to perform harmful actions — the maliciousness lies entirely in the intent of whoever wrote or deployed it, not in some special technical category.

- Software, in general, can do virtually anything a computer's operating system permits: delete files, send spam, mine cryptocurrency, exfiltrate data, and so on.
- **The line between "malware" and legitimate software** (e.g., Microsoft Word, Spotify) is fundamentally an **ethical** one, reinforced by market/business pressures (companies generally don't want to be caught doing harmful things, as it would be bad for business) — not some inherent technical restriction.
- Modern operating systems increasingly **sandbox** applications, requiring explicit user permission for sensitive actions (accessing the camera, microphone, network, etc.) — iOS is specifically highlighted as being particularly strict about this. This is a meaningful (if imperfect) mitigation, since users often click "allow" without much thought.

### 12.2 Viruses

> A **virus** is malware that attaches itself to a host and generally requires **human action** to spread and activate — e.g., opening an infected file, or clicking a malicious email attachment.

- Once running, a virus (like any malware) can do essentially anything: delete files, send spam, mine cryptocurrency, exfiltrate data to an adversary, etc.
- **Key characteristic:** Requires some form of **human mistake or intervention** to initially execute and take hold on a system.

### 12.3 Worms

> A **worm** is malware that can **spread from computer to computer automatically, without requiring human interaction**.

- **Mechanism:** Once a worm infects one computer, it can use that computer's network connection to perform **port scanning** (Section 9.2) across the local or broader network, searching for other computers with open, unencrypted, unauthenticated, and otherwise vulnerable ports.
- If it finds a vulnerable target, it can automatically transfer itself and infect that new machine — repeating the process indefinitely.
- **Root causes of worm propagation:** Software bugs/vulnerabilities, and gaps or misconfigurations in firewalls that allow this kind of unauthorized network traversal.

[⬆ Back to top](#-table-of-contents)

---

## 13. Botnets and Denial-of-Service Attacks

### 13.1 Botnets

> A **botnet** is a large network of malware-infected computers, secretly and remotely controlled by an attacker, generally without the device owners' knowledge.

- Unlike overtly destructive malware, botnet-oriented malware often deliberately **avoids** doing obvious harm (no visible file deletion, no obvious spam) — since the attacker benefits more from a large, persistent, undetected network of "always-on" infected machines than from a single machine rendered instantly useless.
- **Capability:** The attacker can send remote commands to the entire botnet simultaneously, directing potentially hundreds or thousands of infected computers to act in unison — e.g., attacking a target server, sending mass spam, or mining cryptocurrency collectively.

### 13.2 DoS vs. DDoS

> A **Denial-of-Service (DoS) attack** aims to overwhelm a target system with traffic/requests so that legitimate users cannot access its services.

- **Single-attacker DoS:** Limited in effectiveness — a single person's computer/connection generally has far fewer resources than a large target (e.g., repeatedly reloading `google.com` from one personal device would have negligible real-world impact on Google's infrastructure).

> A **Distributed Denial-of-Service (DDoS) attack** leverages a **botnet** — many compromised computers acting together — to send an overwhelming volume of requests to a target simultaneously, from many different sources at once.

- **Why "distributed" is especially dangerous:**
  - A target being attacked from a **single** IP address can simply block that one address via its firewall.
  - A target being attacked from **thousands of different IP addresses** (a botnet) faces a much harder defensive problem — blocking all of them risks also blocking **legitimate users** who happen to share network infrastructure (e.g., an entire company or university campus sharing a single public-facing IP address) with an infected machine on that same network.
- **Underlying resource constraint:** Every computer/server has finite memory and processing capacity per unit of time — a DDoS attack floods that capacity with bogus/malicious requests, crowding out legitimate ones and effectively denying service to real users/customers.
- **Broader insight:** The value of infecting/controlling a computer often isn't about attacking that specific machine directly — it's about what that machine **represents**: one more "node" or ally in a much larger, more powerful, distributed attack network.

[⬆ Back to top](#-table-of-contents)

---

## 14. Antivirus Software, Automatic Updates, and Zero-Day Attacks

> **Antivirus software** is a program (free or paid) that runs continuously or on a schedule, checking a computer for known viruses/worms and attempting to safely remove them (sometimes requiring a reboot to fully clear infected processes from memory).

**Key limitation:** Antivirus software is only effective against **known** threats — it must have up-to-date information about specific viruses/worms to detect them.

- **Recommendation:** Enable **automatic updates** — both for antivirus software specifically and for operating systems/software more broadly.
- **Why automatic updates are generally a net positive for society:**
  - Ensures users are running the latest versions of software, which typically include fixes for previously discovered security vulnerabilities.
  - Reduces the population of users vulnerable to "yesterday's" already-known and already-patched mistakes.
  - Allows software companies to focus development resources on current versions rather than maintaining extensive backward compatibility for many older, potentially insecure versions.
- **Acknowledged downside:** Automatic updates are not risk-free — companies (even major ones like Google, Microsoft, Apple) occasionally release updates that introduce new problems or break existing functionality. This is why updates are typically rolled out gradually (to a subset of users first) rather than to an entire user base all at once.

> A **zero-day attack** is an attack that exploits a vulnerability that the security community, software vendors, and antivirus providers **do not yet know about** — meaning no patch or detection signature exists yet at the time of the attack.

- **Why this is especially dangerous:** Even with antivirus software installed and automatic updates enabled, there is inherently a **lag time** between when a new threat first appears "in the wild" and when vendors identify it, develop a fix/detection signature, and distribute that fix to users. During this window, users remain vulnerable regardless of how diligent their existing security practices are.
- **Core lesson — layered/defense-in-depth security:** No single defensive measure (antivirus, strong passwords, firewalls, encryption, etc.) is sufficient on its own. Real-world security requires a **multi-layered "gauntlet" of defenses** — if an attacker breaches one layer, ideally subsequent layers still hold. This reinforces the course's recurring philosophy: security is not about achieving absolute, perfect protection, but about **continuously raising the cost, difficulty, and risk** for an adversary until they lose interest in pursuing a given target.

[⬆ Back to top](#-table-of-contents)

---

## 15. Q&A Highlights from the Lecture

**Q: How can you detect a machine-in-the-middle attack, and how do you get rid of it?**

> A: Detection is generally **not possible** for an ordinary user — such attacks can occur entirely without your knowledge. The remedy isn't detection, but **prevention through encryption** (the core focus of the rest of the lecture).

**Q: Does a machine-in-the-middle attacker need to be connected to the same Wi-Fi network as the victim?**

> A: Not necessarily. As long as the attacker is within reasonable physical/wireless proximity and has hardware capable of receiving nearby wireless packets, they don't need to share the same network — especially if that network is unencrypted. Specialized software exists that can passively listen to all nearby wireless traffic.

**Q: Do attackers need to know your IP address in advance to see what websites you're visiting?**

> A: No. An attacker can simply listen to all nearby wireless traffic and observe sender/receiver IP addresses directly as packets pass through the air — no prior knowledge of your specific IP address is required for this type of wireless attack.

**Q: Is HTTPS sufficient protection against super cookies, or should users clear cookies frequently?**

> A: **Super cookies** — injected by ISPs/mobile carriers (the lecture cites AT&T and Verizon as historical U.S. examples) — cannot be stopped at the browser level; users must actively **opt out directly through their carrier's account settings** (web or app). However, **using HTTPS significantly reduces this risk**, since carriers cannot inject cookies into properly encrypted messages — unless they've separately installed special monitoring software directly on the user's device (with the user's or an organization's permission).

**Q: Could there be a system where every "hop" between routers verifies packet integrity (like a passport stamp at each stage)?**

> A: Theoretically possible, but impractical in reality — individual users/organizations don't control all the intermediate routers a packet passes through, and there's no broad coordination mechanism for this across the internet. The more practical and widely adopted approach is **end-to-end encryption**, where only the sender and receiver need to handle encryption — eliminating the need to trust any intermediary "hop" along the way.

**Q: What's the difference between a VPN and Tor?**

> A: A **VPN** encrypts traffic between you and a specific VPN server, securing the _data_ itself — but it doesn't anonymize _who_ sent it; if legally compelled (e.g., subpoenaed), a VPN provider could potentially disclose which user was connected at a given time. **Tor**, covered in a later lecture, is specifically focused on **anonymizing/obscuring the origin** of your traffic by routing it through many independent servers worldwide, making it much harder to trace back to its true source.

**Q: If a university/company has installed its own Certificate Authority on my device, and I also use a VPN, does the university/company still know what I'm doing?**

> A: Yes — if an organization has installed software with **administrative privileges** on your device (including their own trusted CA), "all bets are off" regarding your privacy on that device. Since they can already intercept and inspect your HTTPS traffic via their own CA (a sanctioned machine-in-the-middle), using a VPN on top of that doesn't restore your privacy from that same organization — they could be monitoring everything you do on that device regardless.

**Q: If registrars are supposed to catch and block look-alike phishing domains (like `exam1e.com`), why do such domains still exist?**

> A: While reputable registrars have gotten better at flagging suspicious domain registrations, there are hundreds of domain registrars worldwide and hundreds of top-level domains (TLDs) — not all of them apply equally rigorous vetting. A malicious domain may eventually get shut down once discovered/reported, but that may only happen **after** it has already been used to successfully attack a number of victims.

[⬆ Back to top](#-table-of-contents)

---

## 16. Summary of Key Takeaways

1. **Wi-Fi security (WPA)** only encrypts traffic between your device and the local access point — it says nothing about the security of the rest of the journey your data takes across the internet.
2. **HTTP is unencrypted by design**, making it vulnerable to **machine-in-the-middle (MITM) attacks**, including content injection (e.g., unwanted advertisements) and **packet sniffing** of sensitive data like search queries and credit card numbers.
3. **Cookies** enable convenient session persistence (staying logged in) but create a **session hijacking** risk if transmitted over unencrypted HTTP, since a stolen cookie can let an attacker impersonate a legitimate user without ever knowing their password.
4. **HTTPS (via TLS)** encrypts the entire browser-server connection, relying on **digital certificates** issued and signed by trusted **Certificate Authorities (CAs)** — verified using the same digital-signature principles covered in the data security lecture.
5. **SSL stripping** attacks exploit the brief window when browsers default to HTTP before redirecting to HTTPS, sometimes combined with **look-alike domain names** (e.g., `exam1e.com`) to trick users into connecting to a fraudulent site. **HSTS** (with optional `preload`) is the server-side defense; manually typing `https://` is the user-side defense.
6. **VPNs** encrypt _all_ traffic between a device and a VPN server (broader than HTTPS), commonly used for secure remote access to corporate networks — but they secure data without anonymizing its true origin (unlike **Tor**).
7. **SSH** enables secure, encrypted remote command execution on another server — a core tool for developers and system administrators.
8. **Ports** are standardized numeric identifiers that route packets to the correct service on a destination machine (e.g., 80 for HTTP, 443 for HTTPS, 22 for SSH); **port scanning** systematically probes these to discover open/vulnerable services, and underlies both malicious reconnaissance and legitimate **penetration testing**.
9. **Firewalls** control what traffic enters/exits a network, filtering by IP address, port number, or (most granularly) via **deep packet inspection**.
10. **Proxies** act as sanctioned intermediaries — sometimes for legitimate security filtering (URL rewriting, malware scanning), but also capable of functioning as an organization-sanctioned machine-in-the-middle when a custom CA is installed on managed devices.
11. **Malware** (viruses, worms) is fundamentally just software with harmful intent; **viruses** require human action to spread, while **worms** can self-propagate across networks by exploiting vulnerable, unprotected services.
12. **Botnets** turn many individually infected computers into a coordinated attack network, commonly used to conduct **Distributed Denial-of-Service (DDoS)** attacks — far harder to block than a single-source DoS attack, since blocking many distributed IP addresses risks also blocking legitimate users sharing the same network infrastructure.
13. **Antivirus software** and **automatic updates** protect against known threats, but **zero-day attacks** exploit vulnerabilities not yet known to defenders — reinforcing the need for **layered, defense-in-depth security** rather than reliance on any single protective measure.
14. The recurring philosophy across every topic: perfect, absolute security is not achievable — the practical goal is always to **raise the cost, effort, and risk** required for an adversary to succeed, discouraging all but the most determined and well-resourced attackers.

[⬆ Back to top](#-table-of-contents)

---

## 17. Quick-Reference Glossary

| Term                                      | Definition                                                                                                         |
| ----------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| **WPA (Wi-Fi Protected Access)**          | The encryption standard used to secure Wi-Fi connections between a device and its wireless access point            |
| **HTTP**                                  | Hypertext Transfer Protocol; the unencrypted protocol used for web communication                                   |
| **HTTPS**                                 | HTTP secured via TLS encryption                                                                                    |
| **TLS (Transport Layer Security)**        | The modern encryption protocol underlying HTTPS; successor to SSL                                                  |
| **SSL (Secure Sockets Layer)**            | The older predecessor protocol to TLS                                                                              |
| **Machine-in-the-middle (MITM)**          | An attacker intercepting and potentially altering communications between two parties                               |
| **Packet**                                | A unit ("envelope") of data transmitted across a network                                                           |
| **Packet sniffing**                       | Intercepting and reading the contents of network packets                                                           |
| **Cookie**                                | A small piece of data stored by a browser and sent back to a server to maintain login/session state                |
| **Session hijacking**                     | Stealing and reusing a valid session cookie to impersonate a logged-in user                                        |
| **Certificate (digital)**                 | A signed document containing a website's public key and identifying information, used to establish trust           |
| **Certificate Authority (CA)**            | A trusted entity that digitally signs certificates on behalf of websites                                           |
| **SSL stripping**                         | An attack tricking a user into an unencrypted or attacker-controlled connection despite appearing secure           |
| **HSTS (HTTP Strict Transport Security)** | A server directive forcing browsers to always use HTTPS for a domain going forward                                 |
| **VPN (Virtual Private Network)**         | A technology encrypting all traffic between a device and a VPN server, also masking the user's apparent IP address |
| **SSH (Secure Shell)**                    | A protocol for securely connecting to and executing commands on a remote server                                    |
| **Tor**                                   | Anonymization software/network that obscures the true origin of internet traffic                                   |
| **Port number**                           | A standardized numeric identifier indicating which service on a computer should handle incoming traffic            |
| **Port scanning**                         | Systematically probing a range of port numbers to discover open/listening services                                 |
| **Penetration testing (pen testing)**     | Authorized, ethical testing of a system's security to discover vulnerabilities before malicious actors do          |
| **Red team / Blue team**                  | Simulated attacker / simulated defender roles used in organizational security exercises                            |
| **Firewall**                              | Software/hardware that filters traffic entering or leaving a network                                               |
| **Deep packet inspection (DPI)**          | Firewall/proxy technique that examines the actual contents of packets, not just metadata                           |
| **Proxy**                                 | An intermediary server/software that relays, inspects, or filters traffic between two parties                      |
| **URL rewriting**                         | Replacing links in emails with a proxy-routed URL to allow security screening before redirecting                   |
| **Malware**                               | Malicious software of any kind                                                                                     |
| **Virus**                                 | Malware requiring human action (e.g., opening a file) to activate and spread                                       |
| **Worm**                                  | Malware that self-propagates across networks without human interaction                                             |
| **Botnet**                                | A network of malware-infected computers remotely controlled by an attacker                                         |
| **DoS (Denial of Service)**               | An attack aiming to overwhelm and deny access to a target system                                                   |
| **DDoS (Distributed Denial of Service)**  | A DoS attack carried out using many distributed sources (e.g., a botnet)                                           |
| **Antivirus software**                    | Software that detects and removes known viruses/worms from a system                                                |
| **Zero-day attack**                       | An attack exploiting a vulnerability not yet known to defenders/vendors                                            |

[⬆ Back to top](#-table-of-contents)

---

_End of Part 3 — Next lecture continues with securing software, followed by a final lecture on privacy (including Tor)._
