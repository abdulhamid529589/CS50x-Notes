# CS50 Cybersecurity — Securing Data (Part 2)

**Instructor:** David Malan
**Course:** CS50's Introduction to Cybersecurity
**Topic:** Hashing, Salting, Cryptography, Symmetric & Asymmetric Encryption, Digital Signatures, Passkeys, Encryption in Transit/at Rest, and Quantum Computing

---

## 📑 Table of Contents

1. [Introduction: Who Else Is Responsible for Your Password?](#1-introduction-who-else-is-responsible-for-your-password)
2. [Storing Passwords Naively (Plaintext) and Why It's Dangerous](#2-storing-passwords-naively-plaintext-and-why-its-dangerous)
3. [Hashing](#3-hashing)
   - 3.1 [What Is a Hash Function?](#31-what-is-a-hash-function)
   - 3.2 [A Naive (Bad) Hash Function](#32-a-naive-bad-hash-function)
   - 3.3 [A Better (Cryptic) Hash Function](#33-a-better-cryptic-hash-function)
   - 3.4 [How Authentication Works with Hashed Passwords](#34-how-authentication-works-with-hashed-passwords)
   - 3.5 [Hashing Doesn't Solve Everything: Dictionary/Brute-Force Attacks Still Apply](#35-hashing-doesnt-solve-everything-dictionarybrute-force-attacks-still-apply)
   - 3.6 [Rainbow Tables](#36-rainbow-tables)
4. [The Problem of Identical Passwords: Salting](#4-the-problem-of-identical-passwords-salting)
   - 4.1 [The Leak: Same Password → Same Hash](#41-the-leak-same-password--same-hash)
   - 4.2 [How Salting Works](#42-how-salting-works)
   - 4.3 [Where Is the Salt Stored?](#43-where-is-the-salt-stored)
5. [Modern Hashing in Practice](#5-modern-hashing-in-practice)
   - 5.1 [Real-World Hash Output Examples](#51-real-world-hash-output-examples)
   - 5.2 [Keyspace Comparison: Old vs. Modern Hashes](#52-keyspace-comparison-old-vs-modern-hashes)
   - 5.3 [Red Flag: "Forgot Password" Emails That Contain Your Actual Password](#53-red-flag-forgot-password-emails-that-contain-your-actual-password)
   - 5.4 [NIST Recommendation on Password Storage](#54-nist-recommendation-on-password-storage)
   - 5.5 [One-Way Hash Functions Explained](#55-one-way-hash-functions-explained)
6. [Cryptography Fundamentals](#6-cryptography-fundamentals)
   - 6.1 [Codes vs. Ciphers](#61-codes-vs-ciphers)
   - 6.2 [Key Terminology: Encode/Decode vs. Encipher/Decipher (Encrypt/Decrypt)](#62-key-terminology-encodedecode-vs-encipherdecipher-encryptdecrypt)
   - 6.3 [Keys: The Configurable Secret](#63-keys-the-configurable-secret)
7. [Secret Key (Symmetric) Cryptography](#7-secret-key-symmetric-cryptography)
   - 7.1 [How Symmetric Encryption Works](#71-how-symmetric-encryption-works)
   - 7.2 [The Caesar Cipher (Rotational Cipher)](#72-the-caesar-cipher-rotational-cipher)
   - 7.3 [ROT13 and Why a Key of 26 Is Useless](#73-rot13-and-why-a-key-of-26-is-useless)
   - 7.4 [Modern Symmetric Algorithms: AES and Triple DES](#74-modern-symmetric-algorithms-aes-and-triple-des)
   - 7.5 [The Chicken-and-Egg Problem of Shared Secrets](#75-the-chicken-and-egg-problem-of-shared-secrets)
8. [Public Key (Asymmetric) Cryptography](#8-public-key-asymmetric-cryptography)
   - 8.1 [Public and Private Keys](#81-public-and-private-keys)
   - 8.2 [RSA: The Math in a Nutshell](#82-rsa-the-math-in-a-nutshell)
   - 8.3 [Diffie-Hellman Key Exchange](#83-diffie-hellman-key-exchange)
9. [Digital Signatures](#9-digital-signatures)
   - 9.1 [How Signing Works](#91-how-signing-works)
   - 9.2 [How Verification Works](#92-how-verification-works)
   - 9.3 [Why Digital Signatures Are More Secure Than Handwritten Ones](#93-why-digital-signatures-are-more-secure-than-handwritten-ones)
10. [Passkeys (Web Authentication) Explained in Detail](#10-passkeys-web-authentication-explained-in-detail)
11. [Encryption in Transit vs. End-to-End Encryption](#11-encryption-in-transit-vs-end-to-end-encryption)
12. [Deleting Files: What Actually Happens](#12-deleting-files-what-actually-happens)
    - 12.1 [Why "Delete" Doesn't Really Delete](#121-why-delete-doesnt-really-delete)
    - 12.2 [Secure Deletion](#122-secure-deletion)
13. [Full-Disk Encryption (Encryption at Rest)](#13-full-disk-encryption-encryption-at-rest)
    - 13.1 [How It Protects You](#131-how-it-protects-you)
    - 13.2 [Ransomware: The Dark Side of Encryption](#132-ransomware-the-dark-side-of-encryption)
14. [Looking Ahead: Quantum Computing](#14-looking-ahead-quantum-computing)
15. [Q&A Highlights from the Lecture](#15-qa-highlights-from-the-lecture)
16. [Summary of Key Takeaways](#16-summary-of-key-takeaways)
17. [Quick-Reference Glossary](#17-quick-reference-glossary)

---

## 1. Introduction: Who Else Is Responsible for Your Password?

- Last lecture focused on the _user's_ responsibility to choose and protect strong passwords.
- This lecture shifts focus to the **other party** in any account relationship: the **server/application** that must store your username and password long-term so it can authenticate you on future visits.
- Central question: **how should a company responsibly store the credentials you give them?**

[⬆ Back to top](#-table-of-contents)

---

## 2. Storing Passwords Naively (Plaintext) and Why It's Dangerous

- The simplest (and worst) approach: store a plain text file (or database) of `username:password` pairs.
  - Example: `alice:apple`, `bob:banana`
  - This is, in fact, historically how some systems (including early Linux systems) approached credential storage, sometimes with a bit of extra metadata alongside the entries.
- **The core danger:** If an adversary breaches the server/database, they instantly obtain **every user's actual password** in readable form.
- This directly enables the **credential stuffing** attack discussed in the previous lecture — attackers can now try these exact same username/password combinations on other websites, banking on the extremely common habit of password reuse.
- **Design principle:** As administrators/developers, we should assume a breach will happen eventually and design systems to **minimize the damage** when — not if — that occurs.

[⬆ Back to top](#-table-of-contents)

---

## 3. Hashing

### 3.1 What Is a Hash Function?

> **Hashing** is the process of taking an input (e.g., a password) and mathematically transforming it into a **hash value** — a seemingly random, fixed-length string of characters.

- Conceptually a **black box**: input goes in, output (the hash) comes out.
- Can be thought of as:
  - A device/machine.
  - A piece of software.
  - A mathematical function, `f(x)`, where `x` is the input (password) and `f(x)` is the output (hash).
- **Key idea:** Store the _hash_ of the password in the database — **never the password itself**.

### 3.2 A Naive (Bad) Hash Function

- Illustrative (bad) example: hash based only on the **first letter** of the input.
  - `apple` → starts with "A" → hash = `1`
  - `banana` → starts with "B" → hash = `2`
  - `cherry` → starts with "C" → hash = `3`
- **Problem:** This leaks far too much information.
  - Multiple different passwords can share the same first letter (`apple` and `avocado` both hash to `1`).
  - Seeing a hash of `1` immediately tells an attacker the password starts with "A" — reducing their brute-force search space to roughly **1/26th** of the full keyspace.
- **Lesson:** A good hash function should look **cryptic and unguessable** — it must not leak _any_ information about the structure of the original input.

### 3.3 A Better (Cryptic) Hash Function

- A properly designed hash function produces output that looks like meaningless, random mixed-case text/numbers, e.g.:
  - `apple` → `..ekWXa83dhiA` (illustrative example used in lecture)
  - `banana` → a completely different-looking string
  - `cherry` → yet another distinct-looking string
- These outputs reveal **no discernible pattern or relationship** to the original input, even though a defined mathematical formula produced them deterministically.
- **Improved database design:** Instead of storing `alice:apple` and `bob:banana`, the database stores `alice:<hash of apple>` and `bob:<hash of banana>`.
- **Benefit:** If this database is breached, the attacker obtains only hash values — not usable passwords — making it much harder to directly reuse the data in a credential-stuffing attack.

### 3.4 How Authentication Works with Hashed Passwords

Since the actual password is never stored, how does the server verify a login attempt? The process:

**At registration:**

1. User types username (`alice`) and password (`apple`).
2. These are transmitted to the server.
3. The server runs the password through the hash function, producing a hash value.
4. The server stores `alice` + the **hash** — and immediately discards/forgets the plaintext password (`apple`) from memory.

**At subsequent logins (a day, week, or year later):**

1. User types username and password again.
2. The server takes the newly entered password and **re-runs it through the exact same hash function**.
3. The server compares this freshly computed hash against the hash stored in its database.
4. If they match, the user is authenticated — even though the server never stored (or compared against) the original plaintext password at all.

> 💡 In practice, developers virtually never write their own hash function from scratch — they use trusted, well-vetted **libraries** written by cryptography specialists, minimizing the risk of subtle bugs or mathematical weaknesses.

### 3.5 Hashing Doesn't Solve Everything: Dictionary/Brute-Force Attacks Still Apply

Even with hashing in place, hashing only slows attackers down — it does not stop them entirely:

- **Dictionary attack on hashes:** An attacker with a stolen hash file can still take a dictionary/word list (e.g., a list of fruit names), hash _each_ candidate word using the same known hash function, and compare the resulting hash against entries in the stolen database. A match reveals the original password.
- **Brute-force attack on hashes:** Similarly, an attacker can try every possible password (`0000`, `00000001`, `AAAAAAAA`, etc.), hash each candidate, and compare against the stolen hash file — eventually finding matches for `apple`, `banana`, etc.
- **Key distinction from the plaintext scenario:** These attacks now require real **computational work** (hashing each guess) rather than a trivial string comparison — this is more expensive for large databases (thousands/millions of entries), but not impossible.
- **Core theme (repeated from Part 1):** Hashing doesn't make a system _impenetrable_ — it **raises the cost/complexity** for the adversary, buying time and reducing the probability of a quick, cheap compromise.

### 3.6 Rainbow Tables

> A **rainbow table** is a precomputed table mapping candidate passwords (dictionary words, common passwords, brute-force candidates) to their corresponding hash values — built and stored by an attacker **in advance**, before ever seeing a specific stolen database.

- Instead of hashing each guess on the fly during an attack, the attacker simply performs fast **lookups** in an already-computed table (essentially a giant spreadsheet or CSV of "password → hash" pairs).
- This dramatically speeds up the cracking process compared to computing each hash in real time.
- **Limiting factor:** For certain hash functions and long/complex hash outputs, building a comprehensive rainbow table is infeasible — it can require **terabytes or petabytes** of storage, imposing a real cost (money, hardware, time) on the attacker.
- **Defense against rainbow tables:** This is precisely one of the motivations for **salting** (see Section 4) — salting defeats precomputed rainbow tables because the same password will produce different hashes for different users/salts.

[⬆ Back to top](#-table-of-contents)

---

## 4. The Problem of Identical Passwords: Salting

### 4.1 The Leak: Same Password → Same Hash

- Suppose two different users — say, Carol and Charlie — happen to choose the exact same password (`cherry`), purely by coincidence.
- Since a hash function is deterministic (same input always produces the same output, absent randomness), **Carol's and Charlie's stored hash values will be identical.**
- **Why this matters:** If an attacker breaches the database, they may not know what Carol's or Charlie's password actually is — but they _do_ know that **both users share the same password**. This alone is valuable leaked information:
  - It narrows the guessing problem.
  - It may allow inference based on relationships between the two users (e.g., they're related, share interests, or use similar patterns).
- This is a very common real-world problem: with any user population, some fraction will inevitably reuse the same common password (e.g., `1234`, `12345678`) — creating clusters of identical hashes that leak the fact of a shared password, even without revealing what it is.

### 4.2 How Salting Works

> **Salting** means adding a small amount of additional, randomly generated data (the "salt") as a second input to the hash function, alongside the password itself — "sprinkling salt" onto the hashing process to perturb its output.

- Instead of a hash function taking just `password → hash`, it now takes `(password, salt) → hash`.
- The salt is **not meant to be secret** — it's simply there to ensure that even if two users pick the identical password, their resulting hash values will differ (as long as their salts differ).
- **Example from the lecture:**
  - Carol's password `cherry` + salt `50` → produces one specific hash value (prefixed with `50`).
  - Charlie's password `cherry` + salt `49` → produces a **completely different-looking** hash value (prefixed with `49`), even though the underlying password is identical.
- **Important nuance:** Salts are typically randomly generated by the server automatically — the user never chooses or even sees their own salt.
- **Residual risk:** If, by chance, two users are randomly assigned the _same_ salt (increasingly likely as the number of users grows and the salt length is short), some information could still leak. This is a probability-reduction technique, not an absolute guarantee — consistent with the broader "raise the bar for attackers" theme.

### 4.3 Where Is the Salt Stored?

- The salt is stored **directly within the hash value itself** — by convention, often as a prefix (e.g., the first two characters).
- **Login/verification flow with salting:**
  1. User (e.g., Carol) types her username and password.
  2. The server looks up Carol's stored hash value and reads off her salt (e.g., the first two characters).
  3. The server takes the password Carol just typed, combines it with **her specific salt**, and computes the hash.
  4. If this newly computed hash matches Carol's stored hash exactly, she is authenticated.
  5. The same logic applies independently for Charlie, using **his own** stored salt — even though both used the same password `cherry`, each computation uses a different salt and therefore produces (and correctly matches) a different stored hash.

[⬆ Back to top](#-table-of-contents)

---

## 5. Modern Hashing in Practice

### 5.1 Real-World Hash Output Examples

- The lecture's earlier examples used a deliberately simplified hash function (short salt, short hash output) purely for pedagogical clarity.
- **Modern hash functions** in real-world use produce **much longer** output values — visually much longer strings of letters/numbers on screen.
- These longer outputs correspond to using **many more bits** (0s and 1s) internally — even though humans see them rendered as letters and digits.
- **Convention:** Many modern hash storage formats include a short "algorithm identifier" code between dollar signs at the start of the stored value (e.g., something like `$y$...`), indicating exactly which hash function/algorithm was used to generate the rest of the string. This is documented publicly for any given hashing standard.

### 5.2 Keyspace Comparison: Old vs. Modern Hashes

| Hash Function Era                                                 | Approximate Total Possible Hash Values                                                                          |
| ----------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| Older/simplified hash (short output, as used in the lecture demo) | ~18 quintillion                                                                                                 |
| Modern hash function (long output, many more bits)                | An astronomically larger number — so large the lecture jokes even mathematicians might struggle to pronounce it |

- Even 18 quintillion possibilities are theoretically crackable "given enough time, money, and cloud computing" — but modern hash functions push the number so high that brute-forcing becomes practically infeasible within any realistic timeframe, **absent other weaknesses in the system** (e.g., a trivially weak underlying password).

### 5.3 Red Flag: "Forgot Password" Emails That Contain Your Actual Password

- A major real-world red flag: if you click "Forgot Password" on a website and receive an email containing your **actual original password** (rather than a reset link), this is **strong evidence of poor security practice**.
- **Why this is a problem:** If a company can email you your exact password, that means:
  - They stored it in a way that lets them retrieve/read it — i.e., not (properly) hashed.
  - Hashing is designed to be **irreversible** — if the company itself can't reverse the process, they shouldn't be able to email you the plaintext password either.
  - This strongly implies their password database is likely stored in **plaintext or a reversible format**, making it highly vulnerable if breached.
- **Recommended response:**
  - **Minimally:** Stop using that service, and ensure that password isn't reused anywhere else.
  - **Maximally:** Consider contacting the company to raise your concern, potentially referencing authoritative material (e.g., this lecture or NIST documentation).

### 5.4 NIST Recommendation on Password Storage

Continuing from the previous lecture's NIST guidance, a directly relevant quote covered here:

> "Verifiers shall store memorized secrets in a form that is resistant to offline attacks. Memorized secrets SHALL be salted and hashed using a suitable one-way key derivation function. The purpose... is to make each password guessing trial by an attacker who has obtained a password hash file expensive, and therefore, the cost of a guessing attack high or prohibitive."

- This formalizes everything covered in Sections 3 and 4: passwords should be **both salted and hashed** using a proper one-way function specifically designed for this purpose (not just any general-purpose hash function).
- Reinforces the recurring theme: even official guidance frames this not as achieving "perfect" security, but as making attacks **prohibitively expensive**.

### 5.5 One-Way Hash Functions Explained

- **Modern password hash algorithm families mentioned:** SHA-2 and SHA-3 (among others), which are sophisticated, well-vetted mathematical/cryptographic functions.
- **Defining property — "one-way":**
  - Takes an input of **arbitrary length** (a password of any length, short or long).
  - Produces an output of **fixed length** (always the same number of bits/characters, regardless of input length).
  - This means the function maps a **huge (technically near-infinite) domain** of possible inputs onto a **finite range** of possible outputs.
- **Mathematical implication — collisions are inevitable:**
  - If you map a very large set of possible inputs onto a smaller, finite set of possible outputs, by simple pigeonhole reasoning, **multiple different inputs must sometimes map to the same output** (a "collision").
  - Simplified illustration: with the naive "first letter" hash function, hash value `1` could correspond to `apple`, `avocado`, or any other "A" word — you cannot know which one just by looking at the hash.
  - **Practical implication:** In theory, it might be technically possible for a _different_ input than your actual password to produce the exact same hash and thus also grant access to your account — but with modern hash functions using enormous numbers of bits, the odds of this happening (or of anyone finding such a collision) are astronomically, practically negligible.
- **Why this matters for security:** Because the function is one-way, even someone with full access to the hash value cannot mathematically "reverse" it to recover the original password directly — they can only guess-and-check (dictionary/brute-force/rainbow table attacks), which is exactly the added protection hashing provides.
- These one-way hash functions are also called **cryptographic hash functions**, and they belong to the broader discipline of **cryptography** — the study and practice of securing data, both in transit and at rest.

[⬆ Back to top](#-table-of-contents)

---

## 6. Cryptography Fundamentals

> **Cryptography** is the practice and study of securing data — both while it is being transmitted from one point to another, and while it is sitting at rest on a system.

### 6.1 Codes vs. Ciphers

Cryptography can be broadly divided into two historical categories:

**Codes:**

- A mapping between **code words** and their true meanings, typically documented in a shared **codebook** (the lecture shows an example of an actual historical codebook, over 100 years old).
- To send a secure message, the sender looks up words/phrases in the codebook and substitutes them with code words; the recipient does the reverse lookup to recover the original meaning.
- **Weaknesses:**
  - **Cumbersome:** Requires manually flipping through potentially hundreds of pages of a physical book.
  - **Single point of failure:** If either party's codebook is physically stolen, **all past and future messages** become readable to the adversary (assuming the compromise isn't detected and the code isn't changed).

**Ciphers:**

- More **algorithmic** in nature — operating on individual letters (or, in modern computing, individual bits) rather than whole words or phrases.
- Historical examples:
  - **Ralphie's Little Orphan Annie Secret Decoder Pin** (from the film _A Christmas Story_) — a simple mechanical device implementing a letter-to-number substitution cipher.
  - **The Enigma Machine** (Nazi Germany, WWII) — a more sophisticated mechanical/electrical cipher device using rotors and wiring to implement a substitution cipher, configurable with different settings.
- Ciphers are **repeatable processes** that can be applied systematically to every letter (or bit) of a message — well-suited to implementation in modern software.

### 6.2 Key Terminology: Encode/Decode vs. Encipher/Decipher (Encrypt/Decrypt)

| Term                         | Meaning                | Associated With    |
| ---------------------------- | ---------------------- | ------------------ |
| **Encode**                   | Plaintext → Codetext   | Codes (codebooks)  |
| **Decode**                   | Codetext → Plaintext   | Codes (codebooks)  |
| **Encipher** (= **Encrypt**) | Plaintext → Ciphertext | Ciphers/algorithms |
| **Decipher** (= **Decrypt**) | Ciphertext → Plaintext | Ciphers/algorithms |

- "Encrypt" and "decrypt" are the more commonly used modern terms, and are treated as synonyms for "encipher" and "decipher" respectively throughout the rest of the lecture.

### 6.3 Keys: The Configurable Secret

- Best practice in cryptography: use **publicly known, well-documented, well-tested algorithms** — but keep one specific piece of information secret: the **key**.
- A **key** customizes/configures how a shared algorithm behaves for a specific pair of communicating parties, ensuring that even if two people use the exact same cipher algorithm, their ciphertext output won't be identical for the same plaintext (which would otherwise leak information, just as with unsalted password hashes).
- In practice, keys are:
  - Not physical objects — just **very large numbers** (sometimes with special mathematical properties).
  - Sometimes displayed to humans as sequences of letters/punctuation, but fundamentally reducible to numbers (or, at the lowest level, sequences of 0s and 1s).
- Analogy: like a physical key to a lock — both parties (sender and recipient) generally need a copy of the relevant key(s) to encrypt and decrypt messages between them.

[⬆ Back to top](#-table-of-contents)

---

## 7. Secret Key (Symmetric) Cryptography

### 7.1 How Symmetric Encryption Works

> **Secret key cryptography** (also called **symmetric key encryption**) is a scheme where **both the sender and receiver use the exact same key** for both encryption and decryption.

- Security relies entirely on the secrecy of this one shared key — if A and B want to communicate securely, both must keep their shared key confidential.
- Conceptually, the encryption process is a black box: input = plaintext + key → output = ciphertext.
- The purpose of the key: ensures that even if many different people are using the exact same encryption _algorithm_, their resulting ciphertext for the same message will differ, since each pair uses a different (secret) key.

### 7.2 The Caesar Cipher (Rotational Cipher)

- Attributed historically to **Julius Caesar** — one of the oldest known ciphers.
- **Mechanism:** Each letter of the plaintext is "rotated" a fixed number of positions through the alphabet, determined by the key.
  - Key = 1: `A → B`, `B → C`, and so on (wrapping around from `Z` back to `A`).
  - Key = 13: `A → N` (13 positions away).
- **Weakness:** Because the English alphabet has only 26 letters, there are only **25 meaningful keys to try** (a key of 0 or 26 does nothing). This makes the Caesar cipher trivially breakable by brute force — even by hand with pencil and paper, and nearly instantaneously with basic code.
- The lecture includes a **live decryption challenge**: an enciphered message is shown on screen (using an unrevealed rotational key), inviting the audience to brute-force the key themselves — ultimately revealed to reference the _A Christmas Story_ secret decoder pin scene as a playful nod, underscoring how easily such simple ciphers can be cracked "by hand."

### 7.3 ROT13 and Why a Key of 26 Is Useless

- **ROT13** ("rotate 13") is a specific, well-known instance of the rotational cipher using a fixed key of 13.
- Notably **not intended to be secure** — historically used casually online (e.g., to obscure movie spoilers) since it's trivially reversible with a single click/tool.
- **Key insight:** A key of **26** would output ciphertext identical to the original plaintext (since there are exactly 26 letters in the alphabet), making it functionally useless — hence the tongue-in-cheek internet joke that "ROT26 is twice as secure as ROT13" (which is, of course, false).

### 7.4 Modern Symmetric Algorithms: AES and Triple DES

- The Caesar cipher is useful for teaching concepts, but is **not remotely secure** for real-world use — suitable only for trivial scenarios (e.g., a note passed in a middle-school classroom where the "adversary," a teacher, has neither the motive nor tools to brute-force it).
- **Modern, industry-standard symmetric encryption algorithms** mentioned:
  - **AES** (Advanced Encryption Standard)
  - **Triple DES** (3DES)
- Both are extensively mathematically vetted and widely deployed in real-world systems (phones, laptops, desktops) — using vastly larger, more sophisticated keys that make brute-forcing computationally infeasible, unlike a 25-option Caesar key.

### 7.5 The Chicken-and-Egg Problem of Shared Secrets

- Symmetric encryption fundamentally assumes **sender and receiver already share a secret key** in advance.
- **The core problem:** How do two parties who have _never communicated before_ establish a shared secret securely?
  - Example: visiting `amazon.com` or `gmail.com` for the very first time — you have no pre-existing personal relationship or shared secret with these companies, yet you want your connection to be encrypted (as signaled by **HTTPS** in your browser's URL bar, where the "S" stands for "Secure").
  - **The paradox:** The only way to safely establish a shared secret would seemingly be to send it securely — but you can't communicate securely _without_ already having a shared secret. This is a deadlock.
- This chicken-and-egg problem is precisely what motivates the next major topic: **public key (asymmetric) cryptography**.

[⬆ Back to top](#-table-of-contents)

---

## 8. Public Key (Asymmetric) Cryptography

### 8.1 Public and Private Keys

> **Public key cryptography** (a.k.a. **asymmetric key cryptography**) uses **two mathematically related keys** per person, instead of one shared key between two people.

- Everyone using this system has:
  - A **public key** — meant to be shared with the entire world (in email signatures, on websites, on social media, etc.). Not secret at all.
  - A **private key** — meant to be kept strictly confidential, stored only on the owner's own device(s), never shared.
- These two keys are **automatically generated** by your device (unlike passwords, which humans choose and remember) and share a special mathematical relationship.
- **Core property:** Data encrypted using someone's **public key** can only be decrypted using that same person's corresponding **private key** — and no other key in the world will work.

**Encryption/decryption flow:**

1. Sender obtains the recipient's **public key** (freely available).
2. Sender encrypts their plaintext message using that public key → produces ciphertext.
3. Recipient decrypts the ciphertext using their own **private key** → recovers the original plaintext.

- **Named algorithms mentioned:** RSA, Diffie-Hellman, MQV, among others. **RSA** is highlighted as one of the most widely recognized/used.

### 8.2 RSA: The Math in a Nutshell

A simplified conceptual walkthrough (the lecture explicitly notes this is a significant oversimplification):

1. The device chooses two very large **prime numbers**, `p` and `q` (a prime number can only be evenly divided by itself and 1).
2. It multiplies them together: `n = p × q`.
3. This value `n`, along with other values generated as part of the algorithm (`e` for the public exponent, `d` for the private exponent), is used in the encryption/decryption math.
4. **Security rests on the fact that**, given only the product `n`, it is computationally extremely difficult to figure out what the original prime factors `p` and `q` were (a problem known more generally as _integer factorization_).

**Simplified RSA formulas presented:**

- **Encryption:** `c = m^e mod n` (raise message `m` to the power of public exponent `e`, then take the remainder when divided by `n`) → produces ciphertext `c`.
- **Decryption:** `m = c^d mod n` (raise ciphertext `c` to the power of private exponent `d`, then take the remainder when divided by `n`) → recovers original message `m`.
- This relies on **modular arithmetic** — repeated division to compute remainders — as the mathematical backbone of the scheme.

### 8.3 Diffie-Hellman Key Exchange

> **Diffie-Hellman** is an alternative public-key-based technique whose specific goal is **key exchange**: allowing two parties to mathematically arrive at the same shared secret value, without ever transmitting that secret directly across the network — and without an eavesdropper being able to derive it either, even if they intercept every message exchanged.

**Simplified mechanics:**

1. Both parties publicly agree in advance on:
   - A **generator** value `g` (e.g., as simple as `2`).
   - A large **prime number** `p`.
2. **Alice** picks her own secret private value `a`, and computes: `A = g^a mod p`.
3. **Bob** picks his own secret private value `b`, and computes: `B = g^b mod p`.
4. Alice and Bob exchange their computed public values `A` and `B` over the (insecure) network.
5. **Alice** then computes: `s = B^a mod p`.
6. **Bob** then computes: `s = A^b mod p`.
7. Thanks to properties of modular exponentiation, **both computations yield the exact same value**, `s = g^(a×b) mod p` — their shared secret — **even though neither `a` nor `b` was ever transmitted**, and even though anyone eavesdropping only saw `g`, `p`, `A`, and `B` (which is not enough information to derive `s` without solving what's known as the discrete logarithm problem).
8. Once Alice and Bob share this secret `s`, they can use it as the key for any symmetric encryption algorithm discussed earlier (e.g., AES, Triple DES).

> ⚠️ As with hashing and RSA, the lecture emphasizes: **do not attempt to invent your own cryptographic algorithms.** These systems represent decades of rigorous, expert-level mathematical vetting — real-world security depends on relying on standards that have been extensively tried, tested, and proven, not on ad hoc, homegrown schemes.

[⬆ Back to top](#-table-of-contents)

---

## 9. Digital Signatures

> A **digital signature** uses the public/private key relationship in **reverse** compared to encryption — allowing someone to cryptographically prove that a particular message or document was authored/approved by them specifically, without necessarily keeping the message itself secret.

**Named algorithms mentioned:** DSA, ECDSA, RSA (RSA can be used for both encryption _and_ signatures).

### 9.1 How Signing Works

1. Start with a **message** — this could be a letter, an essay, a contract, or any document, of any length.
2. Run the message through a **cryptographic hash function** (as discussed in Section 3/5) to produce a fixed-length **hash** — condensing a potentially huge document down to a compact, fixed-size representation.
3. Take that hash value and the signer's own **private key**, and feed both into a digital signature algorithm.
4. The output of this process is the **signature** — itself just a large number or string of seemingly random text.
5. The signer sends both the original **message** and this computed **signature** to the recipient.

> Note the reversal compared to public key _encryption_: here, the **private key is used to produce** the signature (not to decrypt), and — as we'll see next — the corresponding **public key is used to verify** it (not to encrypt).

### 9.2 How Verification Works

Upon receiving both the message and the claimed signature, the recipient:

1. Runs the received message through the **same publicly known hash function** used by the sender, producing their own freshly computed hash.
2. Takes the sender's claimed **signature** and the sender's **public key**, and decrypts the signature using that public key.
3. If the result of this decryption **exactly matches** the hash the recipient computed themselves in step 1, this proves — with extremely high mathematical confidence — that:
   - The signature could only have been produced using the corresponding **private key**.
   - Since only the legitimate signer possesses that private key, the message must indeed have been signed by that specific person (assuming their private key was never stolen/compromised).

- Trust in _whose_ public key is really "David's" (for example) typically relies on either:
  - A centralized, trusted **registry/authority** that maintains verified public keys for individuals or organizations, or
  - A more **distributed** trust model — e.g., trusting a public key because it's published in someone's email signature, personal website, or verified social media/professional profile (like LinkedIn), assuming you already trust _those_ channels.

### 9.3 Why Digital Signatures Are More Secure Than Handwritten Ones

- Unlike a handwritten signature on paper — which can be **photographed, copied, or traced** by anyone — a digital signature's security depends entirely on the signer's **private key remaining private**.
- As long as that private key has not been compromised or stolen, forging a valid digital signature is considered computationally infeasible — the probability is so vanishingly small it's treated as a non-concern under normal circumstances.
- This makes digital signatures, in a meaningful sense, **objectively more secure** than traditional handwritten signatures.

[⬆ Back to top](#-table-of-contents)

---

## 10. Passkeys (Web Authentication) Explained in Detail

Building directly on public key cryptography and digital signatures, this section revisits and deepens the concept of **passkeys** first introduced (as a preview) in the previous lecture. More formally, this is an implementation of the **Web Authentication (WebAuthn)** standard.

**Goal:** Move toward a **passwordless** world — eliminating the need to create, remember, or store traditional passwords altogether.

**Registration flow (creating a passkey for a new site/app):**

1. Instead of prompting you to create a username and password, the website/app prompts your device to create a **passkey**.
2. Your device asks you to verify yourself locally — via fingerprint, face scan, or a PIN — confirming, with high probability, that you're authorized to use this device.
3. Your device then automatically **generates a brand-new public/private key pair specifically for this one website/app**.
4. Your device sends the **public key** (plus your user ID/username) to the website — **no password is ever created or transmitted**.
5. The corresponding **private key** stays securely on your device (or synced privately across your own devices via a cloud service), never shared with the website.
6. This entire process repeats independently and automatically for **every different website/app** you register with — each gets its own unique key pair.

**Login flow (authenticating on a return visit):**

1. The website sends your device a **"challenge"** — a randomly generated piece of data (a number, word, or phrase) specific to that login attempt.
2. Your device uses your stored **private key** to digitally sign this challenge (exactly per the digital signature process in Section 9).
3. Your device sends the resulting **signature** back to the website.
4. The website uses the **public key** it stored during your original registration to verify the signature against the challenge it just sent.
5. If the verification succeeds — i.e., decrypting the signature with your public key yields back the exact same challenge value — the website can conclude, with very high mathematical confidence, that you are indeed who you claim to be (since only your private key could have produced a valid signature verifiable by your public key).

**Implications:**

- Society moves away from needing to remember dozens/hundreds/thousands of distinct passwords.
- **Requirement:** You must not lose access to the device(s) that hold your private keys — though cloud synchronization services (from Apple, Google, Microsoft, etc.) increasingly allow passkeys to sync securely across a user's own devices, ideally without even the cloud provider itself being able to see the actual private keys.
- **Current limitation:** Not yet universally supported across all websites/apps, but adoption is expected to expand steadily "in the coming weeks, months, and years."
- **Recognizing passkeys in the wild:** If a website asks you to register using your fingerprint, face, or a PIN — and **never** asks you to create a traditional password — it is very likely using passkey/WebAuthn technology.

[⬆ Back to top](#-table-of-contents)

---

## 11. Encryption in Transit vs. End-to-End Encryption

> **Encryption in transit** ensures your data is encrypted/scrambled while actively moving from point A to point B across a network (e.g., between you and Amazon, Gmail, WhatsApp, or any other online service).

- **Goal:** Prevent any unauthorized intermediary ("machine in the middle") from being able to read your data as it travels across the internet.

**Critical nuance — encryption in transit is not the same as end-to-end security:**

- Consider Alice communicating with Bob, potentially through an intermediary/eavesdropper "Eve" — which, in a very real-world sense, could simply represent a service provider like Gmail or Outlook.
- Alice might have a secure, encrypted connection **to Gmail**. Bob might also have a secure, encrypted connection **to Gmail**. But this does **not** guarantee that Alice's communication is secure **all the way through to Bob** — security is **not transitive**.
- **Implication:** The email provider itself (e.g., Google) technically _can_ have the ability to read the actual content of the emails passing through its own servers, even though the connections on either end (Alice↔Gmail and Bob↔Gmail) are individually encrypted.
- Similar reasoning applies to services like **Zoom** for video conferencing: an encrypted connection to Zoom doesn't necessarily prevent Zoom itself from having access to your conversation content.
- In practice, whether or not a provider actually reads your data may depend on internal policies and access controls (most employees likely don't have such access/authorization) — but **technically**, the capability may exist unless stronger protections are used.

> **End-to-end encryption (E2EE)** is the stronger guarantee: it ensures that **only the sender and the intended recipient** can decrypt and read the actual content — **not even the service provider** carrying the data in between can access it, regardless of how many intermediary servers/companies the data passes through.

- With properly implemented E2EE, any intermediary only ever sees ciphertext (meaningless scrambled data) — without the correct key, they cannot recover the original plaintext.
- **Why companies may not always offer E2EE:** Some services commercially benefit from having access to user data (e.g., for advertising or data mining purposes), creating a potential conflict of interest between user privacy and business incentives.
- **Named examples of E2EE-supporting services** discussed: **iMessage** (Apple) and **WhatsApp** — both advertised as offering end-to-end encryption, meaning (if implemented correctly and honestly) not even employees of those companies can read message content, despite the data passing through their servers.
- **Takeaway for users:** Whether you get end-to-end encryption depends on the specific service and its implementation — it is not automatically guaranteed just because a connection shows as "encrypted" or "secure" in general.

[⬆ Back to top](#-table-of-contents)

---

## 12. Deleting Files: What Actually Happens

### 12.1 Why "Delete" Doesn't Really Delete

- Data on any storage device (hard drive, solid-state drive/SSD, USB stick) is ultimately just a very large collection of **0s and 1s (bits)**.
- **Common misconception:** Dragging a file to the Recycle Bin (Windows) or Trash (macOS), and even **emptying** that Recycle Bin/Trash, does **not** actually erase the underlying data.
- **What actually happens:** The operating system maintains an internal table/index (conceptually like a two-column spreadsheet) mapping file names/locations to the specific physical bits on the storage device that represent that file's content.
  - "Deleting" a file (even after emptying the Recycle Bin/Trash) typically just **removes the entry from this lookup table** and marks that physical space as available for reuse.
  - The actual bits representing your old file's content **remain physically present on the drive** until that space happens to get overwritten by new data at some future point.
- **Practical implication:** Shortly after "deleting" a file — even after creating or downloading new files afterward — remnants of the original sensitive file (a Word document, spreadsheet, image, etc.) may still physically exist on the device, recoverable by someone with the right tools, until that specific space is eventually overwritten.

### 12.2 Secure Deletion

> **Secure deletion** actually removes/destroys the underlying data, rather than merely forgetting where it is.

- **Naive/impractical approach:** Simply erasing the bits entirely (leaving nothing) is not practical — this would effectively shrink your usable storage capacity permanently, which is wasteful.
- **Practical secure deletion approach:** **Overwrite** all the bits that represented the sensitive file with new, meaningless data:
  - Change them all to `0`s, **or**
  - Change them all to `1`s, **or**
  - Overwrite them with random `0`s and `1`s.
- Any of these approaches effectively destroys the original information beyond recovery, while keeping the storage space reusable.

[⬆ Back to top](#-table-of-contents)

---

## 13. Full-Disk Encryption (Encryption at Rest)

> **Encryption at rest** refers to data being encrypted while simply sitting stored on a device — as opposed to encryption in transit, which applies to data actively moving across a network. **Full-disk encryption** is a common real-world implementation of encryption at rest, applied to an entire storage device.

### 13.1 How It Protects You

- With full-disk encryption enabled, **all data on the device** is stored in encrypted (scrambled/random-looking) form by default.
- Data is only **decrypted automatically and temporarily** when you successfully authenticate — e.g., via your password, fingerprint, or facial recognition — modern hardware can do this decryption very quickly in practice.

**Key benefits:**

1. **Theft protection:** If your device is stolen while locked/powered off, the thief cannot access your actual data — it appears as meaningless random bits without your credentials. They might still be able to wipe and resell the device, but the _data_ itself remains protected.
2. **Effective secure deletion when discarding/reselling a device:** If you plan to donate, resell, or recycle a device, and it has strong full-disk encryption enabled with a hard-to-guess password, your data is — for all practical purposes — already securely "deleted," since a new owner without your password/biometrics cannot decrypt and read any of it.

**Why this matters even more with modern hardware:**

- Modern storage hardware (especially SSDs/flash-based storage) can develop failures over time.
- Built-in device firmware may detect failing memory sections and **refuse to allow further overwrites** to those specific sections (to prevent further hardware damage) — meaning attempts to securely overwrite old data (Section 12.2) might not always succeed on every part of an aging drive.
- **Practical recommendation:** Enable full-disk encryption **from the very beginning**, when you first set up a device — this way, even if parts of the drive later degrade and become unwritable, you can trust that whatever data exists there was _already_ encrypted from the start, rather than relying on being able to overwrite it later.

**Trade-off (usability vs. security, once again):** If you lose your password, your fingerprint changes significantly, or your facial features change enough to fail recognition, you risk being **permanently locked out of your own data** — reinforcing the recurring theme that stronger security often comes at some usability cost.

### 13.2 Ransomware: The Dark Side of Encryption

> **Ransomware** is a category of malicious attack where adversaries who gain unauthorized access to a system **deliberately encrypt the victim's own data**, then demand payment (commonly in cryptocurrency) in exchange for the decryption key needed to recover it.

- Unlike attacks focused on subtle data theft or misuse (e.g., spam relaying, cryptocurrency mining using stolen computing resources), ransomware attacks are overtly disruptive and extortionate.
- **Real-world targets** cited in the lecture: corporate networks, **hospital systems**, and **city/municipal government computer networks** — all high-stakes environments where data unavailability can have serious real-world consequences, increasing pressure to pay.
- **The extortion risk:** Even if a victim pays the demanded ransom, there is no guarantee the attackers will actually provide a working decryption key — a fundamental risk inherent to any ransom scenario.
- **Broader takeaway:** The same encryption technology that protects users from theft (Section 13.1) can be **weaponized against them** when attackers, rather than the legitimate owner, control the encryption key — illustrating that powerful security tools are inherently dual-use.

[⬆ Back to top](#-table-of-contents)

---

## 14. Looking Ahead: Quantum Computing

- Traditional computing represents information using **bits**, each of which is strictly either a `0` or a `1` at any given time.
- **Quantum computing**, leveraging principles of quantum mechanics, introduces the **qubit (quantum bit)** — which, remarkably, can represent **both 0 and 1 simultaneously** (a property related to _superposition_).
- **Exponential scaling of computational states:**
  - 1 qubit → 2 states at once.
  - 2 qubits → 4 states at once.
  - 3 qubits → 8 states at once.
  - 32 qubits → roughly 4 billion states at once.
- **Security implications:** Much of today's cryptographic security (hashing, brute-forcing resistance, RSA's reliance on factoring large primes, etc.) depends on the assumption that trying every possibility would take an **impractically long time** using conventional (classical) computing.
  - If adversaries gain access to sufficiently powerful quantum computing **before** defenders do, the massively parallel computational power of qubits could potentially render much of today's cryptographic infrastructure **insecure** — tasks assumed to take millennia classically might become feasible in a dramatically shorter time.
- **Present-day outlook:** This is framed as a **forward-looking concern** rather than an immediate, practical threat — the hope/expectation is that defenders (governments, industry, researchers) will gain access to robust quantum-resistant cryptographic techniques at the same time as, or ideally before, adversaries gain access to quantum computing capabilities capable of breaking current standards. Cryptographic algorithms and standards are expected to continue evolving in response to this emerging technology (an active area of research known as **post-quantum cryptography**, though not named explicitly in the lecture).

[⬆ Back to top](#-table-of-contents)

---

## 15. Q&A Highlights from the Lecture

**Q: Since developers usually use existing hash-function libraries rather than writing their own, doesn't that make it easier for attackers to "reverse" the hash using those same widely available libraries?**

> A: Not quite — you _can_ use the same public library to hash guesses and compare them against stolen hash values (this is exactly how dictionary/brute-force attacks against hashes work), but a good hash function is still **not mathematically reversible** in the direct sense. The broader takeaway: this approach doesn't make a system perfectly secure — it makes it _relatively_ more secure by raising the cost (time, computing resources, money) required for an attacker to succeed.

**Q: What if the password is intercepted before it's even hashed (e.g., during transmission)?**

> A: Then "all bets are off" — hashing only protects data once it has been properly hashed and stored; it does nothing to protect a password captured in transit _before_ hashing occurs. This is precisely why the lecture goes on to cover **encryption in transit** as a separate, necessary protection.

**Q: Where exactly is the salt stored?**

> A: Directly within the stored hash value itself — conventionally as the first few characters (e.g., the first two characters in the simplified lecture example). The server reads this prefix to determine which salt to reapply when verifying a login attempt.

**Q: Is there any benefit to hashing a password multiple times (hashing the hash)?**

> A: Not generally necessary — a properly designed modern hash function should already provide sufficiently strong, well-vetted mathematical guarantees with a **single** application. Reiterating a broader theme: security-critical algorithms should be left to specialists/researchers rather than reinvented or "improved upon" ad hoc by individual developers.

**Q: If a company suffers a data/password breach due to poor practices, do they have any legal/ethical obligation to disclose it?**

> A: Ethically, most would say yes. Practically, obligations vary significantly by **industry** and **country/jurisdiction**-specific regulatory requirements. In practice, companies rarely publish detailed technical post-mortems of breaches — partly due to reputational/legal exposure, and partly to avoid handing attackers additional exploitable information about their systems' weaknesses. This tension — how much and how openly to disclose security failures — is itself an active ethical debate within the broader cybersecurity field.

**Q: If someone learns which specific hash function a company uses, could they exploit that knowledge to reverse the hashes?**

> A: Not realistically, if the company is using a modern, well-vetted algorithm — these are mathematically proven/extensively tested by the security research community. Best practice is actually to make the _algorithm_ used fully public and standard (never hide it) — real security should come from the secrecy of the **key** (or, for hashing, from the sheer size of the possible-output space), never from obscurity of the algorithm itself ("security through obscurity" is explicitly discouraged).

**Q: Instead of trying to crack a hash or cipher mathematically, wouldn't it be easier for an attacker to simply access the server/database directly and read out the algorithm/code being used?**

> A: This is certainly a possible practical attack vector, but it doesn't change the underlying recommendation: companies should still **not** rely on keeping their algorithm secret as a security measure. Trust should be placed in the sheer mathematical size of the keyspace/hash space (astronomically large numbers) rather than in obscurity — the assumption is that even with full knowledge of the algorithm, the search space is so vast that practical attacks remain infeasible within a meaningful timeframe (aside from especially weak, easily-guessed passwords).

**Q: How does a cipher work with words rather than numbers?**

> A: In practice, encryption keys generally aren't ordinary words — they are large, effectively random numbers generated automatically by the computer (unlike human-chosen passwords). That said, any word or text can always be converted into an underlying numeric representation using standard character-encoding systems like **ASCII** or **Unicode** (e.g., capital "A" = 65, capital "B" = 66 in ASCII) — and ultimately, everything reduces to patterns of bits (0s and 1s) that computers can process mathematically.

**Q: Could digital signatures be spoofed/forged?**

> A: Practically speaking, no — as long as a well-vetted, uncompromised standard algorithm is used and the signer's private key has not been stolen, forging a valid signature is considered so improbable as to be a non-concern under normal circumstances.

**Q: Are public/private keys tied to something like your IP address?**

> A: No — public keys are typically managed via a **trusted registry** (a centralized service that maintains verified associations between people/organizations and their public keys) or through more **distributed trust** models (e.g., publishing your public key in an email signature, personal website, or a trusted social/professional profile like LinkedIn). Trust ultimately depends on trusting whichever channel or authority is vouching for a given public key's ownership.

**Q: Is the relationship between hashing and encryption essentially: hashing = function only, encryption = function + key?**

> A: Yes, that's a fair simplification. Hashing is fundamentally **one-way** (irreversible by design) — useful for verifying data without needing to recover the original input. Encryption is fundamentally **two-way/reversible** — since its entire purpose is to allow the legitimate recipient to recover the original message using the appropriate key, unlike hashing, which deliberately and permanently discards the ability to recover the original input.

[⬆ Back to top](#-table-of-contents)

---

## 16. Summary of Key Takeaways

1. Companies should **never store plaintext passwords** — a database breach would otherwise instantly expose every user's actual password, fueling attacks like credential stuffing on other services.
2. **Hashing** converts passwords into fixed-length, seemingly random values via a one-way function; authentication works by re-hashing a login attempt and comparing it to the stored hash — the original password is never stored or needed again.
3. Hashing alone doesn't stop **dictionary attacks**, **brute-force attacks**, or **rainbow table attacks** against stolen hash databases — it merely raises the computational cost and time required.
4. **Salting** (adding random, non-secret data to each password before hashing) prevents identical passwords from producing identical hashes, defeating rainbow tables and hiding the fact that two users share a password.
5. Modern hash functions (e.g., SHA-2, SHA-3) use extremely large output spaces, making brute-forcing practically infeasible absent other underlying weaknesses (like a trivially weak password itself).
6. If a website ever **emails you your actual plaintext password**, this is a clear red flag indicating poor security practices — properly hashed passwords cannot be reversed or retrieved by the company itself.
7. **Cryptography** encompasses both historical **codes** (codebook-based) and modern **ciphers** (algorithmic, key-based); today's systems rely almost exclusively on ciphers.
8. **Symmetric (secret key) encryption** uses one shared key for both encryption and decryption (e.g., Caesar cipher historically; AES/Triple DES today) — but requires a pre-existing shared secret, creating a "chicken-and-egg" problem for first-time communication between strangers.
9. **Asymmetric (public key) encryption** solves this problem using mathematically related public/private key pairs (e.g., RSA) — anyone can encrypt a message using your public key, but only your private key can decrypt it.
10. **Diffie-Hellman key exchange** allows two parties to mathematically derive an identical shared secret over an insecure channel, without ever transmitting that secret directly.
11. **Digital signatures** flip the public/private key relationship: the signer uses their **private key** to sign a hashed document, and anyone can verify authenticity using the signer's **public key** — providing strong, hard-to-forge proof of authorship, unlike handwritten signatures.
12. **Passkeys (WebAuthn)** apply these same public/private key and digital signature principles to eliminate traditional passwords altogether, using device-based biometrics/PINs plus automatically generated, per-site key pairs.
13. **Encryption in transit** protects data as it travels across a network, but does **not** guarantee true end-to-end privacy — intermediary service providers may still technically be able to access your data unless **end-to-end encryption** (e.g., iMessage, WhatsApp) is specifically used.
14. **Deleting a file** typically only removes its reference from an internal lookup table — the actual underlying data usually remains recoverable until overwritten; true **secure deletion** requires deliberately overwriting the original bits.
15. **Full-disk encryption** (encryption at rest) protects data on a device from theft and effectively enables secure deletion when discarding/reselling hardware — but the same technology can be weaponized by attackers via **ransomware**.
16. **Quantum computing** represents a longer-term, forward-looking risk to today's cryptographic assumptions, since qubits could dramatically shorten the time needed to break current encryption/hashing standards — an active area of ongoing research and adaptation.
17. Across every topic in this lecture, the same core philosophy recurs: **no system is perfectly, permanently secure** — the practical goal is always to make attacks as **expensive, slow, and improbable** as possible, using well-vetted, industry-standard mathematics rather than homegrown or obscured techniques.

[⬆ Back to top](#-table-of-contents)

---

## 17. Quick-Reference Glossary

| Term                                   | Definition                                                                                                                      |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **Hashing**                            | Converting an input (e.g., a password) into a fixed-length, seemingly random output via a one-way mathematical function         |
| **Hash function**                      | The algorithm that performs hashing; takes arbitrary-length input, produces fixed-length output                                 |
| **Salt**                               | Random, non-secret data combined with a password before hashing, to prevent identical passwords from producing identical hashes |
| **Rainbow table**                      | A precomputed table mapping candidate passwords to their hash values, used to speed up cracking                                 |
| **One-way function**                   | A function that is computationally infeasible to reverse (recover input from output)                                            |
| **Collision**                          | When two different inputs produce the same hash output                                                                          |
| **Cryptography**                       | The practice and study of securing data, in transit and at rest                                                                 |
| **Code (cryptographic)**               | A mapping between code words and true meanings, via a shared codebook                                                           |
| **Cipher**                             | An algorithmic method of transforming plaintext to ciphertext, operating on letters/bits                                        |
| **Plaintext**                          | The original, readable message before encryption                                                                                |
| **Ciphertext**                         | The scrambled, unreadable output of encryption                                                                                  |
| **Encrypt / Encipher**                 | Convert plaintext into ciphertext                                                                                               |
| **Decrypt / Decipher**                 | Convert ciphertext back into plaintext                                                                                          |
| **Key**                                | A secret value used to configure/customize an encryption or decryption algorithm                                                |
| **Symmetric (secret key) encryption**  | Encryption where the same key is used to both encrypt and decrypt (e.g., AES, Triple DES, Caesar cipher)                        |
| **Caesar cipher / Rotational cipher**  | A simple substitution cipher that shifts each letter a fixed number of positions in the alphabet                                |
| **ROT13**                              | A Caesar cipher with a fixed key of 13, used historically for casual obfuscation (not security)                                 |
| **Asymmetric (public key) encryption** | Encryption using two mathematically related keys — a public key and a private key (e.g., RSA)                                   |
| **Public key**                         | A shareable key used to encrypt messages to someone, or to verify their digital signatures                                      |
| **Private key**                        | A confidential key used to decrypt messages sent to you, or to create digital signatures                                        |
| **RSA**                                | A widely used public key algorithm relying on the difficulty of factoring the product of two large prime numbers                |
| **Diffie-Hellman**                     | A public-key-based algorithm for securely deriving a shared secret key between two parties                                      |
| **Digital signature**                  | A cryptographic proof, created using a private key over a hash of a document, verifiable using the corresponding public key     |
| **Passkey / WebAuthn**                 | A passwordless authentication method using device-generated public/private key pairs and digital signatures                     |
| **Encryption in transit**              | Data encrypted only while actively traveling across a network                                                                   |
| **End-to-end encryption (E2EE)**       | Data encrypted such that only the sender and intended recipient can decrypt it — not even intermediary service providers        |
| **Encryption at rest**                 | Data encrypted while stored/sitting on a device (not actively being transmitted)                                                |
| **Full-disk encryption**               | A common implementation of encryption at rest, encrypting an entire storage device's contents                                   |
| **Secure deletion**                    | Overwriting a file's underlying bits (with 0s, 1s, or random data) so the original data cannot be recovered                     |
| **Ransomware**                         | Malware that encrypts a victim's data and demands payment for the decryption key                                                |
| **Qubit (quantum bit)**                | A quantum computing unit of information that can represent both 0 and 1 simultaneously                                          |
| **Quantum computing**                  | A computing paradigm leveraging quantum mechanics, with potential to break many current cryptographic assumptions               |

[⬆ Back to top](#-table-of-contents)

---

_End of Part 2 — This concludes CS50 Cybersecurity's coverage of securing accounts and securing data. Future topics in the course may build on these cryptographic foundations (e.g., network security, software vulnerabilities, and privacy)._
