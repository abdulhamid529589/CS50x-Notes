# CS50 — Week 2: Compiling, Debugging, Arrays & Strings

### Lecture Notes — David J. Malan

---

## 📑 Table of Contents

1. [Introduction & Reading Levels Demo](#1-introduction--reading-levels-demo)
2. [Course Goals for the Week](#2-course-goals-for-the-week)
3. [Debugging Fundamentals](#3-debugging-fundamentals)
   - 3.1 [The Origin of "Bug" and "Debugging"](#31-the-origin-of-bug-and-debugging)
   - 3.2 [Live Debugging Walkthrough — `buggy.c`](#32-live-debugging-walkthrough--buggyc)
   - 3.3 [Syntax Errors vs. Logic Errors](#33-syntax-errors-vs-logic-errors)
4. [Debugging Techniques](#4-debugging-techniques)
   - 4.1 [Technique 1: `printf` as a Debugging Tool](#41-technique-1-printf-as-a-debugging-tool)
   - 4.2 [Technique 2: `debug50` (Stepping Through Code)](#42-technique-2-debug50-stepping-through-code)
   - 4.3 [Technique 3: Rubber Duck Debugging](#43-technique-3-rubber-duck-debugging)
   - 4.4 [Using AI Tools Responsibly](#44-using-ai-tools-responsibly)
5. [From Source Code to Machine Code](#5-from-source-code-to-machine-code)
   - 5.1 [Make vs. Clang](#51-make-vs-clang)
   - 5.2 [Command-Line Arguments to Compiler Tools](#52-command-line-arguments-to-compiler-tools)
   - 5.3 [The Four Steps of "Compiling"](#53-the-four-steps-of-compiling)
     - 5.3.1 [Preprocessing](#531-preprocessing)
     - 5.3.2 [Compiling (to Assembly)](#532-compiling-to-assembly)
     - 5.3.3 [Assembling](#533-assembling)
     - 5.3.4 [Linking](#534-linking)
   - 5.4 [Q&A: How Bits Represent Different Data Types](#54-qa-how-bits-represent-different-data-types)
   - 5.5 [Q&A: Cross-Platform Compilation](#55-qa-cross-platform-compilation)
   - 5.6 [Decompiling / Reverse Engineering](#56-decompiling--reverse-engineering)
6. [Data Types and Memory](#6-data-types-and-memory)
   - 6.1 [Size of Each Data Type](#61-size-of-each-data-type)
   - 6.2 [Visualizing RAM as a Grid](#62-visualizing-ram-as-a-grid)
7. [From Variables to Arrays](#7-from-variables-to-arrays)
   - 7.1 [Problem: Repetitive Variables (`scores.c`)](#71-problem-repetitive-variables-scoresc)
   - 7.2 [Integer Division / Floating Point Fix](#72-integer-division--floating-point-fix)
   - 7.3 [Introducing Arrays](#73-introducing-arrays)
   - 7.4 [Removing "Magic Numbers" with Constants](#74-removing-magic-numbers-with-constants)
   - 7.5 [Writing a Custom `average` Function with Arrays](#75-writing-a-custom-average-function-with-arrays)
8. [Characters, Strings, and the Null Terminator](#8-characters-strings-and-the-null-terminator)
   - 8.1 [Chars Are Just Numbers](#81-chars-are-just-numbers)
   - 8.2 [Strings Are Arrays of Characters](#82-strings-are-arrays-of-characters)
   - 8.3 [The Null Character (`\0`)](#83-the-null-character-0)
   - 8.4 [Arrays of Strings (2D Indexing)](#84-arrays-of-strings-2d-indexing)
9. [String Library Functions](#9-string-library-functions)
   - 9.1 [Manually Computing String Length](#91-manually-computing-string-length)
   - 9.2 [`strlen` from `string.h`](#92-strlen-from-stringh)
   - 9.3 [Loop Efficiency: Avoid Recomputing `strlen`](#93-loop-efficiency-avoid-recomputing-strlen)
10. [Case Conversion: Good, Better, Best](#10-case-conversion-good-better-best)
    - 10.1 [Manual ASCII Math Approach](#101-manual-ascii-math-approach)
    - 10.2 [Using `ctype.h`: `islower()`](#102-using-ctypeh-islower)
    - 10.3 [Using `ctype.h`: `toupper()`](#103-using-ctypeh-toupper)
11. [Revisiting `main`: Command-Line Arguments](#11-revisiting-main-command-line-arguments)
    - 11.1 [`int main(void)` vs. `int main(int argc, string argv[])`](#111-int-mainvoid-vs-int-mainint-argc-string-argv)
    - 11.2 [Using `argv[0]` — The Program's Own Name](#112-using-argv0--the-programs-own-name)
    - 11.3 [Validating Argument Count with `argc`](#113-validating-argument-count-with-argc)
12. [Exit Status / Return Codes](#12-exit-status--return-codes)
    - 12.1 [Checking Exit Status with `echo $?`](#121-checking-exit-status-with-echo-)
    - 12.2 [Real-World Example: `cowsay`](#122-real-world-example-cowsay)
13. [Introduction to Cryptography](#13-introduction-to-cryptography)
    - 13.1 [Plaintext, Ciphertext, and Keys](#131-plaintext-ciphertext-and-keys)
    - 13.2 [The Caesar Cipher](#132-the-caesar-cipher)
    - 13.3 [ROT13 and Its Weaknesses](#133-rot13-and-its-weaknesses)
    - 13.4 [Brute-Force Vulnerability](#134-brute-force-vulnerability)
14. [Key Terms Glossary](#14-key-terms-glossary)

---

## 1. Introduction & Reading Levels Demo

The lecture opens with three student volunteers reading passages of increasing complexity:

| Volunteer | Text Read                                                                          | Estimated Reading Level                                           |
| --------- | ---------------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| Leah      | _"1 fish, 2 fish, red fish, blue fish"_ (Dr. Seuss)                                | Kindergarten (before grade 1) — short phrases, minimal complexity |
| Maria     | _"Congratulations. Today is your day..."_ (Dr. Seuss, _Oh, the Places You'll Go!_) | Grade 3 — introduces rhyme and more substance                     |
| Omar      | Opening paragraph of George Orwell's _1984_                                        | Grade 10 — complex sentence structure and vocabulary              |

**Purpose of the demo:** To motivate the week's central real-world problem — **algorithmically determining a text's reading level** using a mathematical formula (explored further in that week's problem set). To do this computationally, text needs to be broken down more granularly than just printing whole strings — motivating the week's deep dive into **how strings actually work under the hood**.

---

## 2. Course Goals for the Week

The lecture identifies two major goals for the week:

1. **Reading levels** — a representative real-world text-processing problem requiring fine-grained manipulation of strings (not just printing them wholesale, as done in Week 1).
2. **Cryptography** — the art of scrambling (encrypting) information for secure communication, motivated by everyday assumptions about the security of texts, emails, and online credit card transactions.

A secondary, cross-cutting goal: **learning to debug code independently** — using tools beyond just asking a TA or AI, since bugs are an unavoidable and constant part of programming.

---

## 3. Debugging Fundamentals

### 3.1 The Origin of "Bug" and "Debugging"

- The term is often credited to **Dr. Grace Hopper**, a rear admiral in the U.S. Navy and one of the original programmers of the **Harvard Mark I** (an early mainframe computer; a portion is on display at Harvard's Science and Engineering Complex).
- On its successor, the **Harvard Mark II**, Hopper's team famously found an actual **moth** stuck in the circuitry causing a malfunction, and logged it as "first actual case of bug being found" — popularizing the terms **bug** and **debugging**.

### 3.2 Live Debugging Walkthrough — `buggy.c`

The lecture builds a file called `buggy.c` and deliberately introduces, then fixes, common beginner mistakes:

**Version 1 — missing header, missing semicolon, missing newline escape:**

```c
int main(void)
{
    printf("hello, world")
}
```

**Issues identified and fixed, one at a time:**

1. Missing `#include <stdio.h>` → compiler doesn't recognize `printf`.
2. Missing semicolon at end of the `printf` line.
3. Missing `\n` at the end of the string (a stylistic/aesthetic issue, not a hard error) — needed so the next terminal prompt appears on a fresh line.

**Common typo warning:** The header is `stdio.h` (**standard input/output**), not `studio.h` — described as a recurring, frequently-asked-about mistake.

**Version 2 — using `get_string` without proper type declaration:**

```c
name = get_string("What's your name? ");
```

- Error: `use of undeclared identifier 'name'` → in C (unlike Python), variables must have their **type explicitly declared** before use.
- Fix: declare the type as `string` (not `int`, since it holds text):

```c
string name = get_string("What's your name? ");
```

**Version 3 — using `string` without the CS50 library:**

- Error: `use of undeclared identifier 'string'. Did you mean 'stdin'?` — a **red herring** from the compiler's autocorrect-style suggestion.
- **Root cause:** `string` is **not a native C keyword** — it's a type defined specifically by the **CS50 library** (`cs50.h`) as a teaching convenience ("training wheels"). The lecture notes that in a few weeks, students will learn what a string _actually_ is under the hood (see [Section 8](#8-characters-strings-and-the-null-terminator)) and eventually stop relying on this CS50-specific type name.
- Fix: add `#include <cs50.h>` (conventionally placed **alphabetically** among header includes, as a stylistic — not required — practice).

**Version 4 — logic bug, not syntax bug:**

```c
printf("hello, world\n");  // forgot to use the %s placeholder and the `name` variable
```

- This compiles successfully (no syntax error) but doesn't produce the intended output ("hello, world" instead of "hello, David").
- Fix: use the string format placeholder and pass the variable:

```c
printf("hello, %s\n", name);
```

### 3.3 Syntax Errors vs. Logic Errors

| Type             | Description                                                                        | Detected By                                                                                                                           |
| ---------------- | ---------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| **Syntax error** | Code violates the grammar/rules of the language; **prevents compilation entirely** | The compiler, via error messages                                                                                                      |
| **Logic error**  | Code compiles and runs, but doesn't do what the programmer _intended_              | Only by human judgment (or automated correctness checks like `check50`) — the compiler has no concept of the programmer's actual goal |

---

## 4. Debugging Techniques

### 4.1 Technique 1: `printf` as a Debugging Tool

`printf` can be used **temporarily** to inspect the internal state of a program (e.g., variable values) while diagnosing a bug — then removed once the bug is fixed.

**Example bug — a loop meant to print 3 "bricks" instead prints 4:**

```c
for (int i = 0; i <= 3; i++)
{
    printf("#\n");
}
```

**Diagnostic technique:** Temporarily print the loop variable's value alongside the output:

```c
printf("i is %i\n", i);
```

or more concisely, inline:

```c
printf("# %i\n", i);
```

**Diagnosis:** The output reveals the loop runs for `i = 0, 1, 2, 3` — **four** iterations, not three, because the condition `i <= 3` includes `3`.

**Fix:** Change `i <= 3` to `i < 3` (or start at `i = 1` and use `i <= 3`, though `i = 0` combined with `<` is the conventional idiom in C).

### 4.2 Technique 2: `debug50` (Stepping Through Code)

**`debug50`** is a CS50-provided command that launches an **industry-standard debugger** inside VS Code (simplified for convenience, but functionally identical to real-world debugging tools).

**Setup and usage:**

1. Run `debug50 ./buggy` — if no breakpoints are set, the tool prompts you to set one first.
2. **Breakpoint:** Click to the left of a line number to set a red marker indicating "pause execution here."
3. Re-run `debug50 ./buggy` — execution pauses at the breakpoint (e.g., at the first line inside `main`).

**Debugger controls:**

| Control                      | Function                                                                                          |
| ---------------------------- | ------------------------------------------------------------------------------------------------- |
| **Play button**              | Resume/continue execution until the next breakpoint or program end                                |
| **Step Over** (curved arrow) | Execute the current line, then pause on the next line (does not enter called functions)           |
| **Step Into**                | If the current line calls another function, jump inside that function and pause on its first line |

**Observability while paused:**

- The **Variables** panel shows current values of all variables in scope.
- Before a variable is initialized, it may show a **garbage value** — a leftover, effectively random bit pattern from the memory's prior use (explained further as a real-world security concern to be revisited later in the course).
- The **Call Stack** panel shows which function is currently executing (e.g., `main` in `buggy.c`).

**Live example — stepping into `print_column`:**

- Setting a breakpoint at `main`, stepping over the input-prompting line, then **stepping into** the `print_column` function reveals, line by line, that the loop variable `i` reaches `3` and still satisfies `i <= 3` — producing the unwanted fourth "brick." This confirms the same root cause found earlier via `printf`, but observed interactively.

**Q&A — Breakpoint flexibility:** A breakpoint doesn't have to be set at `main`; it can be placed directly inside a specific function of interest (e.g., `print_column`) so that `main` runs automatically up to that point without manual stepping.

**Q&A — For-loop execution order (reinforced via the debugger):**

1. Initialization (runs once, at the very start).
2. Condition (Boolean expression) is checked.
3. If true, the loop body executes.
4. The update/increment step runs.
5. Return to step 2, and repeat.

_(Note: initialization, condition, and update must appear in this fixed order in a `for` loop's header, though C does support multiple initializations/updates/conditions in more advanced usage — not covered in depth here.)_

### 4.3 Technique 3: Rubber Duck Debugging

- **Rubber duck debugging**, popularized in a well-known programming book, involves explaining a bug or confusing concept out loud to an inanimate object (e.g., a literal rubber duck).
- **Why it works (anecdotally):** Articulating the problem in your own words often reveals the flawed logic or misunderstanding, without needing another person (or AI) to actually answer anything.
- CS50 provides physical rubber ducks to students, plus a virtual/AI-powered version accessible at **CS50.ai** and embedded in VS Code at **CS50.dev**.

### 4.4 Using AI Tools Responsibly

**Guidance offered in the lecture:**

- Appropriate use: asking the AI duck to **explain what an error message means** if it's confusing.
- Inappropriate use: **pasting your entire code and asking "what's wrong with my code?"** without any of your own effort.
- **Rationale:** The purpose of working through problems (in this or any course) is to build genuine mental models and problem-solving "muscle memory." Over-reliance on AI defeats that purpose — the guidance is to "meet the AI halfway," trying `printf`, `debug50`, or a literal rubber duck _before_ escalating to AI or human help.

---

## 5. From Source Code to Machine Code

### 5.1 Make vs. Clang

- **Clarification of a prior simplification:** In Week 1, students were told (as a simplification) that `make` is "the compiler." In fact, **`make` is a build-automation tool**, and the actual compiler being invoked is **Clang** (for "C Language") — a popular, open-source compiler.
- Running `clang hello.c` directly (without `make`) works, but by default produces an executable named **`a.out`** ("assembler output") rather than a custom name — a historical convention.
- To specify an output name manually: `clang -o hello hello.c` (the `-o` flag, followed by the desired output name, is itself a command-line argument to Clang).

**Linking the CS50 library manually:**

- Running `clang hello.c` alone (when the code uses `get_string`) produces a **linker error**:
  ```
  Linker command failed with exit code 1... undefined reference to `get_string`
  ```
- Fix: explicitly tell the compiler to link the CS50 library:
  ```
  clang -o hello hello.c -lcs50
  ```
- **`make` automates all of this** — including invoking Clang with the correct output name and linking the CS50 library — which is why `make hello` alone is sufficient in this course's environment (CS50.dev is pre-configured to handle this).

### 5.2 Command-Line Arguments to Compiler Tools

The lecture uses this discussion to introduce the general concept of **command-line arguments**: any words typed after a program's name at the terminal prompt.

**Examples already familiar from prior use:**

- `code hello.c` → `hello.c` is a command-line argument to `code`.
- `make hello` → `hello` is a command-line argument to `make`.
- `clang -o hello hello.c -lcs50` → three arguments: `-o hello` (output name), `hello.c` (source file), `-lcs50` (library to link).

### 5.3 The Four Steps of "Compiling"

While casually referred to as one step ("compiling"), the process technically consists of **four distinct sub-steps**, each automated in succession by `make`/`clang`:

#### 5.3.1 Preprocessing

- The compiler scans for **preprocessor directives** (lines starting with `#`, such as `#include <stdio.h>`).
- For each `#include`, the compiler locates the named header file on disk and **copies its contents** (function prototypes, etc.) directly into your source code — sparing you from manually typing out prototypes (e.g., for `printf`, `get_string`) yourself.

#### 5.3.2 Compiling (to Assembly)

- Converts the **preprocessed C source code** into **assembly code** — a lower-level, human-readable-but-cryptic instruction language (historically used before higher-level languages like C, Python, etc. existed).
- Assembly includes recognizable references to functions (`main`, `get_string`, `printf`) alongside low-level operations like `move`, `push`, `xor`, `call`.
- These are the lowest-level instructions understood directly by a computer's **CPU** (Central Processing Unit — made by companies like Intel, AMD, or Apple).

#### 5.3.3 Assembling

- Converts the **assembly code** into actual **machine code** — the raw zeros and ones — specific to your program's file (e.g., `hello.c`'s corresponding machine code).
- This is why the historical default output filename is `a.out` — "**a**ssembler **out**put."

#### 5.3.4 Linking

- Your program's compiled machine code must be **combined ("linked")** with the already-compiled machine code of any external libraries it uses (e.g., the CS50 library's implementation of `get_string`, or the standard library's implementation of `printf`).
- These library implementations exist as **pre-compiled binary code** on the system already; linking merges all the relevant zeros-and-ones blocks into one final executable.
- **This is exactly what the earlier `-lcs50` flag and the "linker error" refer to** — without it, the linker has no instructions to locate and merge in the CS50 library's compiled code.

**Summary — the four steps, colloquially just called "compiling":**

1. Preprocessing (`#include` expansion)
2. Compiling (C → assembly)
3. Assembling (assembly → machine code for your file)
4. Linking (merging your machine code with library machine code)

### 5.4 Q&A: How Bits Represent Different Data Types

**Q: How does the computer know which bits represent text vs. numbers vs. instructions?**

- Answer deferred to a later week (Week 4) for full detail, but the short answer: the raw zeros and ones follow **conventions/patterns** — certain bit positions/groupings systematically represent certain kinds of data or instructions. It is not an arbitrary, unstructured "hot mess."

### 5.5 Q&A: Cross-Platform Compilation

**Q: Why can't you just run the same compiled program on a different device (e.g., old Mac program on a PC)?**

- The **compiling** step (C → assembly) produces assembly code **specific to the target CPU architecture**. The same C source code compiled for an iPhone, an Android device, a Mac, or a PC will produce **different assembly instructions** for each, because each uses different underlying CPU designs.
- **Benefit of this layered design:** Different people/teams can implement different pieces (preprocessor, compiler, assembler, linker) independently, and multiple compiler backends can target different hardware (e.g., iPhones vs. Android) while everyone continues writing the _same_ higher-level C code.

### 5.6 Decompiling / Reverse Engineering

**Q: Could you reverse the compiling process to "decompile" someone else's software (e.g., reconstruct Microsoft Word's source code from its distributed binary)?**

- **In principle, yes — and this is a genuine concern in intellectual property law** — since the zeros and ones must be present on your device to run, in theory they could be studied and reverse-engineered.
- **In practice, this is extremely difficult and often impractical:**
  - You'd need to know the target CPU architecture and painstakingly reconstruct meaning from raw bit patterns (grouped in units like 8, 16, 32, or 64 bits).
  - **The reasoning given:** anyone with enough skill and time to fully reverse-engineer a large, complex program (like Microsoft Word) would likely find it **faster to simply reimplement the software from scratch** than to reverse it.
  - **Reverse engineering also can't recover certain implementation choices** — e.g., machine code resulting from a `for` loop vs. a stylistically equivalent `while` loop is often indistinguishable, since both compile to equivalent underlying instructions. Some source-level design decisions are simply lost in the compiled output.
- **Contrast with JavaScript:** Unlike compiled languages such as C, **JavaScript is typically not compiled** — its source code is sent as-is from web servers to browsers and can be directly viewed on nearly any website (a teaser for a later unit on web programming).

---

## 6. Data Types and Memory

### 6.1 Size of Each Data Type

| Type     | Size                             | Notes                                                                                                                                                                              |
| -------- | -------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `bool`   | 1 byte (8 bits)                  | Only needs 1 bit conceptually (true/false), but a full byte is used for efficiency/simplicity — 7 bits go unused                                                                   |
| `int`    | 4 bytes                          | Roughly ±2 billion range (or ~4 billion unsigned)                                                                                                                                  |
| `long`   | 8 bytes                          | Roughly ±9 quadrillion range — double the bytes of `int`                                                                                                                           |
| `float`  | 4 bytes                          | Real/decimal numbers; limited precision                                                                                                                                            |
| `double` | 8 bytes                          | Twice the bits of `float`, allowing more precision — though **floating-point imprecision remains a fundamental limitation** regardless, relevant to scientific/financial computing |
| `char`   | 1 byte                           | A single ASCII character                                                                                                                                                           |
| `string` | _(Variable — depends on length)_ | E.g., "hi" ≈ 2 bytes (plus a terminator — see [Section 8.3](#83-the-null-character-0)); "hello" ≈ 5 bytes (plus terminator)                                                        |

### 6.2 Visualizing RAM as a Grid

- Physical memory (RAM — Random Access Memory) is modeled as a large, addressable grid of bytes — conceptually similar to a "canvas" that can be filled with the zeros and ones representing any program's variables.
- Multi-byte values (e.g., a 4-byte `int`) occupy **contiguous** bytes back-to-back — not scattered arbitrarily around memory.
- This mental model sets up the transition to arrays: a natural extension of "several contiguous bytes for one value" is "several contiguous _chunks_ of bytes for _multiple_ values."

---

## 7. From Variables to Arrays

### 7.1 Problem: Repetitive Variables (`scores.c`)

**Initial (poorly designed) approach — one variable per score:**

```c
int score1 = 72;
int score2 = 73;
int score3 = 33;
printf("Average: %i\n", (score1 + score2 + score3) / 3);
```

**Design critique raised in lecture:** This doesn't scale — adding a 4th, 5th, or 30th score would require manually creating that many individual variables, which is clearly poor design as the problem grows (a useful heuristic: _"if you had 300 of these, would this approach still make sense?"_).

### 7.2 Integer Division / Floating Point Fix

**Bug demonstrated:** Dividing `int` by `int` truncates any decimal remainder (reviewed from Week 1).

```c
printf("%i\n", (score1 + score2 + score3) / 3);  // truncates to 59 instead of 59.33
```

**Fixes explored:**

1. Change all variables to `float` (and use `%f` in `printf`) — note `%f` also works for `double`, which can cause a `printf` format-mismatch warning if types aren't matched carefully.
2. Change just the **denominator** to a float literal: `/ 3.0` — since involving even one floating-point value in an arithmetic expression **promotes the whole calculation** to floating-point.
3. Use an explicit **cast**: `(float) 3` — converts an existing integer/variable to `float` at that point in the expression, without needing to redeclare its original type.

### 7.3 Introducing Arrays

**Definition:** An array is a **contiguous block ("chunk") of memory** sized to hold a fixed number of same-typed values.

**Syntax:**

```c
int scores[3];        // declare an array of 3 ints
scores[0] = 72;
scores[1] = 73;
scores[2] = 33;
```

- Indexing starts at **0** (a recurring convention in computing).
- Arrays can also be **statically initialized** in one line, letting the compiler infer the size automatically:

```c
int scores[] = {72, 73, 33};
```

**Using arrays with a loop and `get_int` for dynamic input:**

```c
for (int i = 0; i < 3; i++)
{
    scores[i] = get_int("Score: ");
}
```

### 7.4 Removing "Magic Numbers" with Constants

**Problem identified:** The literal number `3` appears in **two separate places** (array size and loop bound) — a "**magic number**" that's easy to get out of sync if changed in only one location.

**Fix — introduce a named constant:**

```c
const int N = 3;
int scores[N];
for (int i = 0; i < N; i++) { ... }
```

**Style convention:** Constants are conventionally written in **all capital letters** (e.g., `N`) to visually flag to other programmers/collaborators that the value should never change.

### 7.5 Writing a Custom `average` Function with Arrays

**Design goal:** Move the averaging logic into its own reusable function.

```c
float average(int numbers[], int length)
{
    float sum = 0;
    for (int i = 0; i < length; i++)
    {
        sum += numbers[i];
    }
    return sum / length;
}
```

**Key lessons highlighted:**

- **C arrays don't carry their own length** (unlike arrays/lists in Python or Java) — so any function that takes an array as a parameter must **also separately receive the array's length** as a second argument.
- **Passing an array to a function:** specify the element type, a name, and **empty square brackets** (no size needed) in the function's parameter list: `int numbers[]`.
- **Common bug reintroduced:** returning `sum / length` where both are still `int` types causes the same **integer truncation** problem seen earlier — fixed by making `sum` a `float` (or casting `length` to `float`).
- **Function prototypes:** Since `main` is conventionally placed at the top of the file (a style preference), any custom function defined _below_ `main` (like `average`) needs its **prototype** copied above `main` so the compiler knows about it in advance — the _one_ case in the lecture where copy-pasting code is explicitly endorsed as reasonable.

---

## 8. Characters, Strings, and the Null Terminator

### 8.1 Chars Are Just Numbers

**Demonstration — declaring three `char` variables:**

```c
char c1 = 'H';
char c2 = 'I';
char c3 = '!';
printf("%c%c%c\n", c1, c2, c3);   // prints: HI!
```

**Key insight:** Using `%i` instead of `%c` in the same `printf` call reveals the **underlying ASCII numeric values**:

```c
printf("%i %i %i\n", c1, c2, c3);   // prints: 72 73 33
```

- **Chars are single-quoted**, unlike strings, which are double-quoted — foreshadowing the deeper distinction explored next.
- `printf` is "smart enough" to implicitly treat a `char` as its integer ASCII value when using an integer format specifier — no explicit cast is required.

### 8.2 Strings Are Arrays of Characters

**Key revelation of the lecture:** A `string` (as provided by the CS50 library) is, underneath, simply an **array of `char` values**.

**Demonstration:**

```c
string s = "HI!";
printf("%c%c%c\n", s[0], s[1], s[2]);   // prints: HI!
printf("%i %i %i\n", s[0], s[1], s[2]); // prints: 72 73 33
```

This confirms that indexing into a string with square brackets works exactly like indexing into any other array — because it _is_ one.

### 8.3 The Null Character (`\0`)

**Investigative demonstration:** Printing "one location past the end" of a 3-character string (`s[3]`, even though only indices 0–2 were explicitly set) unexpectedly reveals the value **`0`** — not by coincidence, but **by design**.

**Explanation:**

- Every string literal in double quotes is **automatically terminated** by the compiler with an extra byte: the **null character**, written `\0` (analogous to the `\n` newline escape sequence), representing the byte value `00000000`.
- So a string like `"HI!"`, which appears to be 3 characters, actually occupies **4 bytes** in memory: `H`, `I`, `!`, `\0`.
- **Why this matters functionally:** Functions like `printf` (and `strlen`, covered next) don't inherently "know" where a string ends — they work by **looping character by character until they encounter the null terminator**. Without it, such functions would have no way to know where to stop reading memory.
- **Corner case tested in lecture:** An empty string (`""`, e.g., from pressing Enter with no input to `get_string`) still occupies **1 byte** — just the null terminator itself, with a length of 0.

**Two-string memory layout example:**

```c
string s = "HI!";   // occupies 4 bytes: H, I, !, \0
string t = "bye";   // occupies 4 bytes: b, y, e, \0
```

Each string independently ends with its own null terminator, allowing functions to correctly determine where each string stops, even when multiple strings coexist in memory.

### 8.4 Arrays of Strings (2D Indexing)

**Demonstration — an array containing two strings:**

```c
string words[2];
words[0] = "hi";
words[1] = "bye";
```

Since each string is itself an array of characters, this creates a natural **two-level indexing** structure:

```c
printf("%c%c\n", words[0][0], words[0][1]);   // prints: hi
printf("%c%c%c\n", words[1][0], words[1][1], words[1][2]);  // prints: bye
```

_(Presented explicitly as a demonstration of underlying mechanics, not as a recommended everyday coding style — a normal program would just use `printf("%s", words[0])` directly.)_

**Q&A — How arrays are created/declared:** Specify a **type**, a **name**, and a **size in square brackets** (e.g., `int scores[N]` or `string words[2]`). Once declared, individual elements are accessed the same way, via square-bracket indexing.

**Q&A — Must arrays be filled index-by-index?** No — arrays can be **statically initialized** in a single line using curly braces (e.g., `int scores[] = {72, 73, 33};`), letting the compiler infer the size automatically. This only works when all values are known upfront (not, for example, when values come from `get_int` at runtime).

---

## 9. String Library Functions

### 9.1 Manually Computing String Length

**First-principles approach**, using the null-terminator insight directly:

```c
string name = get_string("Name: ");
int n = 0;
while (name[n] != '\0')
{
    n++;
}
printf("%i\n", n);
```

**Logic:** Repeatedly check whether the character at the current index is the null terminator; if not, increment the counter and continue. Stops naturally once `\0` is reached.

### 9.2 `strlen` from `string.h`

**Better design — don't reinvent this wheel.** The **`string.h`** header (part of C's standard library, documented via the CS50 manual pages) provides a ready-made function:

```c
#include <string.h>
int n = strlen(name);
```

- `strlen` stands for **"string length"** and internally performs essentially the same null-terminator-scanning logic shown above — but as a pre-written, well-tested, reusable function.
- Can be used inline/nested directly in other expressions, e.g., `printf("%i\n", strlen(name));`, eliminating the need for an intermediate variable entirely.

### 9.3 Loop Efficiency: Avoid Recomputing `strlen`

**Inefficient pattern identified:**

```c
for (int i = 0; i < strlen(s); i++)
{
    printf("%c", s[i]);
}
```

- **Problem:** The loop's condition (`i < strlen(s)`) is **re-evaluated on every single iteration** — meaning `strlen` is recalculated repeatedly, even though the string's length never changes during the loop.
- **Why this matters:** For small strings, the wasted work is negligible (microseconds), but in loops that run **millions of times**, or in larger/more performance-sensitive programs, this kind of redundant computation can meaningfully add up in wasted time and computing resources.

**Fix — compute the length once, store it, and reuse it:**

```c
for (int i = 0, n = strlen(s); i < n; i++)
{
    printf("%c", s[i]);
}
```

- Demonstrates that a `for` loop's initialization clause can declare **multiple variables at once** (as long as they share the same type).

**Caveat noted in lecture:** Modern compilers are often smart enough to automatically detect and optimize away this specific inefficiency on their own — but the underlying **principle of avoiding unnecessary repeated work inside loops** is presented as a general skill worth recognizing and applying manually.

---

## 10. Case Conversion: Good, Better, Best

### 10.1 Manual ASCII Math Approach

**Goal:** Convert a lowercase input string to all uppercase.

**Approach — exploit the fact that lowercase and uppercase ASCII letters are a fixed distance apart:**

```c
for (int i = 0, n = strlen(s); i < n; i++)
{
    if (s[i] >= 'a' && s[i] <= 'z')          // if lowercase
    {
        printf("%c", s[i] - 32);              // shift to uppercase
    }
    else
    {
        printf("%c", s[i]);                   // leave unchanged
    }
}
```

- **Key fact from the ASCII table:** Lowercase letters are consistently **32 values higher** than their uppercase counterparts (e.g., lowercase `a` = 97, uppercase `A` = 65). Subtracting 32 from any lowercase letter's ASCII value converts it to uppercase.
- Demonstrates treating characters as their underlying numeric values for direct arithmetic manipulation — reinforcing the char/int equivalence from [Section 8.1](#81-chars-are-just-numbers).

### 10.2 Using `ctype.h`: `islower()`

**Improvement — don't hand-roll the range check:**

```c
#include <ctype.h>
...
if (islower(s[i]))
{
    printf("%c", s[i] - 32);
}
else
{
    printf("%c", s[i]);
}
```

- **`islower()`** (from the **`ctype.h`** header) encapsulates the same "is this character between 'a' and 'z'?" logic that was previously written manually — reducing risk of subtle bugs and improving readability.

### 10.3 Using `ctype.h`: `toupper()`

**Best/cleanest approach — eliminate the conditional entirely:**

```c
#include <ctype.h>
...
printf("%c", toupper(s[i]));
```

- **`toupper()`** internally checks whether a character is lowercase and converts it if so, **passing already-uppercase (or non-alphabetic) characters through unchanged** — removing the need for any explicit `if`/`else` in the calling code altogether.

**General lesson reinforced:** When a coding task feels tedious, repetitive, or like "surely someone has solved this already," it's often worth checking for an existing library function (from the C standard library, or CS50's own library) rather than reimplementing the logic from scratch. Notably, **standard libraries like `ctype.h` and `string.h` don't require an explicit linker flag** (unlike the CS50 library, which needs `-lcs50` — automatically handled by `make`).

---

## 11. Revisiting `main`: Command-Line Arguments

### 11.1 `int main(void)` vs. `int main(int argc, string argv[])`

Previously, students were told to simply accept `int main(void)` on faith. The lecture now reveals **why**, and introduces the alternative form.

**Form 1 (used exclusively so far):**

```c
int main(void)
```

- `void` explicitly declares that the program **does not accept command-line arguments** — consistent with every program written in the course up to this point (none of which were ever run with extra words after the program's name).

**Form 2 (newly introduced):**

```c
int main(int argc, string argv[])
```

- **`argc`** ("**arg**ument **c**ount") — an `int` representing how many words were typed at the prompt (including the program's own name).
- **`argv`** ("**arg**ument **v**ector") — an **array of strings**, containing each of those words individually.
- **Important constraint:** C's `main` function supports **only these two specific forms** — you cannot invent a third form with different argument counts or types; the names `argc`/`argv` can technically be changed, but their _types_ and _meaning_ (by convention and compiler/OS behavior) cannot.

**Example — `greet.c`, using command-line input instead of `get_string`:**

```c
#include <stdio.h>

int main(int argc, string argv[])
{
    printf("hello, %s\n", argv[1]);
}
```

- Running `./greet David` prints `hello, David` — the name is supplied directly at the command line rather than prompted interactively.
- Running `./greet` with no name typed prints `hello, (null)` — since `argv[1]` doesn't exist in that case. _(Note: this "null" is related to, but distinct in naming/context from, the null character `\0` discussed earlier — described as something to be revisited later in the course.)_

### 11.2 Using `argv[0]` — The Program's Own Name

**Demonstration:**

```c
printf("hello, %s\n", argv[0]);
```

- **`argv[0]` always automatically contains the program's own name** as typed/invoked — regardless of what the source file or compiled binary is actually named.
- **Use case:** Useful for self-referential output, such as printing usage instructions or "thanks for running [program name]" messages that automatically adapt if the program is renamed.

### 11.3 Validating Argument Count with `argc`

**Improved version — check whether the user actually supplied exactly one argument (a name):**

```c
if (argc == 2)   // program name + one additional argument = 2 total
{
    printf("hello, %s\n", argv[1]);
}
else
{
    printf("hello, world\n");
}
```

- `argc` counts **all** words at the prompt, including the program's own name — so requesting exactly one additional argument (e.g., a name) means checking for `argc == 2`.
- Demonstrated behavior: running `./greet David Malan` (i.e., two extra words) results in `argc == 3`, which does **not** match the `== 2` check, so the program falls through to the default "hello, world" branch — illustrating that this validation approach also naturally guards against _too many_ arguments, not just too few.

---

## 12. Exit Status / Return Codes

**Revisiting the `int` in `int main(...)`:** Historically presented without explanation, this return type represents the function's **exit status** — a numeric code indicating whether the program completed successfully or encountered some kind of error.

**Convention:**

- **`return 0;`** conventionally signals **success**.
- **Any nonzero value** (`1`, `2`, `3`, etc.) signals **some kind of failure** — with the specific number's meaning left entirely up to the programmer's own documentation/conventions (analogous to real-world numeric error codes like Zoom's connectivity error codes, or the web's familiar **404 "file not found"** status).

**Example — `status.c`:**

```c
int main(int argc, string argv[])
{
    if (argc != 2)
    {
        printf("missing command line argument\n");
        return 1;
    }
    else
    {
        printf("hello, %s\n", argv[1]);
        return 0;
    }
}
```

### 12.1 Checking Exit Status with `echo $?`

**Terminal trick demonstrated:** After running a compiled program, the special command:

```
echo $?
```

reveals the **exit status** the just-run program returned — normally invisible/irrelevant to typical end users, but essential for automated tooling.

**Practical relevance mentioned:** This is (in part) how CS50's own automated grading tool, **`check50`**, can determine whether a submitted program behaved correctly — by inspecting this otherwise-hidden exit status value, among other checks.

### 12.2 Real-World Example: `cowsay`

**Demonstration (presented as playful, not academically essential):** The classic Unix novelty program **`cowsay`** takes command-line arguments for both the **message** (e.g., `cowsay moo`) and stylistic **flags** (e.g., `cowsay -f dragon "some message"`), producing ASCII-art creatures with speech bubbles.

**Underlying mechanism (as explained):** Conceptually, such a program is just applying the same fundamentals already covered — parsing `argc`/`argv`, using conditionals to check which "character" flag was requested, and printing the corresponding pre-written ASCII art — composed into a more elaborate, polished result.

---

## 13. Introduction to Cryptography

### 13.1 Plaintext, Ciphertext, and Keys

**Mental model presented for this week's cryptography problem set:**

- **Plaintext** — the original, human-readable message (e.g., in English or another natural language).
- **Cipher** — an algorithm (conceptually a "black box") that transforms plaintext into unreadable **ciphertext**.
- **Key** — a secret value shared in advance between sender and recipient, required both to **encrypt** (plaintext → ciphertext) and **decrypt** (ciphertext → plaintext) the message reliably.

**Core requirement emphasized:** Simply "scrambling" letters randomly doesn't work as an encryption scheme, because the recipient would have no systematic way to reverse an arbitrary scramble. A **shared, agreed-upon transformation rule** (the key) is what makes reversal possible.

### 13.2 The Caesar Cipher

**Named after Julius Caesar**, historically used to communicate with his generals — an early, low-tech encryption method.

**Mechanism:** Shift every letter in the plaintext forward by a fixed number of positions (the key) in the alphabet, wrapping around from `Z` back to `A` as needed.

**Example — key = 1 ("rotate by 1"):**

- Plaintext `HI` → Ciphertext `IJ` (each letter shifted forward by 1: H→I, I→J).

**Decryption:** The recipient reverses the process by **subtracting** the same key value from each ciphertext letter (effectively applying the _negative_ of the encryption key).

**Note on scope:** As demonstrated, this simple version of the cipher only transforms **alphabetic letters** — punctuation and spaces are left unaffected in the lecture's examples (a design choice/limitation, not an inherent requirement of all Caesar-style ciphers).

### 13.3 ROT13 and Its Weaknesses

- **ROT13** ("rotate by 13") is a specific, historically notable variant of the Caesar cipher, long used informally online (e.g., by Reddit and other forums) to obscure spoilers — requiring a small amount of deliberate effort (clicking a button or running code) to read, without providing genuine security.
- **Example:** With key 13, `HI` becomes `UV` (H and I shifted 13 positions forward).
- **Special case — ROT26 is pointless:** Since the English alphabet has exactly 26 letters, shifting by 26 wraps completely back around to the original letters (`A→A`, `B→B`, etc.) — providing **zero actual obfuscation**.

### 13.4 Brute-Force Vulnerability

**Core security weakness of the Caesar cipher (and ROT13 specifically):** There are only **25 meaningfully distinct possible keys** (1 through 25 — excluding 0 and 26, which produce no change at all).

**Implication:** An adversary intercepting a Caesar-ciphered message can simply **try every possible key** (an exhaustive **brute-force** search) until the decrypted result becomes recognizable, readable text. With so few possibilities, this is trivial for any modern computer (or even a patient human) to defeat.

**Framing for the rest of the course:** The Caesar cipher's historical significance lies in being an early, intuitive example of encryption — but its extreme vulnerability to brute-forcing motivates the need for **more sophisticated cryptographic algorithms**, which the course will explore in more depth going forward.

**Closing demonstration (as a playful send-off):** The lecture ends by presenting an encrypted closing message using this same rotate-by-one logic in reverse (subtracting 1 from each letter), which decrypts to reveal: _"H I I S W A S C S 5 0"_ → **"this was CS50."**

---

## 14. Key Terms Glossary

| Term                                  | Definition                                                                                                                                                     |
| ------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Bug**                               | An error or mistake in a program; term popularized following Grace Hopper's team finding a literal moth in the Harvard Mark II                                 |
| **Syntax error**                      | A grammatical mistake in code that prevents successful compilation                                                                                             |
| **Logic error**                       | Code that compiles and runs but produces incorrect/unintended behavior                                                                                         |
| **`debug50`**                         | CS50's command to launch an interactive line-by-line debugger inside VS Code                                                                                   |
| **Breakpoint**                        | A marker telling a debugger where to pause program execution                                                                                                   |
| **Step Over**                         | Debugger action: execute the current line and pause on the next, without entering called functions                                                             |
| **Step Into**                         | Debugger action: if the current line calls a function, jump inside and pause at its first line                                                                 |
| **Garbage value**                     | A leftover, effectively random bit pattern occupying memory that hasn't yet been explicitly initialized                                                        |
| **Rubber duck debugging**             | Explaining a problem aloud (traditionally to a rubber duck) to surface flawed reasoning without outside help                                                   |
| **Clang**                             | The specific open-source C compiler used underneath the `make` command in this course                                                                          |
| **`a.out`**                           | Historical default output filename from the Clang/assembler ("assembler output") when no custom name is specified                                              |
| **Preprocessing**                     | Compilation sub-step that expands `#include` directives by copying in header file contents                                                                     |
| **Assembly code**                     | A low-level, human-readable (but cryptic) instruction language, intermediate between C and raw machine code                                                    |
| **Assembling**                        | Compilation sub-step converting assembly code into machine code (zeros and ones) for a specific file                                                           |
| **Linking**                           | Compilation sub-step combining a program's own compiled code with pre-compiled library code it depends on                                                      |
| **Linker error**                      | An error indicating the compiler could not find/link required library code (e.g., missing `-lcs50`)                                                            |
| **Decompiling / Reverse engineering** | Attempting to reconstruct source code or logic from compiled machine code                                                                                      |
| **Array**                             | A fixed-size, contiguous block of memory holding multiple values of the same type, accessed via zero-based indexing                                            |
| **Magic number**                      | A literal value hard-coded in multiple places in code, risking inconsistency if only one instance is later changed                                             |
| **Constant (`const`)**                | A variable whose value is not intended to change after initialization; conventionally named in all capital letters                                             |
| **Function prototype**                | A function's signature (name, parameters, return type) declared in advance of its full definition, so the compiler recognizes it before that point in the code |
| **`char`**                            | A C data type representing a single character, internally stored as its numeric ASCII value                                                                    |
| **String**                            | (Via the CS50 library) A sequence of characters; under the hood, simply an array of `char` values                                                              |
| **Null character (`\0`)**             | A special byte (value `0`) automatically appended to the end of string literals, marking where the string's content ends                                       |
| **`strlen()`**                        | Standard C library function (from `string.h`) that returns a string's length by counting characters up to its null terminator                                  |
| **`islower()` / `toupper()`**         | Standard C library functions (from `ctype.h`) for checking/converting character case                                                                           |
| **`argc`**                            | "Argument count" — the number of words typed at the command-line prompt when running a program, including the program's own name                               |
| **`argv`**                            | "Argument vector" — an array of strings containing each word typed at the command-line prompt                                                                  |
| **Exit status**                       | A numeric value returned by a program (via `main`'s return value) indicating success (`0`) or a specific type of failure (nonzero)                             |
| **`echo $?`**                         | A terminal command that displays the exit status of the most recently run program                                                                              |
| **Plaintext**                         | The original, unencrypted, human-readable form of a message                                                                                                    |
| **Ciphertext**                        | The scrambled, unreadable output of an encryption process                                                                                                      |
| **Cipher**                            | An algorithm used to transform plaintext into ciphertext (and back, given the correct key)                                                                     |
| **Key**                               | A secret value required to correctly encrypt and/or decrypt a message using a given cipher                                                                     |
| **Caesar cipher**                     | A classical encryption method shifting each letter of the alphabet forward by a fixed number of positions (the key)                                            |
| **ROT13**                             | A specific, well-known Caesar cipher variant using a shift of exactly 13 positions                                                                             |
| **Brute-force attack**                | An attack strategy that exhaustively tries every possible key (or input) until the correct one is found                                                        |

---

## 📌 Overarching Lessons of the Lecture

1. **Debugging is a learnable skill, not guesswork.** `printf` statements, the `debug50` interactive debugger, and rubber-duck-style verbal reasoning are all legitimate, complementary techniques — to be tried _before_ escalating to AI or human help.
2. **Syntax errors and logic errors are fundamentally different problems.** The compiler catches the former automatically; the latter requires human (or automated correctness-checking) judgment, since the compiler has no concept of a programmer's actual intent.
3. **"Compiling" is really four automated sub-steps** (preprocessing, compiling to assembly, assembling to machine code, linking) — understanding this demystifies cryptic error messages (e.g., linker errors) and explains why programs are architecture-specific.
4. **C requires explicit management of low-level details** (data types, array lengths, memory layout) that many modern/higher-level languages abstract away — understanding _why_ (via direct exploration of memory, `char`/`int` equivalence, and the null terminator) builds durable, transferable intuition about how computers actually work.
5. **Don't reinvent solved problems.** Standard library functions (`strlen`, `islower`, `toupper`, etc.) exist precisely so programmers don't need to hand-roll common, well-tested logic — improving both correctness and code clarity.
6. **Simple designs reveal their own limitations under scrutiny.** The Caesar cipher illustrates that "some" obfuscation isn't the same as real security — a theme that will recur and deepen throughout the course's cryptography unit.

---

_End of Week 2 notes._
