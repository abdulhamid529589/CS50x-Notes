# CS50 Cybersecurity — Securing Accounts (Part 1)

**Instructor:** David Malan
**Course:** CS50's Introduction to Cybersecurity
**Topic:** Authentication, Authorization, Passwords, Attacks, and Defenses for Digital Accounts

---

## 📑 Table of Contents

1. [Introduction: Security in the Physical vs. Digital World](#1-introduction-security-in-the-physical-vs-digital-world)
   - 1.1 [The Physical-World Analogy](#11-the-physical-world-analogy)
   - 1.2 [Authentication](#12-authentication)
   - 1.3 [Authorization](#13-authorization)
   - 1.4 [Digital Credentials: Username & Password](#14-digital-credentials-username--password)
2. [Password Strength and Common Attacks](#2-password-strength-and-common-attacks)
   - 2.1 [Dictionary Attacks](#21-dictionary-attacks)
   - 2.2 [Brute Force Attacks](#22-brute-force-attacks)
   - 2.3 [Measuring Password Strength (Keyspace Math)](#23-measuring-password-strength-keyspace-math)
   - 2.4 [Live Demonstration: Cracking Passwords with Python](#24-live-demonstration-cracking-passwords-with-python)
   - 2.5 [Key Takeaway: Security as a Function of Time and Resources](#25-key-takeaway-security-as-a-function-of-time-and-resources)
3. [NIST Password Recommendations](#3-nist-password-recommendations)
   - 3.1 [Minimum & Maximum Length](#31-minimum--maximum-length)
   - 3.2 [Blocklisting Weak/Compromised Passwords](#32-blocklisting-weakcompromised-passwords)
   - 3.3 [No Password Hints or Security Questions](#33-no-password-hints-or-security-questions)
   - 3.4 [No Arbitrary/Periodic Password Changes](#34-no-arbitraryperiodic-password-changes)
   - 3.5 [Rate Limiting Failed Login Attempts](#35-rate-limiting-failed-login-attempts)
4. [Multi-Factor Authentication (MFA / 2FA)](#4-multi-factor-authentication-mfa--2fa)
   - 4.1 [The Three Factor Categories](#41-the-three-factor-categories)
   - 4.2 [One-Time Passwords (OTP)](#42-one-time-passwords-otp)
   - 4.3 [SMS-Based OTP Risks: SIM Swapping](#43-sms-based-otp-risks-sim-swapping)
   - 4.4 [Keylogging Threat](#44-keylogging-threat)
5. [Credential Stuffing](#5-credential-stuffing)
6. [Social Engineering and Phishing](#6-social-engineering-and-phishing)
   - 6.1 [Social Engineering](#61-social-engineering)
   - 6.2 [Phishing](#62-phishing)
   - 6.3 [Phishing Defenses](#63-phishing-defenses)
   - 6.4 [AI / Deepfake Voice Threats](#64-ai--deepfake-voice-threats)
7. [Man-in-the-Middle (MITM) Attacks](#7-man-in-the-middle-mitm-attacks)
8. [Systemic Defenses](#8-systemic-defenses)
   - 8.1 [Single Sign-On (SSO)](#81-single-sign-on-sso)
   - 8.2 [Password Managers](#82-password-managers)
   - 8.3 [Passkeys](#83-passkeys)
9. [Q&A Highlights from the Lecture](#9-qa-highlights-from-the-lecture)
10. [Summary of Key Takeaways](#10-summary-of-key-takeaways)
11. [Quick-Reference Glossary](#11-quick-reference-glossary)

---

## 1. Introduction: Security in the Physical vs. Digital World

### 1.1 The Physical-World Analogy

Malan opens by grounding the digital concepts in something familiar: physical security.

- Whether you live in a house, apartment, or dormitory, you almost certainly use a **key** to get through a locked door.
- That key grants access to the **entire space** behind the door — not selectively to one room versus another.
- Critically: **whoever holds the key can enter** — the lock has no way of knowing whether the person holding the key is the rightful owner or someone who stole/borrowed/copied it.

This maps directly onto two foundational pillars of digital security:

### 1.2 Authentication

> **Authentication** = the process of proving _who you are_.

- Example: "I am David" — but the system needs some way to verify that claim.
- In the digital world, authentication is most commonly done via a **username + password** combination, though (as we'll see later) biometrics, possession-based factors, and passkeys are increasingly used as well.

### 1.3 Authorization

> **Authorization** = determining _what you are allowed to access_, once your identity has been established.

- Being authenticated does **not** automatically mean you should have access to _everything_.
- Malan's physical analogy: even if a system confirms "yes, this is David," that doesn't necessarily mean David should have access to the _entire_ building — perhaps he should only be authorized to enter the lobby, or perhaps not be authorized at all.
- **Why this distinction matters:** Many real-world security failures occur not because authentication was broken, but because **authorization was too permissive** — a verified user was granted more access than they needed.

| Concept            | Question It Answers                                            | Example                                                    |
| ------------------ | -------------------------------------------------------------- | ---------------------------------------------------------- |
| **Authentication** | "Are you who you say you are?"                                 | Entering a username + password                             |
| **Authorization**  | "What are you allowed to do/see now that we know who you are?" | Being allowed into the lobby but not into a private office |

### 1.4 Digital Credentials: Username & Password

- **Username** — can be public. Examples: `David`, `Malan`, or (most commonly today) an email address that uniquely identifies a person.
- **Password** — should be private, known only to the account holder.
- Modern life requires managing **dozens, if not hundreds**, of separate accounts — and ideally, each should have a **distinct** password (a theme that recurs heavily throughout the lecture, tied to _credential stuffing_ in Section 5).
- The core assumption underlying password-based authentication: _if you type in the correct username AND the correct password, you must be the legitimate account owner_, because presumably only that person knows both values.

> ⚠️ This assumption breaks down the moment a password is guessed, stolen, leaked, or reused elsewhere — which is exactly what the rest of the lecture explores.

[⬆ Back to top](#-table-of-contents)

---

## 2. Password Strength and Common Attacks

Having a password isn't enough — it needs to be a **good** password. This section explains _why_, using two classic attack types and live mathematical/code demonstrations.

### 2.1 Dictionary Attacks

> A **dictionary attack** is when an adversary systematically tries every word from a dictionary (or word list) as a candidate password.

- Historically this meant literally opening a physical dictionary; today it means running a file containing thousands/millions of real words (in English or any other language) against a login form or a stolen password hash.
- **Why it works:** Humans tend to choose passwords that are actual words or simple variations of words, because they're easy to remember.
- **Implication:** Any password that is a plain dictionary word (`sunshine`, `dragon`, `password`) will be cracked almost immediately, regardless of how "obscure" the word might feel to the user.

### 2.2 Brute Force Attacks

> A **brute force attack** is when an adversary uses software to mechanically try **every possible combination** of characters until the correct password is found.

- The term evokes old-fashioned battering rams used to physically force open castle doors — the digital equivalent just tries "every possible key" instead of one physical strategy.
- Even a **fully random** password is vulnerable if it's **short enough**, because a fast enough computer (or network of computers) can eventually enumerate every possibility.
- Brute force is the "fallback" attack when dictionary attacks fail — slower, but guaranteed to work eventually if given unlimited time.

### 2.3 Measuring Password Strength (Keyspace Math)

The central formula introduced in the lecture:

```
Total possible passwords = (number of possible characters per position) ^ (password length)
```

This is illustrated by walking through increasingly complex character sets:

#### a) Digits only (0–9), 4-character password

- 10 possible digits per position, 4 positions → `10 × 10 × 10 × 10 = 10⁴ = 10,000` combinations.
- Verified intuitively: the smallest password is `0000`, the largest is `9999` — that's **10,000** total values (0 through 9999 inclusive), _not_ 9,999 (a common off-by-one mistake students made in the live chat).
- This is the default passcode length on many phones (4-digit PIN) — and it is **not secure**.

#### b) Upper + lowercase letters (A–Z, a–z), 4-character password

- 26 lowercase + 26 uppercase = **52** possible characters per position.
- `52⁴ ≈ 7.3 million` combinations — roughly 700× more than the 4-digit case.

#### c) Letters + digits + punctuation (full ASCII keyboard set), 4-character password

- On a standard US keyboard: 26 lowercase + 26 uppercase + 10 digits + ~32 punctuation symbols = **94** possible characters.
- `94⁴ ≈ 78 million` combinations — about 10× more than the letters-only case.

#### d) Letters + digits + punctuation, 8-character password

- `94⁸ ≈ 6 quadrillion` combinations.
- This is the point at which cracking becomes genuinely impractical for casual attackers — the exponential growth from lengthening the password matters far more than simply adding more symbol types.

**Summary Table:**

| Character Set                  | Length | Pool Size | Total Combinations  |
| ------------------------------ | ------ | --------- | ------------------- |
| Digits only (0–9)              | 4      | 10        | 10⁴ = 10,000        |
| Upper + lowercase letters      | 4      | 52        | 52⁴ ≈ 7.3 million   |
| Letters + digits + punctuation | 4      | 94        | 94⁴ ≈ 78 million    |
| Letters + digits + punctuation | 8      | 94        | 94⁸ ≈ 6 quadrillion |

> 💡 **Core insight:** Increasing the _length_ of a password has a far greater multiplicative effect on total combinations than increasing the _variety_ of characters used. Going from 4 → 8 characters (with the same 94-character pool) increases the keyspace by a factor of 94⁴ (~78 million), whereas going from digits-only to full ASCII at the same length only increases it by ~7,800×.

### 2.4 Live Demonstration: Cracking Passwords with Python

Malan demonstrates brute forcing live using **Python**, run from **VS Code** (Visual Studio Code), a free code editor used throughout CS50.

**Conceptual setup:** Imagine an adversary has physically stolen a phone and plugged it into a laptop via USB/Lightning cable. The following code simulates generating every possible password for a 4-character passcode:

```python
from string import digits  # gives access to characters '0'-'9'

for i in digits:
    for j in digits:
        for k in digits:
            for l in digits:
                print(i, j, k, l)
```

- This produces every 4-digit combination from `0000` to `9999`, printed to the screen (in a real attack, each combination would instead be automatically submitted to the login screen).
- **Note on code quality:** Malan acknowledges this nested-loop style (4 levels deep) is not idiomatic/clean Python — a more elegant approach exists using `itertools.product`, but the nested-loop version is used here for pedagogical clarity for those new to programming.

**Trial 1 — 4-digit PIN (digits only):**

```python
from string import digits
```

- Result: All 10,000 combinations printed in a matter of **milliseconds**.
- **Takeaway:** A 4-digit phone passcode offers essentially no real protection against a determined, even mildly technical attacker with physical device access.

**Trial 2 — 4-character password (upper + lowercase letters):**

```python
from string import ascii_letters
```

- Same nested-loop structure, but iterating over `ascii_letters` (all 52 upper/lowercase letters) instead of `digits`.
- Result: Took a **few seconds** to complete — noticeably slower, but still trivially fast.

**Trial 3 — 4-character password (letters + digits + punctuation):**

```python
from string import ascii_letters, digits, punctuation
```

- Combines all three character sets (94 total possible characters).
- Result: Took roughly **10× as long** as Trial 2 (~78 million vs. ~7 million combinations) — visibly slower, "Hollywood movie"–style scrolling text, but Malan notes real brute-force attacks proceed methodically (not the dramatic "guess one character at a time" trope seen in films).

**Trial 4 — 8-character password (letters + digits + punctuation):**

- Not fully demonstrated live (would take far too long — that's the point!), but the math (~6 quadrillion combinations) shows this crosses into genuinely impractical territory for brute-forcing on ordinary hardware.

### 2.5 Key Takeaway: Security as a Function of Time and Resources

- The fundamental _method_ of brute-forcing doesn't change as passwords get longer/more complex — what changes is **how long it takes** and **how many resources** (computing power, electricity, money, time) the attacker must expend.
- **Goal of password policy:** Raise the "cost" of attack high enough that, practically speaking, one of the following happens before the attacker succeeds:
  - The password gets changed.
  - The account is no longer in use.
  - The attacker (or the victim!) is no longer around to care.
- **The core trade-off:** The longer and more complex a password is, the _more secure_ it is — but also the _harder it is for a human to remember_. This tension between **usability** and **security** is a recurring theme throughout the entire lecture, and there is no single "correct" answer — it depends on context (personal preference, company policy, sensitivity of the account, etc.).

[⬆ Back to top](#-table-of-contents)

---

## 3. NIST Password Recommendations

The **National Institute of Standards and Technology (NIST)**, a U.S. government agency, publishes widely referenced guidelines for password and authentication security. Malan walks through several direct recommendations:

### 3.1 Minimum & Maximum Length

- **Minimum:** "Memorized secrets shall be at least eight characters in length." This lines up with the earlier math — 8 characters with a full character set (~6 quadrillion combinations) is the point where brute forcing becomes impractical.
- **Maximum:** Verifiers (i.e., websites/apps) "should permit subscriber-chosen memorized secrets of at least 64 characters in length," and should accept:
  - All printing ASCII characters, including spaces.
  - Unicode characters (e.g., emoji, accented letters from non-English languages).
- **Rationale:** As passwords become harder to remember, users increasingly benefit from choosing a **long passphrase** (a sentence, quote, or phrase) instead of a short, complex jumble of symbols. A 64-character sentence can be:
  - Easy for a human to remember.
  - Extremely resistant to both dictionary and brute-force attacks (assuming it isn't itself a famous quote or overly simple phrase).
- **Reality check:** Malan notes that in practice, many websites _still_ fail to support this — he recounts a personal experience of signing up for a new site and being forced to navigate arbitrary rules about uppercase/lowercase/punctuation counts and restricted symbol sets, which creates unnecessary **friction** without necessarily improving security.

### 3.2 Blocklisting Weak/Compromised Passwords

NIST recommends verifiers compare chosen passwords against a blocklist containing:

1. **Passwords from previous breach corpuses** — i.e., passwords found in previously leaked/hacked databases that are now circulating publicly. If a password already appears in such a list, it should be rejected, since attackers will try these first (this is essentially an "attacker-curated dictionary").
2. **Dictionary words** — plain words from any human language.
3. **Repetitive or sequential characters** — e.g., `aaaaaa`, `1234abcd`, `0000`. These patterns are trivially easy for an attacker to guess or prioritize.
4. **Context-specific words** — e.g., the name of the service itself, or the user's own username. Example: a user should not be permitted to set their Gmail password to something like `GmailPassword123`, nor their Amazon password to `AmazonPassword`. Smart attackers will try these same heuristics before resorting to brute force.

### 3.3 No Password Hints or Security Questions

- NIST: "Memorized secret verifiers shall not permit the subscriber to store a hint that is inaccessible to an unauthenticated claimant," and should **not** prompt users to set up recovery info based on things like "What was the name of your first pet?"
- **Why this is dangerous:**
  - Password hints often inadvertently _leak_ the password itself (e.g., a hint like "my password is my childhood pet's name" tells an attacker exactly where to look).
  - In the era of social media (LinkedIn, Facebook, Instagram, etc.), personal trivia like pet names, first cars, schools attended, or mothers' maiden names is often **publicly discoverable** or can be socially engineered out of the victim (see Section 6).
  - Anyone with enough time and motivation to specifically target an individual can often piece together answers to these "secret questions" from public information.

### 3.4 No Arbitrary/Periodic Password Changes

- NIST: "Verifiers shall not require memorized secrets to be changed arbitrarily (e.g., periodically)."
- This directly contradicts older corporate IT policies that once mandated password changes every 30/60/90 days — and Malan explains _why_ this older practice is now considered counterproductive:
  1. **Minimal-effort changes:** When forced to change frequently, users tend to make only trivial modifications (`password1` → `password2` → `password3`). If an attacker learns a previous password (e.g., from an old breach), they can often correctly guess the current one by extrapolating the pattern.
  2. **Increased forgetfulness:** Frequent forced changes cause users to genuinely forget their current password, since it's constantly in flux and easily confused with previous iterations — leading to poor usability without a corresponding security benefit.
- **Modern best practice instead:** Only require a password change when there is actual evidence of compromise (e.g., a known breach), not on an arbitrary calendar schedule.

### 3.5 Rate Limiting Failed Login Attempts

- NIST: "Verifiers shall implement a rate-limiting mechanism that effectively limits the number of failed authentication attempts that can be made on the subscriber's account."
- **Real-world example:** iPhones and Android devices lock you out (or introduce increasing time delays) after repeated incorrect passcode attempts — commonly after around 10 failed tries.
  - After the first several failures: short delay (e.g., 1 minute).
  - After more failures: longer delays (e.g., 5, 10+ minutes), and in some configurations, the device may **wipe itself entirely** if this feature is enabled.
- **Why rate limiting matters:** Recall the earlier demo — a 4-digit PIN could be cracked in _milliseconds_ with no rate limiting. Rate limiting artificially slows the attacker down to (at most) a handful of guesses per unit time, dramatically raising the real-world time cost of a brute-force attack — even though the underlying keyspace hasn't changed.
- **Added benefit — increased risk to the attacker:** If someone has stolen your phone at a coffee shop and is trying to brute force it before you notice, rate limiting increases the chance you'll notice the theft (or return to the table) before they succeed, increasing their risk of being caught in the act.
- **Trade-off acknowledged:** Legitimate users can accidentally lock themselves out (e.g., typing a passcode wrong with wet fingers) — again, a balance between usability and security.

[⬆ Back to top](#-table-of-contents)

---

## 4. Multi-Factor Authentication (MFA / 2FA)

### 4.1 The Three Factor Categories

**Two-Factor Authentication (2FA)**, more broadly **Multi-Factor Authentication (MFA)**, requires proof from **two or more fundamentally different categories** — not simply two passwords in a row (which would just be "two-step" verification, a distinction Malan explicitly draws out, noting that some companies use "two-step" language loosely/interchangeably with true 2FA).

| Factor Category            | Definition           | Examples                                        |
| -------------------------- | -------------------- | ----------------------------------------------- |
| **Knowledge**              | Something you _know_ | Password, PIN, security question answer         |
| **Possession**             | Something you _have_ | Physical key fob, smartphone, authenticator app |
| **Inherence (Biometrics)** | Something you _are_  | Fingerprint, facial recognition, retina scan    |

- The security value of MFA comes from **narrowing the attack surface**: a password alone can be guessed/leaked by anyone anywhere in the world. But a _possession_ factor means an attacker must be physically close to (or in possession of) the victim's device — dramatically reducing the pool of potential attackers (e.g., narrowing the threat down to "people near you in the coffee shop" rather than "anyone on the internet").

### 4.2 One-Time Passwords (OTP)

> A **One-Time Password (OTP)** is a temporary, single-use code generated specifically for one login attempt, rather than a fixed, reusable password.

Delivery mechanisms discussed:

- **Physical key fobs** — small hardware devices (often issued by employers) displaying a code that automatically changes every few seconds, synchronized with a server.
- **Authenticator apps** — software (e.g., Google Authenticator or similar) that generates rotating codes on your smartphone, functioning like a "digital key fob."
- **SMS text messages** — a code sent via traditional text message to the user's registered phone number.
- Some possession factors can even be plugged in via USB, removing the human from the process entirely and letting the device itself authenticate directly with special software.

### 4.3 SMS-Based OTP Risks: SIM Swapping

- Every phone contains a **SIM card** (physical chip or, increasingly, embedded/eSIM) with a **unique identifier**.
- Mobile carriers associate this unique SIM identifier with your phone number — this is how they know where to route your calls/texts.
- **SIM swapping attack:** An adversary contacts (or otherwise manipulates) the victim's mobile carrier and convinces them — often via social engineering using personal information gathered elsewhere — to reassign the victim's phone number to a **new SIM card that the attacker controls**.
- **Consequence:** Once the swap succeeds, all future SMS messages (including OTP codes) intended for the victim are instead delivered to the **attacker's device**.
- **Recommendation:** Where possible, prefer authenticator apps (which communicate directly with a server, independent of the cellular/SMS network) over SMS-based codes, since SIM swapping specifically targets the SMS delivery channel.

### 4.4 Keylogging Threat

- **Malware**, specifically **keyloggers**, can be installed on a victim's computer or phone (via malicious downloads, infected software, viruses, worms, etc.) and silently record **every keystroke**.
- Logged keystrokes (including passwords _and_ OTP codes) are typically transmitted to a server controlled by the attacker.
- **Sophisticated attack scenario:** If the malware and attacker are fast enough, they could capture a victim's username, password, _and_ one-time code, then use all three to log in before — or immediately after — the legitimate user does, effectively racing the victim to their own account.
- **Defenses:**
  - Avoid entering sensitive credentials on shared, public, or otherwise untrusted computers (internet cafés, public library computers, a friend's laptop, university lab machines, etc.) — Malan personally avoids this even at Harvard's own lab computers.
  - Only log into important accounts from devices you personally control and trust.
  - Run reputable antivirus/anti-malware software (addressed directly in the Q&A — see Section 9).
  - Recognize that this is a relatively **sophisticated, lower-probability** threat compared to simpler attacks like brute forcing or credential stuffing — but still worth guarding against, especially for high-value targets.

[⬆ Back to top](#-table-of-contents)

---

## 5. Credential Stuffing

> **Credential stuffing** is an attack where an adversary takes a list of usernames and passwords obtained from one breached service and systematically tries ("stuffs") those same credentials into **other, unrelated** websites and apps.

- **Why it works:** This attack exploits **password reuse** — the extremely common (if understandable) habit of using the same password across many different accounts.
- **Attack flow:**
  1. Some website/service (Service A) is breached, exposing usernames and passwords.
  2. That leaked data is published or sold online.
  3. An attacker takes those same username/password pairs and tries them on Service B, Service C, etc. — reasoning that many people reuse credentials across services.
  4. If the victim reused their password, the attacker gains access to _additional_ accounts beyond the originally breached one — even though those other services were never directly compromised.
- **Why usernames are different from passwords here:** It's generally fine (and common/necessary) to reuse the _same username_ across sites, especially when that username is simply your email address. The real danger is reusing the **same password**.
- **Defense:** Use a **unique password on every single website/app**. In practice, this is very difficult to do reliably without help — which is exactly why **password managers** (Section 8.2) are so strongly recommended.

[⬆ Back to top](#-table-of-contents)

---

## 6. Social Engineering and Phishing

### 6.1 Social Engineering

> **Social engineering** is a _non-technical_ attack that manipulates human psychology — trust, authority, urgency, fear — to trick someone into revealing information or taking an unsafe action.

**Live class demonstration:** Malan asked students to write down one of their own passwords on a piece of paper. Several students actually did it — despite (or perhaps partly _because of_) his position as a trusted instructor. This was a deliberate illustration:

- Anyone who complied was, in that moment, successfully "socially engineered" — a circumstance was created (an authority figure making a request in a seemingly legitimate context) that led people to act against their own security interest.
- **Lesson:** This same dynamic is what real attackers exploit — impersonating IT support, a bank representative, a company executive, or another trusted figure to extract sensitive information.
- Malan's advice afterward: destroy (shred/tear/flush) any paper on which a password was written, and internalize the feeling of having been "duped" as a reminder for real-world situations.

### 6.2 Phishing

> **Phishing** is a _technical_ form of social engineering, typically conducted via email or fraudulent websites, designed to trick victims into entering credentials or personal information into a fake or malicious platform.

Common phishing tactics discussed:

- **Spoofed emails** appearing to come from trusted organizations (banks, PayPal, Google) or trusted individuals (politicians, teachers), encouraging the recipient to click a link — to "verify your account," "make a donation," "reset your password," etc.
- **Fake login pages** that are pixel-for-pixel visual clones of real ones (e.g., a counterfeit Gmail login screen). Malan notes that, from a technical standpoint, replicating a website's appearance is often as simple as "copy and paste" of the real site's HTML/CSS.
- **Social media "engagement bait"** — posts inviting users to comment personal trivia (e.g., "comment your favorite childhood song!"). These seemingly innocent prompts can double as **phishing for security-question answers**, since many people (unwisely) use such personal facts as security-question responses elsewhere.

### 6.3 Phishing Defenses

Practical steps recommended in the lecture:

1. **Check the URL bar carefully** before entering any credentials. Confirm the domain is exactly correct (e.g., `gmail.com`, `google.com`, or the appropriate country-code domain) — not a lookalike domain.
2. **Hover over links** before clicking to preview the actual destination URL (commonly shown in the browser's status bar/corner) — the visible text of a link can say one thing while the actual underlying URL points elsewhere entirely.
3. **Don't trust links in unsolicited or sensitive emails.** Instead of clicking, open a new browser tab and manually type the website's known address (e.g., `paypal.com`) directly.
4. **Be cautious even around 2FA/verification prompts.** A sufficiently sophisticated phishing site can act as a **real-time relay**: it captures your username, password, _and_ your 2FA code as you enter them, and immediately forwards all three to the real website before you finish — potentially changing your password and locking you out before you realize what happened.
5. Recognize the inherent **usability vs. security trade-off**: manually retyping URLs instead of clicking links is less convenient, but meaningfully reduces phishing risk — the "right" balance depends on how sensitive the account is (banking and medical accounts warrant more caution than a casual forum account, for example).

### 6.4 AI / Deepfake Voice Threats

- Some accounts (notably certain banking systems) have historically used **voice recognition** ("my voice is my password") as an authentication factor.
- With the rise of AI-driven **deepfake audio generation**, this is increasingly risky: if an attacker has access to _any_ recordings of a victim's voice (increasingly easy given how much audio/video content people post or appear in), they may be able to synthetically generate convincing speech — including phrases the victim never actually said — to impersonate them.
- **Recommendation (raised in the Q&A):** Disable voice-based authentication wherever a stronger alternative (e.g., push-notification-based 2FA) is available.

[⬆ Back to top](#-table-of-contents)

---

## 7. Man-in-the-Middle (MITM) Attacks

> A **man-in-the-middle (MITM) attack** occurs when an intermediary system — sitting somewhere between the user and the destination website/app — intercepts, monitors, or manipulates data as it passes through.

- Internet traffic between a user and a destination (e.g., `amazon.com`, `paypal.com`, WhatsApp) doesn't travel directly — it passes through numerous intermediate machines: home routers, ISP infrastructure, university networks, corporate networks, and more.
- If any of these intermediate machines are compromised, malicious, or under the control of an adversary, that adversary may be able to observe or tamper with the data in transit — even though, from the user's perspective, it feels like a direct, private connection to the destination service.
- **Mitigation (deferred to a later lecture):** Proper use of **cryptography and encryption** — ensuring data is unreadable/unmodifiable to anyone except the intended sender and recipient — is the primary defense against MITM attacks. Malan explicitly defers the technical details of this to the course's next lecture on securing data.

[⬆ Back to top](#-table-of-contents)

---

## 8. Systemic Defenses

Having covered the _threats_, the lecture pivots to **systemic solutions** — approaches that reduce reliance on human memory/discipline (since, as Malan candidly acknowledges, humans — including himself — are often "the source of the problem," using weak or reused passwords out of sheer practical necessity).

### 8.1 Single Sign-On (SSO)

> **Single Sign-On (SSO)** allows a user to log into a new website/application using credentials from an existing, trusted account (e.g., "Log in with Google," "Log in with Facebook").

**How it actually works, step by step:**

1. The user clicks "Log in with Google" (or similar) on a third-party website.
2. The user's browser is **redirected to the actual provider's login page** (e.g., the real `google.com` or `facebook.com`) — not a fake or intermediary form hosted by the third-party site.
3. The user enters their username and password directly on the trusted provider's site.
4. Using cryptographic techniques (detailed in a later lecture), the provider confirms successful login to the third-party site — but **crucially, the actual password is never shared with the third-party website.** Only a confirmation of identity (e.g., "yes, this is malan@harvard.edu") is passed along.

**Benefits:**

- **Reduced friction:** Users don't need to create and remember yet another new password for every new service.
- **Inherited security:** If a user has already invested in strong security practices (strong password + 2FA) on their primary account (Google/Facebook/etc.), those same protections effectively extend to every service using SSO through that account.
- **Backward compatible:** Sites can still offer traditional email/password registration alongside SSO, for users who prefer not to link accounts.

### 8.2 Password Managers

> A **password manager** is software that securely generates, stores, and autofills unique passwords for every account a user has.

**Core features:**

- **Password generation:** Automatically creates strong, random passwords matching whatever length/complexity rules a given site requires (uppercase, lowercase, digits, punctuation, custom length, etc.) — removing the burden of manual password creation.
- **Secure storage:** Remembers generated (or manually entered) passwords so the human never needs to memorize them.
- **Autofill with domain verification:** When revisiting a saved site, the password manager can automatically fill in login credentials — but **only if the current URL matches the domain originally used to save the credentials.** This is a critical **anti-phishing defense**: if a user is tricked into visiting a fake look-alike site, the password manager will correctly recognize the mismatched domain and refuse to autofill, potentially alerting the user that something is wrong.
- **Cross-device sync:** Unlike basic browser-only "remember password" features (which are typically tied to a single browser/device and don't sync well across devices or with family members), dedicated password managers can synchronize securely across a phone, laptop, and desktop.

**The central trade-off:**

- All of a user's credentials are protected by **one single master password**. This concentrates risk: lose or forget that master password, and access to _every_ stored account may be jeopardized. Conversely, if that master password is compromised, an attacker could potentially gain access to everything at once ("all your eggs in one basket," as raised directly in student Q&A — see Section 9).
- **Mitigation of this risk:** The master password should be exceptionally strong, long, and unique — since it is effectively protecting everything else.

**Built-in / mainstream options mentioned in the lecture:**

| Provider  | Product                                                                            |
| --------- | ---------------------------------------------------------------------------------- |
| Apple     | iCloud Keychain                                                                    |
| Google    | Google Password Manager                                                            |
| Microsoft | Credential Manager                                                                 |
| (Various) | Third-party password managers (often with extra features for families/enterprises) |

- Malan's general recommendation for less-technical users: prefer the password manager **built into your operating system/ecosystem** (Apple, Google, Microsoft) unless a third-party product offers meaningfully better features for your specific situation (e.g., family sharing, enterprise administration).

### 8.3 Passkeys

> **Passkeys** are an emerging authentication technology designed to eliminate passwords altogether, using public-key cryptography.

- When registering with a new website/app that supports passkeys, your device (Mac, PC, or phone) automatically generates a **cryptographic key pair**:
  - A **private key**, which never leaves your device.
  - A **public key**, which is shared with and stored by the website/app.
- These two values have a mathematical relationship that allows the website to verify your identity on subsequent logins **without you ever having to remember or type a traditional password**.
- Your device handles the authentication process automatically (often unlocked via the device's own biometric authentication — fingerprint or face recognition — before releasing/using the private key).
- Passkeys can be **securely synchronized across a user's own devices** (e.g., Mac, iPhone, iPad) so that any of them can be used to authenticate.
- **Why this solves the "eggs in one basket" problem** (raised regarding password managers): rather than one master password protecting a vault of _other_ passwords, passkeys avoid the need for a password vault altogether — each service has its own unique cryptographic key pair generated and managed automatically by the device.
- Malan explicitly defers the full technical explanation of how public/private key cryptography works to the **next lecture**, which focuses on securing data via cryptography and encryption.

[⬆ Back to top](#-table-of-contents)

---

## 9. Q&A Highlights from the Lecture

The lecture included live audience questions that add valuable nuance:

**Q: Why aren't USB fingerprint-recognition devices more commonly used?**

> A: Primarily cost — dedicated biometric hardware is an added expense most individual consumers won't bother with, and can be costly to deploy across a large company's workforce. This is part of the motivation behind **passkeys**, which instead leverage a device people already own (like a phone with a built-in fingerprint/face sensor).

**Q: If four-digit passwords are so unsafe, why do some programs/systems still use them?**

> A: It comes down to the **usability vs. security trade-off** from the perspective of the developer/organization. If a system is too inconvenient, users may abandon it or forget their credentials constantly. Other contributing factors include general unawareness of security best practices, or simply not having thought through the implications of short passcodes. Industry practice is gradually improving, but many systems still fall short of best practices.

**Q: Do you recommend using built-in password managers (Google/Apple) to remember passwords?**

> A: Yes — this is addressed more fully in Section 8.2, but Malan's short answer in the moment was an affirmative "yes."

**Q: Doesn't putting all your passwords in one password manager defeat the purpose of having different passwords (i.e., "keeping all your keys in one safe")?**

> A: This is a genuinely valid trade-off to weigh personally:
>
> - If you're currently using **easy-to-guess** passwords, or **reusing** the same password across many sites, switching to a password manager is almost certainly a **net positive** — since it moves you toward unique, strong, hard-to-guess passwords everywhere.
> - If you're _already_ diligently using strong, unique, hard-to-guess passwords for every account (and not writing them down anywhere insecure), consolidating them into a single password manager _could_ arguably introduce a new single point of failure.
> - In Malan's experience, **most people** fall into the first category and would benefit from adopting a password manager — but it's a decision each person should make deliberately based on their own current habits, not blind trust in "some guy on the internet."

**Q: If password managers are so helpful, why do we still need antivirus software?**

> A: Password managers and antivirus software address **different threat categories**. Antivirus protects against malware more broadly (viruses, worms, ransomware that encrypts/deletes your data, spam-sending malware, etc.). Additionally, if malware (like a keylogger) is present on your device, it could theoretically capture your two-factor code as well if you're not fast enough entering it — so antivirus remains an important complementary layer of defense, even if the overall probability of this specific combined attack is relatively low.

**Q: With AI able to clone/sample voices, could deepfake audio be used for social engineering (e.g., impersonating a boss or bank)?**

> A: Yes — this is a real and growing concern. Malan's specific recommendation: disable voice-recognition-based authentication (e.g., "my voice is my password" systems) if a stronger alternative like push-notification 2FA is available, precisely because AI-generated deepfake audio can potentially reproduce a victim's voice saying things they never actually said.

[⬆ Back to top](#-table-of-contents)

---

## 10. Summary of Key Takeaways

1. **Authentication** proves identity ("who are you?"); **authorization** determines access rights ("what are you allowed to do?"). Both matter — being authenticated doesn't automatically mean full access should be granted.
2. Password strength grows **exponentially** with both length and character-set variety — but **length matters more** than variety. NIST recommends a minimum of 8 characters, with support for up to 64+ character passphrases.
3. **Dictionary attacks** exploit predictable, real-word passwords; **brute-force attacks** exploit short passwords by exhaustively trying every combination. Both are demonstrated as fast/trivial for weak passwords using simple Python scripts.
4. Follow **NIST best practices**: enforce minimum length, allow long passphrases, block breached/dictionary/repetitive/context-specific passwords, eliminate password hints and security questions, avoid arbitrary forced password rotation, and implement rate limiting on failed login attempts.
5. **Multi-Factor Authentication (MFA)** combines knowledge, possession, and/or inherence factors. Prefer **authenticator apps** or hardware tokens over **SMS-based OTPs**, due to SIM-swapping vulnerabilities.
6. **Credential stuffing** exploits password reuse across sites — the core defense is using a **unique password for every account**.
7. **Social engineering** and **phishing** exploit human trust rather than technical vulnerabilities — verify URLs, hover over links before clicking, never trust unsolicited requests for credentials, and manually navigate to sensitive sites instead of clicking email links.
8. **Man-in-the-middle attacks** exploit intermediary systems on the network path; proper **encryption/cryptography** (covered in the next lecture) is the core defense.
9. **Single Sign-On (SSO)** and **password managers** reduce the burden on users to create/remember many strong, unique passwords — each with its own trade-offs (SSO ties security to one primary account; password managers concentrate risk into one master password).
10. **Passkeys** represent the emerging, more secure future of authentication — replacing memorized secrets with automatically generated, device-managed cryptographic key pairs.
11. Above all: cybersecurity is a continual, deliberate **balancing act between usability and security** — the objective isn't perfect, unbreakable security (which is unrealistic and destroys usability), but rather **raising the cost and difficulty for adversaries** to a level that meaningfully deters realistic threats.

[⬆ Back to top](#-table-of-contents)

---

## 11. Quick-Reference Glossary

| Term                         | Definition                                                                                       |
| ---------------------------- | ------------------------------------------------------------------------------------------------ |
| **Authentication**           | Proving who you are (e.g., via username + password)                                              |
| **Authorization**            | Determining what an authenticated user is allowed to access                                      |
| **Dictionary attack**        | Trying real words from a wordlist as candidate passwords                                         |
| **Brute force attack**       | Systematically trying every possible character combination                                       |
| **Keyspace**                 | The total number of possible password combinations, given character set and length               |
| **NIST**                     | U.S. National Institute of Standards and Technology; publishes security best-practice guidelines |
| **Rate limiting**            | Restricting the number of allowed failed login attempts within a time period                     |
| **MFA / 2FA**                | Multi-Factor / Two-Factor Authentication — requiring 2+ distinct factor categories to log in     |
| **Knowledge factor**         | An authentication factor based on something you know (password, PIN)                             |
| **Possession factor**        | An authentication factor based on something you physically have (phone, key fob)                 |
| **Inherence factor**         | An authentication factor based on biometrics (fingerprint, face)                                 |
| **OTP (One-Time Password)**  | A temporary, single-use authentication code                                                      |
| **SIM swapping**             | Tricking a mobile carrier into transferring a victim's phone number to an attacker's SIM card    |
| **Keylogger**                | Malware that records every keystroke on an infected device                                       |
| **Credential stuffing**      | Reusing leaked username/password pairs across multiple unrelated sites                           |
| **Social engineering**       | Manipulating human trust/psychology to extract sensitive information                             |
| **Phishing**                 | Technical social engineering via fake emails/websites designed to steal credentials              |
| **Man-in-the-middle (MITM)** | An attacker intercepting/altering data as it passes through an intermediary system               |
| **Single Sign-On (SSO)**     | Logging into a third-party site using credentials from a trusted existing account (e.g., Google) |
| **Password manager**         | Software that generates, stores, and autofills unique passwords, protected by a master password  |
| **Passkey**                  | A password-free authentication method using an automatically generated public/private key pair   |

[⬆ Back to top](#-table-of-contents)

---

_End of Part 1 — Next lecture continues with cryptography, encryption, and the technical foundations behind passkeys and secure data transmission._
