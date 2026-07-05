# CS50: Introduction to Computer Science — Week 0 Lecture Notes

> **Source:** Harvard University CS50 — "Introduction to the Intellectual Enterprises of Computer Science and the Art of Programming"
> **Instructor:** David Malan
> **Topic:** Week 0 — AI & Programming, Binary, Data Representation, Algorithms, and Scratch
> **Purpose:** A comprehensive, standardized reference guide for review and exam preparation.

---

## Table of Contents

1. [Course Introduction & Philosophy](#1-course-introduction--philosophy)
2. [AI in Programming — A Live Demo](#2-ai-in-programming--a-live-demo)
3. [What Is Computer Science?](#3-what-is-computer-science)
4. [Representing Information: Unary, Binary & Bits](#4-representing-information-unary-binary--bits)
5. [Bits, Bytes & Counting in Binary](#5-bits-bytes--counting-in-binary)
6. [Representing Text: ASCII & Unicode](#6-representing-text-ascii--unicode)
7. [The Human "Byte" Demonstration](#7-the-human-byte-demonstration)
8. [Representing Colors: RGB](#8-representing-colors-rgb)
9. [Pixels, Images, Video & Audio](#9-pixels-images-video--audio)
10. [Algorithms & Problem Solving](#10-algorithms--problem-solving)
11. [Algorithmic Efficiency — Linear vs. Logarithmic](#11-algorithmic-efficiency--linear-vs-logarithmic)
12. [Pseudocode](#12-pseudocode)
13. [Core Programming Concepts: Functions, Conditionals, Loops](#13-core-programming-concepts-functions-conditionals-loops)
14. [From Binary to Code: Compilers & Abstraction](#14-from-binary-to-code-compilers--abstraction)
15. [Introduction to Scratch](#15-introduction-to-scratch)
16. [Scratch Walkthrough: "Hello World"](#16-scratch-walkthrough-hello-world)
17. [Scratch Walkthrough: User Input & the `join` Function](#17-scratch-walkthrough-user-input--the-join-function)
18. [Scratch Walkthrough: Loops & Custom Blocks](#18-scratch-walkthrough-loops--custom-blocks)
19. [Scratch Walkthrough: Conditionals & the `forever` Loop](#19-scratch-walkthrough-conditionals--the-forever-loop)
20. [Case Study: "Oscar Time" — A Complete Scratch Game](#20-case-study-oscar-time--a-complete-scratch-game)
21. [Case Study: "IV's Hardest Game" — Movement, AI Enemies & Collision](#21-case-study-ivs-hardest-game--movement-ai-enemies--collision)
22. [Glossary of Key Terms](#22-glossary-of-key-terms)
23. [Key Takeaways Summary](#23-key-takeaways-summary)

---

## 1. Course Introduction & Philosophy

CS50 is Harvard University's introduction to the intellectual enterprises of computer science and the art of programming.

### The Role of AI in Modern Programming

- AI is "seemingly everywhere" and has already changed — and will continue to change — programming.
- AI can help you: find bugs/mistakes in code, and increasingly, add entirely new features to software just by describing them in natural language.
- **Historical context:** For decades, **humans have been the bottleneck** in software development — limited hours in the day, limited people on a team, and an ever-growing backlog of bugs and features.
- **Crucial caveat:** Even with AI assistance, you still need to understand the fundamentals. CS50 has never been about merely teaching _how_ to program — that's a side effect. **The overarching goal is to teach you how to think:** how to take an input, produce correct output, and master tools (including AI) along the way.

### Learning to Drive, Not Just Ride

- By the end of the course, you'll be acquainted with: **Scratch, C, Python, SQL, HTML, CSS, and JavaScript.**
- The larger goal: you'll be able to **teach yourself new things** and tell computers what you want them to do — while remaining **"in the driver's seat"** (or "the pilot," "the conductor" — pick your metaphor).
- **Analogy to calculators:** Just as it's still valuable to know how to do basic addition/subtraction even though calculators exist, it's valuable to understand programming fundamentals even as AI can write code for you. The instructor draws a comparison to learning multiple methods for derivatives/integrals in math class — at some point, you "get the idea" and can lean on assistive tools rather than repeating rote procedures endlessly.
- **The balance:** Master the _ideas_, then lean on a "copilot"/assistant to help execute — but only because you understand what's actually happening underneath.

---

## 2. AI in Programming — A Live Demo

A live demonstration was given, building a simple chatbot in **Python** using **Visual Studio Code (VS Code)**.

### Tools Introduced

- **VS Code:** A popular, largely open-source/free code editor (similar to Notepad or Google Docs, but for code — no bold/underline formatting).
- **Terminal window:** A panel within VS Code where text commands are typed to instruct the computer.
- **API (Application Programming Interface):** A way to write code on top of functionality/features that another company (in this case, **OpenAI**) provides.

### Step-by-Step Build (Conceptual Walkthrough)

**Version 1 — Hardcoded chatbot:**

```python
from openai import OpenAI

client = OpenAI()
response = client.responses.create(
    input="In one sentence, what is CS50?",
    model="gpt-5"
)
print(response.output_text)
```

- Running `python chat.py` executes this program.
- This version can only ever answer **one hardcoded question**.

**Version 2 — Dynamic user input:**

```python
prompt = input("Question: ")
# ...then pass `prompt` as the input to client.responses.create()
```

- A **variable** (`prompt`, analogous to `x`, `y`, `z` in math) stores whatever the human types in — "just like in math," where the equals sign means "store this value."
- This makes the chatbot dynamic — it can now answer _any_ question the user types.

**Version 3 — Adding a "system prompt":**

- Observed that repeatedly having to type instructions like "in one sentence" or "in one word" into every question was tedious.
- Introduced the concept of a **system prompt**: standardized instructions given to the AI _once_, rather than the user needing to repeat them every time.

```python
system_prompt = "Limit your answer to one sentence."
user_prompt = input("Question: ")
# Both system_prompt and user_prompt are passed into the API call
```

- Demonstrated further by changing the system prompt to `"Pretend you're a cat"` — resulting in an answer ending in "meow," showing how system prompts can control **AI personality/behavior**.

### Key Takeaway from the Demo

> In roughly 10 lines of code (not all of which a beginner would yet understand), it's possible to build a fairly powerful, customizable chatbot — using the same underlying primitives (variables, input, function calls) that the rest of CS50 will teach.

### CS50's Own AI Tools

- CS50 provides its own AI-based tools accessible via **CS50.dev** and **CS50.ai**.
- Analogy: the **rubber duck** — a longstanding tradition in programming where a developer explains their bug out loud to an inanimate object (traditionally a rubber duck) since the act of verbalizing confusion often reveals the mistake, even without another person responding.
- CS50's AI ("duck") is designed to behave like a good human tutor — leading students toward a solution without simply handing over the answer outright.
- **Course policy:** Students are **not** permitted to use general AI tools (ChatGPT, Claude, Gemini, etc.) for coursework, but **are encouraged** to use CS50's own AI-based software, as well as human help (teaching staff, classmates).

---

## 3. What Is Computer Science?

- Computer science is fundamentally **the study of information** — how to represent it and how to process it.
- **Computational thinking** = the application of computer science ideas/methodologies to problems generally, even outside of traditional "computing" contexts.
- **Core insight:** Computer science is really about **problem solving**. Computers, programming languages, etc. are just tools and methodologies for solving problems.

### The Problem-Solving Model

A simple, foundational diagram used throughout the course:

```
   INPUT  →  [ BLACK BOX / Algorithm ]  →  OUTPUT
 (the problem)                          (the goal/solution)
```

- **Input:** The problem you want solved.
- **Black box:** The "secret sauce" — the step-by-step process (the algorithm) that transforms input into output.
- **Output:** The solution/goal.

To use computers to solve problems, humans first needed to agree on a **standardized way to represent information** (the inputs and outputs) — which brings us to binary.

---

## 4. Representing Information: Unary, Binary & Bits

### Unary (Base 1)

- Uses single digits — like counting on fingers, one finger = one unit.
- With **one human hand**, you can count to **5** in unary (or up to 10 with two hands) — quite limiting.

### Binary (Base 2)

- By instead considering the **pattern/position** of fingers being up or down (rather than just the total count), and assigning different "weights" to each finger position, you can count much higher.
- **Demonstration:** With 5 fingers on one hand, using binary positional weighting, you can count as high as **31** (32 total possible combinations, including 0).
- This is the same principle computers use: instead of "fingers up or down," computers use **electricity flowing or not flowing** through tiny switches called **transistors**.

### Why Binary Works So Well for Computers

- Electricity is simple: it's either **flowing or not** — no need to distinguish precise voltage levels (e.g., "5 volts vs. 0 volts" is much simpler to build reliable hardware around than trying to distinguish 10 different voltage levels for a base-10 system).
- **Light bulb analogy:** By convention, an **off light bulb = 0**, and an **on light bulb = 1**. This maps perfectly to real-world computer hardware, where transistors act as tiny switches that can be "on" (capturing electricity) or "off" (letting it dissipate).

### Key Terminology

| Term                                       | Definition                                                 |
| ------------------------------------------ | ---------------------------------------------------------- |
| **Bit** (binary digit)                     | A single 0 or 1 — the smallest unit of digital information |
| **Byte**                                   | A group of **8 bits**                                      |
| **Kilobyte, Megabyte, Gigabyte, Terabyte** | Progressively larger units built from bits/bytes           |

---

## 5. Bits, Bytes & Counting in Binary

### Understanding Positional Value (Decimal Review)

In the familiar **base 10 (decimal)** system, a number like `123` is understood via positional place values:

```
123 = (1 × 100) + (2 × 10) + (3 × 1) = 100 + 20 + 3 = 123
```

- The columns represent **10⁰, 10¹, 10²**, and so on (ones, tens, hundreds place) — because decimal uses **10 possible digits** (0–9) per column.

### Binary Positional Value

The same logic applies to **base 2 (binary)** — but each column can only be **0 or 1**, and column weights are powers of 2 instead of powers of 10:

```
Binary columns (right to left): 2⁰=1, 2¹=2, 2²=4, 2³=8, 2⁴=16, 2⁵=32, 2⁶=64, 2⁷=128 ...
```

**Worked Examples (3-bit system):**
| Binary | Calculation | Decimal |
|---|---|---|
| `000` | (4×0) + (2×0) + (1×0) | 0 |
| `100` | (4×1) + (2×0) + (1×0) | 4 |
| `111` | (4×1) + (2×1) + (1×1) | 7 |

**Counting sequence in 3-bit binary:** `000, 001, 010, 011, 100, 101, 110, 111` (decimal 0 through 7).

> To count higher than 7, you need a **4th bit** — this illustrates a real limitation: if a computer doesn't have "room" (enough allocated bits/memory) to store an additional digit, counting can **wrap around** unexpectedly back to 0, causing bugs (this concept is revisited later in the course as **integer overflow**).

### The Byte: 8 Bits Together

- **1 byte = 8 bits.**
- With 8 bits, you can represent decimal values from **0 to 255** (256 total distinct patterns, including 0).
- **Why 256 matters:** It equals **2⁸**. This number appears constantly in computing — e.g., older computer displays/certain image formats were limited to 256 colors because they used only 8 bits per pixel.

### Larger Bit-Widths

| Bit Width   | Approximate Range                                                                              |
| ----------- | ---------------------------------------------------------------------------------------------- |
| **8 bits**  | 0 to 255                                                                                       |
| **32 bits** | ~4 billion combinations (2³²) — or ~2 billion if reserving a bit to represent negative numbers |
| **64 bits** | An enormously larger number (2⁶⁴) — standard in modern computers, phones, etc.                 |

> **Connection to AI:** Modern AI's rapid advancement is partly attributed to the confluence of: computers becoming exponentially faster, having vastly more memory available (all measured in these binary/byte units), and vast quantities of training data existing on the internet.

---

## 6. Representing Text: ASCII & Unicode

### The Core Problem

If a computer only has zeros and ones, how does it represent a letter like "A"? **Answer: by assigning it a specific integer identity**, and agreeing globally on that mapping.

### ASCII (American Standard Code for Information Interchange)

- A standardized mapping created by a group of (primarily American) engineers, assigning each letter/symbol a specific number.
- **Key mappings:**
  | Character | ASCII Decimal Value |
  |---|---|
  | `A` | 65 |
  | `B` | 66 |
  | `C` | 67 |
  | ... | (sequential) |
  | `a` (lowercase) | 97 |

- **Fun fact/pattern:** Lowercase letters are always exactly **32 more** than their uppercase counterparts (e.g., 97 − 65 = 32; 98 − 66 = 32). This means a computer can convert text between uppercase and lowercase just by **flipping a single bit** (the one representing the value 32) in the byte's binary pattern.

**Worked Example — Decoding a Message:**
Given 3 bytes representing the decimal values **72, 73, 33**:

- 72 = `H`
- 73 = `I`
- 33 = `!` (exclamation point)
- **Decoded message: "HI!"**

### Context Matters

The _same_ pattern of bits/bytes can be interpreted completely differently depending on the software/context:

- In a text messaging app → interpreted as letters
- In a calculator app → interpreted as a number
- In Photoshop → interpreted as a color
- **The programmer is responsible for telling the computer how to interpret a given pattern of bits** (as a number, letter, color, etc.).

### Limitations of ASCII → Enter Unicode

- ASCII, using only 7–8 bits, can represent only **256 possible characters** — enough for English (uppercase, lowercase, digits, punctuation) but **not enough** for accented characters, many Asian language glyphs, or emoji.
- **Unicode** is a superset of ASCII, using far more bits per character (16, 24, or even 32 bits) — enabling representation of "all human language, past, present, and hopefully future," plus symbols like emoji.
  - With 32 bits, you could represent roughly **4 billion** distinct characters.

### Emoji as Unicode Characters

- Emoji are **not images** — they are **characters**, just like letters, but rendered with a colorful graphical font.
- **Example:** The number `4,036,991,106` (a large 32-bit binary pattern) corresponds to Unicode's "face with tears of joy" 😂 emoji.
- **Cross-platform rendering differences:** Unicode standardizes _that_ an emoji named "face with tears of joy" exists, but each company (Apple, Google, Microsoft, Telegram) is free to design/interpret its own **visual rendering** of that character — which is why the same emoji can look different across platforms (and why Telegram, for example, can even animate it).

---

## 7. The Human "Byte" Demonstration

A live classroom exercise: **8 student volunteers** stood in a row, each holding a sign representing a binary place value (from right to left): **1, 2, 4, 8, 16, 32, 64, 128.**

- **Raised hand = 1 (bit is "on")**
- **No raised hand = 0 (bit is "off")**

The class collectively used the ASCII cheat sheet to decode the pattern the volunteers formed, spelling out a three-letter word **one letter (one byte) per round**:

| Round | Decimal Value Formed | ASCII Character |
| ----- | -------------------- | --------------- |
| 1     | 66                   | B               |
| 2     | 79                   | O               |
| 3     | 87                   | W               |

**Result: The word "BOW"** was spelled out collectively — demonstrating, in physical/human form, exactly how a sequence of on/off bits (grouped into bytes) is decoded via a standardized mapping (ASCII) into readable text, just as a phone or computer would do internally.

---

## 8. Representing Colors: RGB

### The Same Fundamental Question

Just as with letters, computers must represent colors using only integers (patterns of bits).

### The RGB Model

- Most colors can be produced by mixing three "primary" light colors: **Red, Green, and Blue**.
- **Historical analogy:** Old classroom slide/film projectors used three separate lenses — one each for red, green, and blue light — which, when aligned/overlapped correctly on a screen, produced a full range of colors.
- Modern software (e.g., Photoshop) uses the exact same underlying model.

### Representing a Color Numerically

Each of the three RGB channels is typically represented using **1 byte (8 bits)**, meaning each channel can range from **0 to 255**:

```
(Red, Green, Blue) → each value 0–255
```

**Worked Examples:**
| Red | Green | Blue | Resulting Color |
|---|---|---|---|
| 72 | 23 | 33 | A dark shade of yellow |
| 0 | 0 | 0 | Black (absence of all color) |
| 255 | 255 | 255 | White (maximum of all three) |

> **Interesting overlap:** The specific numbers `72, 23, 33` used for the RGB demo were deliberately chosen because, when interpreted instead as ASCII, `72 = H`, `73 = I` — reinforcing the point that **the exact same bit pattern can mean completely different things** depending on whether the software interprets it as text or as color data.

### Hexadecimal Notation (Briefly Mentioned)

- Web pages and design tools often represent RGB values using hex codes (e.g., `00` or `FF`), which is simply **another number base** for expressing values 0–255 — to be explored further later in the course.

---

## 9. Pixels, Images, Video & Audio

### Pixels

- Zooming into any digital image (e.g., the "face with tears of joy" emoji) reveals it's composed of a grid of individual colored dots called **pixels**.
- Each pixel's color requires **3 bytes** to store (1 byte each for Red, Green, Blue) — i.e., **24 bits per pixel** under the RGB model.
- **Implication for file sizes:** An image with thousands (or millions) of pixels, each requiring 3 bytes, quickly adds up to **megabytes** of data — explaining why photo/video file sizes are often large.

### Video

- A video is conceptually just **a rapid sequence of still images** (frames) — much like an old-fashioned "flip book," or how the historical term "motion pictures" describes showing ~30 images per second to create the illusion of motion.
- This represents a **hierarchy of abstraction**: bits → letters/numbers/colors → images (pixels) → video (sequences of images).

### Audio/Music

- Musical notes can similarly be represented using a small set of numbers, such as:
  - **Frequency/pitch** of the note
  - **Duration** (how long the note is held)
  - **Amplitude/loudness** (how "hard" the note is struck)
- So long as both the sender's and receiver's software agree on how to interpret groups of numbers (e.g., "three numbers at a time representing pitch, duration, loudness"), an audio file can be shared and reproduced faithfully.

### Central Theme

> No matter the data type — text, numbers, colors, images, video, or audio — a computer ultimately only ever has **zeros and ones**. Everything else is a matter of **standardized agreement** on how to interpret specific patterns of those bits, and it is the **programmer's job** to tell the computer (explicitly or via the file format/software context) how a given pattern should be interpreted.

---

## 10. Algorithms & Problem Solving

- An **algorithm** is a **step-by-step set of instructions for solving a problem** — this is the "secret sauce" occupying the black box in the input→output model from Section 3.
- **Software = an implementation of an algorithm**, written in some programming language (C, Java, Python, etc.) that the computer can execute.
- **Key insight for the course:** Different programming languages don't just let you solve the _same_ problems in different ways — different languages are often genuinely **better suited** to solving different _kinds_ of problems, saving time and effort as a result.

### Case Study: Searching a Phone Book (Finding "John Harvard")

**Algorithm 1 — Linear Search (one page at a time):**

- Start at the beginning, check each page sequentially until the target is found.
- **Correct?** Yes.
- **Efficient?** No — potentially requires checking up to _every single page_ (worst case) before finding the target.

**Algorithm 2 — Linear Search, Two Pages at a Time:**

- Flip two pages at a time instead of one.
- **Correct?** **Not quite, as originally stated** — you might skip right past the target page (landing between two checked pages).
- **Fixable:** If you overshoot, simply double back one page to check. With this fix, it becomes correct — and roughly **twice as fast** as Algorithm 1 (plus one extra "double-back" step in the worst case).

**Algorithm 3 — Binary Search (divide and conquer):**

- Open to the _middle_ of the book/section. Determine whether the target is alphabetically before or after the current page.
- **Discard (don't search) the half of the book that cannot contain the target.**
- Repeat this process on the remaining half, again and again, until you narrow down to a single page.
- **Correct?** Yes.
- **Efficient?** Dramatically more efficient than either linear approach — this is known as **binary search**.

---

## 11. Algorithmic Efficiency — Linear vs. Logarithmic

### Visualizing Efficiency

Using a graph with:

- **X-axis:** Size of the problem (n) — e.g., number of pages in the phone book
- **Y-axis:** Time taken to solve the problem

| Algorithm                             | Growth Pattern                                                                             | Notation (informal)                                                     |
| ------------------------------------- | ------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------- |
| **Algorithm 1** (one page at a time)  | A straight, steep diagonal line                                                            | Proportional to **n**                                                   |
| **Algorithm 2** (two pages at a time) | A straight diagonal line, but with roughly **half the slope** of Algorithm 1               | Still proportional to **n** (just with a smaller constant factor — n/2) |
| **Algorithm 3** (binary search)       | A curve that rises **very slowly** and flattens out increasingly as the problem size grows | Proportional to **log₂(n)** — "logarithmic"                             |

### Why Binary Search Scales So Much Better

- **Thought experiment:** Suppose two towns (each with a 1,000-page phone book) merge, creating a combined 2,000-page phone book overnight.
  - **Algorithm 1 or 2:** Time to search roughly **doubles**, since these are linear — directly proportional to the number of pages.
  - **Algorithm 3 (binary search):** Time increases only **marginally** — you only need **one additional "halving" step** to handle double the data, because each step eliminates half of whatever remains (regardless of how large the starting pile is).
- **Core lesson:** Computer science (and good algorithm design) is fundamentally about solving problems not just _correctly_, but **efficiently** — minimizing time, CPU usage, memory (RAM), number of people involved, or cost, depending on the constraint that matters most.

---

## 12. Pseudocode

**Pseudocode** is an informal, human-readable (English-like) way of expressing step-by-step instructions for an algorithm — not tied to any specific programming language's syntax. Every person may write their own pseudocode slightly differently.

### Example: Pseudocode for Binary Search in a Phone Book

```
1. Pick up the phone book.
2. Open to the middle of the phone book.
3. Look at the page.
4. If the person is on the page, call them.
   Else if the person is earlier in the book,
       open to the middle of the left half of the remaining book.
       Go back to line 3.
   Else if the person is later in the book,
       open to the middle of the right half of the remaining book.
       Go back to line 3.
   Else,
       quit. [handles the "corner case" where the person is not in the book at all]
```

### Why "Go Back to Line 3" Doesn't Loop Forever

- Each repetition operates on a **progressively smaller** subset of the problem (half the size each time), so the process must eventually "bottom out" at a single page/result — it's not an infinite loop, because the underlying problem shrinks with each iteration.

### The Importance of Handling Edge Cases

- The pseudocode explicitly needed a final `else, quit` clause to handle the case where the target **isn't in the book at all**. Without handling this "corner case," undefined/unpredictable behavior can result.
- **Real-world connection:** When a computer or phone freezes, crashes, or spontaneously reboots, it's frequently because a programmer **failed to anticipate** an uncommon-but-possible situation like this — leaving the program's behavior in that scenario undefined, which manifests as a hang, crash, or reboot.

---

## 13. Core Programming Concepts: Functions, Conditionals, Loops

Analyzing the pseudocode above reveals the fundamental building blocks used across virtually all programming languages (Scratch, C, Python, and beyond):

| Concept                | Definition                                                                                                                            | Example from Pseudocode                 |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- |
| **Function**           | A verb/action — a small, self-contained piece of work that gets something done                                                        | "pick up," "open to," "look at," "call" |
| **Conditional**        | A "fork in the road" — a decision point where the program chooses between different paths                                             | "If... else if... else..."              |
| **Boolean Expression** | A yes/no (true/false) question that a conditional relies on to decide which path to take (named after mathematician **George Boole**) | "Is the person on this page?"           |
| **Loop**               | A "go back to" instruction that induces repeated/cyclical behavior                                                                    | "Go back to line 3"                     |

These four concepts — **functions, conditionals, Boolean expressions, and loops** — form the backbone of essentially all the code students will write throughout the course, regardless of the specific language used.

---

## 14. From Binary to Code: Compilers & Abstraction

### The Reality of Raw Machine Code

- At the absolute lowest level, computers only understand patterns of zeros and ones — not just for _data_ (numbers, letters, colors), but also for **instructions**.
- Companies like **Intel, AMD, and Nvidia** define standardized patterns of bits that correspond to low-level operations: "add two numbers," "load from hard drive into memory," "store to memory," "print to screen," etc.
- **Historical context:** Early programmers wrote code by directly representing these bit patterns — e.g., using **punch cards**, where physical holes represented bits.

### The Invention of Compilers

- Writing code directly in binary quickly became extremely tedious.
- Someone eventually invented the **compiler**: a program that **translates one language into another** — enabling humans to write in a more understandable, higher-level language, which the compiler then converts down into machine-readable binary.

### The Language Hierarchy Demonstrated

The course showed the exact same "Hello, World" concept expressed at increasing levels of abstraction:

1. **Raw binary** (a long string of 0s and 1s) — technically executes and prints "hello world," but is utterly unreadable to humans.
2. **The C language:**

   ```c
   #include <stdio.h>

   int main(void)
   {
       printf("hello, world\n");
   }
   ```

   More human-readable, but still has "cryptic" syntax (curly braces, `main`, `void`) that beginners find unfamiliar.

3. **The Python language:**
   ```python
   print("hello, world")
   ```
   Far simpler — a single, intuitive line.

### The Principle of Abstraction

> **Abstraction** in computing means building on top of solutions that others (or you, previously) have already solved, without needing to re-solve or even fully understand every low-level detail each time.

- Python code is ultimately converted (via a chain of translation, historically written _in_ C) down to machine-level binary.
- This is the same principle by which the earlier chatbot demo was possible: **OpenAI abstracted away** enormous underlying complexity (natural language processing, machine learning infrastructure), allowing a programmer to build a working chatbot in just ~10 lines of Python — "standing on the shoulders" of work already done by others.

---

## 15. Introduction to Scratch

### What Is Scratch?

- A **graphical, block-based programming language** developed roughly 20 years ago by **MIT's Media Lab**, designed to be beginner- and child-friendly.
- Programs are built by **dragging and dropping color-coded "puzzle piece" blocks** rather than typing text-based syntax.
- Despite its simplicity, Scratch represents the **exact same fundamental concepts** (functions, loops, conditionals, variables) that will later be used in C, Python, and other text-based languages — just visually.
- Accessed via a browser at **scratch.mit.edu**; CS50 uses its own hosted version via **CS50 Studio / CS50 Code Space**.

### The Scratch Interface

| Interface Area            | Purpose                                                                                                                                                |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Blocks Palette**        | The library of available "puzzle piece" blocks, organized by color-coded category (Events, Looks, Sound, Sensing, Control, Operators, Variables, etc.) |
| **Programming/Code Area** | Where you drag and assemble blocks together to build your program                                                                                      |
| **Sprites**               | The characters/objects in your program (default: a cat, but can be changed to anything — a dog, trash can, etc.)                                       |
| **Stage**                 | The visual "world" where the sprite lives and is animated                                                                                              |

### The Coordinate System (Cartesian Plane)

- The Scratch stage uses an X-Y coordinate plane, with **(0, 0)** at the center.
- Sprites can be moved along this plane using numeric X/Y values (e.g., moving up/down/left/right by a specified number of pixels).

---

## 16. Scratch Walkthrough: "Hello World"

### Building the Simplest Program

1. Drag the **"when green flag clicked"** block (from the **Events** category) into the code area — this defines the program's starting trigger. (The green flag = "go"; a red stop-sign icon = "stop.")
2. Drag a **"say [Hello!]"** block (from the **Looks** category) and snap it beneath the event block.
3. Edit the white oval text field to say, e.g., **"hello, world."**
4. Click the green flag to run the program — the sprite (a cat, by default) displays a speech bubble with the text.

### Mapping This to the Problem-Solving Model

| Concept                  | Scratch Element                                                                                                 |
| ------------------------ | --------------------------------------------------------------------------------------------------------------- |
| **Input**                | The white oval text (the string you typed, e.g., "hello, world") — also called an **argument** or **parameter** |
| **Algorithm**            | The underlying implementation of the "say" block (built by MIT's engineers — not something the student wrote)   |
| **Function**             | The "say" block itself — a piece of functionality that implements an algorithm                                  |
| **Output / Side Effect** | The visible speech bubble appearing on the screen                                                               |

**Key term — Side Effect:** A visible or audible result that occurs as a consequence of running a function (e.g., a speech bubble appearing, a sound playing) — distinct from a "return value" (see below).

---

## 17. Scratch Walkthrough: User Input & the `join` Function

### Prompting for User Input

1. From the **Sensing** category, use the **"ask [What's your name?] and wait"** block.
2. This block is also a function — but instead of producing an immediate visible side effect, it produces a **return value**: the user's typed response is automatically stored in a special built-in variable called **`answer`**.

**Key distinction:**
| Side Effect | Return Value |
|---|---|
| Something the **human** sees directly (e.g., a speech bubble) | Something only the **computer** can "see" and later use (stored in a variable) |

### The Timing Bug

- Naively placing an "ask and wait" block followed by a "say [Hello, ] [answer]" block on **separate lines** results in the two messages appearing sequentially — too fast for the human eye to perceive the first ("Hello,") before the second overwrites it, making it _look_ like a bug (only "answer" appears to show).
- **Attempted fix 1:** Insert a "wait 1 second" block between the two "say" blocks — technically works, but produces an awkward two-step visual sequence rather than one combined message.

### The `join` Function (from the Operators category)

- The **"join [apple] [banana]"** block takes **two inputs (arguments)** and concatenates ("joins") them together into a single combined string — itself returning a new value that can be used elsewhere (e.g., nested inside a "say" block).

**Final, elegant solution:**

```
say (join ["Hello, "] (answer))
```

- This nests the `join` function's output directly as the input to the `say` function — producing a single, correctly-formatted greeting (e.g., "Hello, David") in one seamless step, with no visible lag or awkward two-step reveal.
- **Small aesthetic fix:** Adding a trailing space after the comma in "Hello," ensures proper spacing in the final joined output ("Hello, David" instead of "Hello,David").

### The General Composition Pattern

This exercise illustrates a core programming principle: **composing solutions to smaller problems into solutions for bigger problems** — much like nested parentheses in math, where you resolve the innermost expression first, and its result feeds into the outer expression.

```
[ "What's your name?" ] → ask block → answer (stored value)
                                            ↓
[ "Hello, " ] + [ answer ] → join block → combined string
                                            ↓
                         combined string → say block → (side effect: speech bubble)
```

### Bonus: Text-to-Speech Extension

- Scratch supports optional **extensions**, such as **Text to Speech**, adding new blocks like **"speak [ ]"** and **"set voice to [ ]"** (e.g., kitten, giant), allowing the sprite to audibly speak the joined greeting in different voice styles.

---

## 18. Scratch Walkthrough: Loops & Custom Blocks

### The Problem with Repetition (Copy-Pasting Code)

- To make the cat "meow" three times with pauses in between, one (poor) approach is to **duplicate** the same two blocks ("play sound meow until done" + "wait") three times in a row.
- **Why this is bad, even though it's "correct":**
  - If you later want to change the wait duration, you must remember to update it in **every single copy** — easy to forget one, leading to inconsistent/buggy behavior.
  - This problem scales disastrously: imagine needing to make the same fix across dozens or hundreds of duplicated blocks instead of just three.

### Solution 1: The `repeat` Loop

- From the **Control** category, the **"repeat [3]"** block wraps around a sequence of blocks, executing them the specified number of times.

```
repeat (3):
    play sound "meow" until done
    wait 1 second
```

- This **centralizes** the logic — changing the wait duration or repeat count now only requires editing it in **one place**.

### Solution 2: Custom Blocks (Making Your Own Function)

- Scratch allows creating entirely new custom blocks via **"Make a Block"** — effectively defining your own reusable function.
- **Example:** Creating a block called **"meow"** that internally contains the "play sound" and "wait" logic.
- Once defined, the new **"meow"** block appears in the blocks palette and can be dragged/used anywhere, just like any built-in block — hiding ("abstracting away") the implementation details.

### Adding Parameters to Custom Blocks

- Custom blocks can accept their own **inputs/arguments**, just like built-in blocks.
- **Example:** Editing the "meow" block definition to add an input parameter named **`n`** ("meow [n] times"), then using a `repeat (n)` loop inside the block's definition.
- Once defined this way, using **"meow (3)"** in the main program automatically repeats the underlying logic 3 times — with **no visible loop block needed in the calling code** at all; the repetition logic is fully encapsulated inside the custom block.

> **Core lesson:** This exact process — defining reusable blocks/functions with parameters — mirrors exactly how MIT itself implemented built-in blocks like "repeat" or "wait": as a user, you don't need to know _how_ they work internally, only _that_ they work, and how to use them. This is the same principle that allows building a chatbot on top of OpenAI's API without needing to understand the underlying AI model implementation.

---

## 19. Scratch Walkthrough: Conditionals & the `forever` Loop

### Building a Simple Interactive Behavior ("Petting the Cat")

Using the **Control** category's **"forever"** block combined with an **"if [ ] then"** conditional and the **Sensing** category's **"touching [mouse-pointer]?"** Boolean block:

```
forever:
    if <touching mouse-pointer?> then
        play sound "meow" until done
```

- This continuously checks (every frame) whether the mouse cursor is touching the sprite — if so, it meows, simulating the effect of "petting" the cat.

### The Importance of `forever` for Continuous Checking

**Common mistake:** Omitting the `forever` wrapper and just using a single `if` check directly after "when green flag clicked":

```
when green flag clicked
if <touching mouse-pointer?> then
    play sound "meow" until done
```

- **Why this fails:** The program runs so fast that, at the _exact instant_ the green flag is clicked, the mouse likely isn't yet touching the sprite — the check happens once, finds "false," and the program simply ends. Moving the mouse onto the sprite _afterward_ has no effect, since the check was never repeated.
- **The fix:** Wrapping the `if` inside a `forever` loop ensures the condition is checked **continuously**, so that whenever the mouse _does_ touch the sprite (at any point while the program runs), it will be detected.

---

## 20. Case Study: "Oscar Time" — A Complete Scratch Game

A more involved example program (built by the instructor ~20 years ago) was used to demonstrate how a full game is built incrementally, from simple building blocks, across multiple versions.

**Premise of the game:** Drag falling pieces of trash into a trash can (Oscar) before a synchronized song ends, scoring points for each successful catch.

### Version 0 — Just Visuals, No Code

- Simplest starting point: just placing sprites on the stage and customizing their appearance ("costumes") — e.g., changing the default cat sprite into a trash can, and giving the stage background a lamppost image. **No functional code yet.**

### Version 1 — Basic Animation via Costume-Switching

- Sprites can have multiple **costumes** (different visual states) — e.g., a closed trash can, a trash can with its lid popping open, and "Oscar" popping out.
- Logic added for the trash can sprite:

```
switch costume to "Oscar 1" (closed)
forever:
    if touching mouse-pointer then
        switch costume to "Oscar 2" (open)
    else
        switch costume to "Oscar 1" (closed)
```

- Result: hovering the mouse over the trash can causes its lid to visually "pop open" — a simple animation achieved purely by rapidly switching between pre-made costume images (illustrating that animation is often just "an illusion" created via fast, sequential image swapping — akin to a flip book, as discussed in Section 9).

### Version 2 — Making the Trash Fall & Be Draggable

Two parallel scripts attached to the trash sprite (both starting "when green flag clicked," running **simultaneously**):

**Script A — Falling motion:**

```
set drag mode to draggable
go to random x position (between set bounds), y = 180 (top of screen)
forever:
    change y by -1   [moves the sprite downward, pixel by pixel]
```

> Scratch automatically prevents sprites from moving off-stage even inside a `forever` loop, preventing "loss of control."

**Script B — Collision detection with trash can:**

```
forever:
    if touching Oscar then
        [teleport back to a new random position at the top, simulating "a new piece of trash falling"]
```

### Version 3 — Refactoring with a Custom Block

- Noticed that the "go to a random X position, Y=180" logic was **duplicated** across both parallel scripts — a design smell (if you ever need to change this behavior, you'd have to remember to update it in multiple places).
- **Solution:** Created a custom block called **"go to top"**, encapsulating that shared logic once, then called `go to top` from both scripts instead of duplicating the raw instructions.
  > This exemplifies the DRY principle ("Don't Repeat Yourself") — a recurring theme throughout the lecture.

### Version 4 — Adding a Score

- Introduced a custom **variable** called `score`, initialized to 0 at the start.
- Each time the trash touches Oscar (right before teleporting away), the code increments: `change score by 1`.
- Scratch **automatically displays a scoreboard** overlay on the stage whenever a variable is created, updating live as the value changes.
- **Best practice noted:** Name variables meaningfully (`score`) rather than using vague placeholders like `x`, `y`, `z`.

### Final Version — Full Game

- Combines all of the above: falling trash (multiple sprites/pieces), score tracking, timed music synchronization (the instructor mentioned the song timing took significant trial and error to line up with specific falling objects), and win/lose feedback via a final displayed score.

---

## 21. Case Study: "IV's Hardest Game" — Movement, AI Enemies & Collision

A second, more advanced example (built by a former CS50 student) demonstrating keyboard-controlled movement, boundary collision, and simple AI-driven enemy behavior.

### Version 1 — Basic Player Movement with Wall Collision

The player's sprite (a Harvard crest) runs **two parallel scripts**:

**Script A — "Feel for walls":**

```
go to (0, 0)
forever:
    if touching left wall then
        change x by 1   [bounce back off the wall]
    if touching right wall then
        change x by -1
```

**Script B — "Listen for keyboard":**

```
forever:
    if key [up arrow] pressed then change y by 1
    if key [down arrow] pressed then change y by -1
    if key [right arrow] pressed then change x by 1
    if key [left arrow] pressed then change x by -1
```

> This demonstrates how arrow-key input is translated into simple coordinate arithmetic (incrementing/decrementing X or Y) to produce movement in a defined direction.

### Version 2 — An Autonomous Enemy (Patrolling Behavior)

A second sprite ("Yale") moves back and forth autonomously, without any player input:

```
go to (0, 0)
point in direction 90
forever:
    if touching left wall or touching right wall then
        turn 180 degrees   [reverses direction]
    move (1) steps
```

> **Discussion point — increasing difficulty:** Simply increasing the "move (1) steps" value to a larger number (e.g., 10) makes the enemy move dramatically faster, illustrating how a single parameter change can significantly alter gameplay difficulty.

### Version 3 — A "Homing" Enemy (Simple Pursuit AI)

A third sprite ("MIT") actively follows/chases the player:

```
go to a random position
forever:
    point toward [Harvard logo sprite]
    move (1) steps
```

- **Discussion point — a visual bug:** Increasing "move steps" too high (e.g., to 10) causes visibly "twitchy," erratic movement, because the enemy **overshoots** the player's position each frame, then must sharply correct direction — repeatedly overcorrecting back and forth. **Lesson:** Movement speed/step-size parameters must be tuned carefully (e.g., 2–5 steps) to avoid this visual glitch while still maintaining meaningfully engaging difficulty.

### The Complete Game

Combining all of the above elements (player movement + multiple patrolling/homing enemies + progressively increasing difficulty across levels) produces the finished multi-level obstacle-course game, played live by a student volunteer to conclude the lecture.

---

## 22. Glossary of Key Terms

| Term                                        | Definition                                                                                                        |
| ------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| **Algorithm**                               | Step-by-step instructions for solving a problem                                                                   |
| **API (Application Programming Interface)** | A way for one program to use functionality/services provided by another company's software                        |
| **ASCII**                                   | American Standard Code for Information Interchange — a standardized mapping of numbers to English letters/symbols |
| **Bit**                                     | A single binary digit (0 or 1)                                                                                    |
| **Boolean Expression**                      | A question with a true/false (yes/no) answer, used in conditionals                                                |
| **Byte**                                    | A group of 8 bits                                                                                                 |
| **Compiler**                                | A program that translates code from one language into another (e.g., a higher-level language into machine code)   |
| **Conditional**                             | A programming construct representing a decision point/"fork in the road"                                          |
| **Function**                                | A piece of code representing a verb/action that accomplishes a specific task, often implementing an algorithm     |
| **Loop**                                    | A programming construct that repeats a sequence of instructions                                                   |
| **Pseudocode**                              | Informal, human-readable representation of an algorithm's steps                                                   |
| **Return Value**                            | Data a function "hands back" to the program (visible to the computer/code, not necessarily to the human)          |
| **RGB**                                     | A color model representing colors as combinations of Red, Green, and Blue intensity values (each typically 0–255) |
| **Side Effect**                             | A visible or audible result that occurs directly from running a function (e.g., something appearing on screen)    |
| **Sprite**                                  | A character/object in a Scratch program                                                                           |
| **System Prompt**                           | Standing instructions given to an AI model that shape its behavior across all subsequent responses                |
| **Unicode**                                 | A superset of ASCII supporting a vastly larger character set, including non-English scripts and emoji             |
| **Variable**                                | A named storage location for a value (text, number, etc.), akin to `x`, `y`, `z` in math                          |

---

## 23. Key Takeaways Summary

1. **AI does not eliminate the need to understand fundamentals** — CS50's core goal is teaching _how to think_ and problem-solve, with AI as one of many tools (like a "copilot") along the way, not a replacement for foundational understanding.
2. **All computer data — text, numbers, colors, images, video, and audio — is ultimately represented as patterns of bits (0s and 1s).** The _meaning_ of any given pattern depends entirely on how the software/programmer chooses to interpret it.
3. **Binary (base 2)** uses positional place values (powers of 2) just as decimal (base 10) uses powers of 10 — just with only two possible digits per column instead of ten.
4. **A byte = 8 bits**, allowing 256 distinct values (0–255) — a foundational unit that recurs throughout computing (colors, character encodings, etc.).
5. **ASCII** maps numbers to English letters/symbols; **Unicode** extends this dramatically to support all world languages and symbols like emoji, using more bits per character.
6. **RGB** represents colors as three separate 0–255 intensity values for Red, Green, and Blue, mixed together.
7. **Algorithms are step-by-step problem-solving procedures** — and different algorithms solving the _same_ problem can have dramatically different efficiency (e.g., linear search vs. binary search).
8. **Binary search's "divide and conquer" approach** scales far better than linear approaches as problem size grows — a core lesson in algorithmic efficiency (linear vs. logarithmic growth).
9. **Pseudocode** lets you plan an algorithm's logic in plain language before writing actual code — and forces you to consider edge cases (like "what if the target isn't found at all?") that, if unhandled, cause real-world bugs, crashes, and freezes.
10. **Functions, conditionals (with Boolean expressions), loops, and variables** are the fundamental, universal building blocks of virtually all programming languages — from Scratch's visual blocks to C, Python, and beyond.
11. **Compilers translate higher-level, human-friendly code down into machine-executable binary**, enabling the principle of **abstraction**: building on top of others' completed, hidden-complexity work (just as a chatbot can be built atop OpenAI's API, or a Scratch program atop MIT's pre-built blocks).
12. **Good program design avoids repetition (the "DRY principle")** — using loops and custom functions/blocks to centralize logic, rather than copy-pasting the same instructions repeatedly, which is fragile and error-prone to maintain.
13. **Scratch**, despite being visual and beginner-friendly, teaches the exact same core concepts (functions, loops, conditionals, variables, custom blocks/functions) that underlie professional, text-based programming languages.

---

_End of Week 0 lecture notes. Subsequent weeks will introduce the C programming language, followed by Python, SQL, HTML/CSS/JavaScript, and more advanced computer science concepts._
