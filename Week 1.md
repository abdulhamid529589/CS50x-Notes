# CS50 — Week 1 Course Notes

### Introduction to C: From Scratch to Real Code

_Compiled and structured from lecture transcription_

---

## 📚 Table of Contents

1. [From Scratch to C — The Big Picture](#1-from-scratch-to-c--the-big-picture)
2. [Source Code vs Machine Code & Compilers](#2-source-code-vs-machine-code--compilers)
3. [The Development Environment: VS Code](#3-the-development-environment-vs-code)
4. [GUI vs CLI](#4-gui-vs-cli)
5. [Writing & Running Your First C Program](#5-writing--running-your-first-c-program)
6. [Anatomy of `printf` — Comparing Scratch to C](#6-anatomy-of-printf--comparing-scratch-to-c)
7. [Escape Sequences](#7-escape-sequences)
8. [Common Compiler Errors & How to Read Them](#8-common-compiler-errors--how-to-read-them)
9. [Header Files & Libraries](#9-header-files--libraries)
10. [CS50's Manual Pages & Documentation](#10-cs50s-manual-pages--documentation)
11. [CS50 Library Functions (`get_string`, `get_int`, etc.)](#11-cs50-library-functions-get_string-get_int-etc)
12. [Variables & Format Specifiers](#12-variables--format-specifiers)
13. [Linux / The Operating System Underneath](#13-linux--the-operating-system-underneath)
14. [Essential Linux/CLI Commands](#14-essential-linuxcli-commands)
15. [Conditionals in C](#15-conditionals-in-c)
16. [Comparison & Logical Operators](#16-comparison--logical-operators)
17. [Data Types in C](#17-data-types-in-c)
18. [Variables, Increment/Decrement Operators](#18-variables-incrementdecrement-operators)
19. [Practical: Building a `compare.c` Program](#19-practical-building-a-comparec-program)
20. [Flowcharts — Visualizing Conditional Logic](#20-flowcharts--visualizing-conditional-logic)
21. [Practical: Building an `agree.c` Program (chars & logical OR)](#21-practical-building-an-agreec-program-chars--logical-or)
22. [Loops: `while`, `for`, and `do while`](#22-loops-while-for-and-do-while)
23. [Practical: Building `cat.c` (Meowing Program)](#23-practical-building-catc-meowing-program)
24. [Infinite Loops & `continue`/`break`](#24-infinite-loops--continuebreak)
25. [Variable Scope](#25-variable-scope)
26. [Custom Functions & Prototypes](#26-custom-functions--prototypes)
27. [Correctness, Design & Style](#27-correctness-design--style)
28. [CS50 Tools: `check50`, `design50`, `style50`](#28-cs50-tools-check50-design50-style50)
29. [Practical: Building "Mario" (1D & 2D Loops)](#29-practical-building-mario-1d--2d-loops)
30. [Constants](#30-constants)
31. [Arithmetic Operators & Building a Calculator](#31-arithmetic-operators--building-a-calculator)
32. [Integer Overflow](#32-integer-overflow)
33. [Real-World Overflow Bugs (Boeing 787, Pac-Man)](#33-real-world-overflow-bugs-boeing-787-pac-man)
34. [Floating-Point Imprecision & Truncation](#34-floating-point-imprecision--truncation)
35. [The Y2K Problem & The Year 2038 Problem](#35-the-y2k-problem--the-year-2038-problem)
36. [Key Takeaways](#36-key-takeaways)

---

## 1. From Scratch to C — The Big Picture

- **Week 0** focused on **Scratch** — a graphical, drag-and-drop programming language where puzzle pieces interlock only if logically valid.
- The purpose of Scratch was to build a **mental model** for fundamental programming constructs that recur throughout the course (and in later languages like Python):
  - Functions, return values, arguments, variables, loops, conditionals.
- **Week 1** transitions to **C**. The core message: _the ideas are the same as last week — only the syntax changes._ It will look "scarier," but with practice comes muscle memory.

> **Analogy:** MIT's tradition of "hacks" once connected a real drinking fountain to a fire hydrant, with a sign reading _"Getting an education from MIT is like trying to drink from a fire hose."_ This captures how CS50 may feel at times — but what matters most is your growth **3 months from now**, not how overwhelmed you feel day-to-day.

### Visual Comparison

- Scratch's "Hello World" program used a **yellowish "when green flag clicked" block** (which doesn't do much itself, just starts the program) and a **purple "say" block** (which does the real work).
- In C, this maps to: a yellowish `#include`/`int main(void)` boilerplate (waved away for now) and a purple `printf(...)` line (which does the real work).

---

## 2. Source Code vs Machine Code & Compilers

| Term             | Definition                                                                                      |
| ---------------- | ----------------------------------------------------------------------------------------------- |
| **Source Code**  | Code that **humans** write and can understand (with training/practice) — e.g., C code           |
| **Machine Code** | The **zeros and ones** that computers actually understand                                       |
| **Compiler**     | A piece of software that takes **source code as input** and produces **machine code as output** |

- This follows the same **input → black box → output** mental model introduced in Week 0.
- There are many different compilers in the world; CS50 has students use one of the most popular ones.

---

## 3. The Development Environment: VS Code

- **Visual Studio Code (VS Code)** is a hugely popular tool in the real programming industry — professional programmers use it regularly.
- CS50 provides a **cloud-based version** of VS Code (accessible via any web browser, at `cs50.dev`) so that:
  - Everything is **pre-installed and pre-configured**.
  - Students avoid early technical-support headaches.
  - Students can later **install VS Code locally** on their own Mac/PC for free, becoming fully independent of CS50's tools.

### Interface Layout

| Area                               | Purpose                                                                                                    |
| ---------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| **Top area / tabs**                | Where code files (e.g., `hello.c`) open in tabs                                                            |
| **Left sidebar (File Explorer)**   | Shows files and folders created over time                                                                  |
| **Far-left (Activity Bar)**        | A modern "menu" for opening/closing panels and features (often hidden by the instructor to reduce clutter) |
| **Bottom-right (Terminal window)** | Where commands are typed to compile and run code                                                           |

- **Syntax highlighting**: VS Code automatically color-codes different parts of your code (keywords, strings, etc.) as you type, to help different features "jump out" visually — no need to manually manage this.

---

## 4. GUI vs CLI

| Term    | Full Name                | Meaning                                                                     |
| ------- | ------------------------ | --------------------------------------------------------------------------- |
| **GUI** | Graphical User Interface | An interface with icons, buttons, windows — interacted with via mouse/touch |
| **CLI** | Command Line Interface   | An interface where you type text commands (e.g., the terminal window)       |

- VS Code combines **both**: it's a GUI overall, but contains a **CLI** (the terminal) within it.
- The world of computing software is generally divided into GUIs and CLIs — some software has both.
- **Convention:** The command prompt is marked with a `$` (dollar sign) — this has nothing to do with currency, it's just a computing convention (some systems use `^` or `>` instead).

---

## 5. Writing & Running Your First C Program

### Step 1: Create the File

```bash
code hello.c
```

- `code` is a VS-Code-specific command that creates/opens a file.
- **Convention:** No spaces in filenames, no capitalization — unlike typical Mac/PC file naming habits.

### Step 2: Write the Code

```c
#include <stdio.h>

int main(void)
{
    printf("hello, world\n");
}
```

### Step 3: Compile the Code

```bash
make hello
```

- `make hello` looks for a file called `hello.c` (inferring this from the name you gave) and compiles it into an executable program called `hello`.
- **No output after compiling = a good sign.** Output usually indicates an error.

### Step 4: Run the Program

```bash
./hello
```

- The `./` means: "go into the **current folder** and run the program named `hello`."
- This is **not** double-clicking an icon — it's a CLI-based paradigm you'll see again and again, regardless of what you name your programs.

> **Q&A note:** The program doesn't have to be named "hello" — it can be named anything (e.g., "goodbye"), as long as you're consistent between the filename, the `make` command, and the run command.

---

## 6. Anatomy of `printf` — Comparing Scratch to C

| Scratch Concept                           | C Equivalent                                           |
| ----------------------------------------- | ------------------------------------------------------ |
| A **function/verb** (small piece of work) | `printf` (the "f" means **format** the printed output) |
| Oval-shaped input slot                    | Parentheses `( )`                                      |
| Text to display                           | The text inside double quotes: `"hello, world"`        |

### Required Syntax Details in C (vs. Scratch's simplicity)

1. **Double quotes** `" "` around the text to print — signals to the computer that this is literal text to display, not a special function/variable name.
2. A trailing **`\n`** (backslash-n) — usually added at the end to move the cursor to a new line.
3. A terminating **semicolon** `;` — finishes the "thought" (like a period ends an English sentence).

> **Key lesson:** In C, if you mess up almost any of these details, something will go wrong. This is normal for a first program — muscle memory develops with practice for spotting these details.

### Function Analogy (Math)

Just like a math function `f(x)` takes an input and produces an output, a code function takes **arguments** (inputs) and can produce:

- **Side effects** (visual/audio things that happen, e.g., printing to the screen) — like Scratch's "say" block.
- **Return values** (reusable values) — covered later with `get_string`.

---

## 7. Escape Sequences

**Definition:** Special symbol sequences (starting with a backslash) that perform an unusual action rather than printing literal characters.

| Escape Sequence | Meaning                                                                                                 |
| --------------- | ------------------------------------------------------------------------------------------------------- |
| `\n`            | New line (moves cursor to the next line)                                                                |
| `\r`            | Carriage return (moves cursor back to the left, like an old typewriter's return lever)                  |
| `\"`            | A literal double-quote character (needed because a plain `"` would be interpreted as ending the string) |

### Why Not Just Press Enter in the Source Code Instead?

- Doing so would put part of a single statement across multiple lines in your source file — this:
  1. **Violates a core principle** that most programming languages are **line-based** (each "thought" ideally starts and finishes on one line).
  2. Looks **sloppy/incorrect** stylistically.
- Escape sequences (like `\n`) solve this cleanly — you stay on one line in your source code, but the sequence still produces a new line _when the program runs_.

---

## 8. Common Compiler Errors & How to Read Them

### Missing Semicolon

```
error: expected semicolon after expression
```

- The compiler reports the **file name**, **line number**, and often the **character position** of the problem, plus a caret (`^`) pointing at the mistake.
- **Fix:** Add the missing semicolon and recompile.

### Missing `#include <stdio.h>`

```
error: call to undeclared library function 'printf' with type ...
```

- This happens if you forget to `#include` the header file that declares `printf`.
- **Fix:** Add back `#include <stdio.h>` at the top of the file.

> **General workflow:** Whenever you see an error, look at the **file, line number, and message** — the compiler is not only translating code, it's also actively **finding mistakes and pointing you toward them**.

---

## 9. Header Files & Libraries

- **Header files** end in `.h` (not `.c`) and contain **code that other people wrote** that you can reuse in your own programs.
- A **library** = reusable code someone else wrote (e.g., last week's chatbot used a library from OpenAI).
- `#include <stdio.h>` tells the compiler: _"I didn't write all this code myself — please include the definitions from this other file (Standard Input/Output header) before compiling."_
- Common typo to avoid: it's **`stdio.h`**, not "studio.h."

---

## 10. CS50's Manual Pages & Documentation

- The traditional way to look up C functions is the official **"man pages"** (manual pages) — but these were often written decades ago for advanced programmers, and are hard to read for beginners.
- CS50 has imported and **simplified** this documentation at **`manual.cs50.io`** — written in more approachable, teaching-assistant-style language.

### What a CS50 Manual Page Includes

- The **function name** and a brief description (e.g., `printf` — "prints to the screen").
- A **synopsis** (with an option to toggle to the original, more technical documentation).
- The **header file** the function is defined in.
- A **prototype** (the function's first line of code).
- An **English description** and **example code**.

> **Recommendation:** Even with AI tools available (like the CS50 "duck"), students should build the habit of checking **official documentation** — since AI tools are themselves trained on this kind of source material.

---

## 11. CS50 Library Functions (`get_string`, `get_int`, etc.)

Because certain tasks (like getting user input) are surprisingly hard to do in raw C — especially for beginners — CS50 created its **own header file**, `cs50.h`, as a set of **"training wheels"** for the first few weeks of the course (removed later on).

### Key Functions in `cs50.h`

| Function          | Returns             |
| ----------------- | ------------------- |
| `get_string(...)` | A **string** (text) |
| `get_int(...)`    | An **integer**      |
| `get_char(...)`   | A **character**     |
| `get_float(...)`  | A **float**         |
| `get_double(...)` | A **double**        |

> Note: There is **no `get_bool`** — CS50 chose not to implement this, since getting a raw true/false directly from a user isn't typically that useful.

### Comparing Scratch's `ask` to C's `get_string`

| Scratch                                                         | C                                                                               |
| --------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| `ask "What's your name?" and wait`                              | `get_string("What's your name? ")`                                              |
| Answer stored automatically in a fixed variable called `answer` | Must be manually stored via `string answer = get_string("What's your name? ");` |

### Key C Syntax Rules Introduced Here

- To **keep** a function's return value, use the **assignment operator `=`**, with the variable name on the **left**.
- You must specify the variable's **type** (e.g., `string`) when creating it.
- End the statement with a **semicolon**.

```c
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    string answer = get_string("What's your name? ");
    printf("hello, %s\n", answer);
}
```

> **Important distinction demonstrated:** Writing `printf("hello, answer\n");` literally prints the word "answer" — since it's inside quotes, it's treated as literal text, not a reference to the variable. This is why **format specifiers** (like `%s`) are needed (see Section 12).

---

## 12. Variables & Format Specifiers

**Problem:** You cannot directly "join" text and a variable's value inside a `printf` string the way Scratch lets you snap blocks together. Instead, C uses **placeholders** inside the string, plus additional arguments after a comma.

### Format Specifiers by Type

| Specifier | Data Type               |
| --------- | ----------------------- |
| `%s`      | string                  |
| `%c`      | char (single character) |
| `%f`      | float                   |
| `%i`      | int                     |
| `%li`     | long (long integer)     |

### Example

```c
string answer = get_string("What's your name? ");
printf("hello, %s\n", answer);
```

- The **first argument** to `printf` is the format string (with a placeholder), and the **second argument** is the variable whose value should be substituted in.
- `printf` can take **2, 3, 4, or more arguments** — it depends on how many placeholders are used.
- **Important distinction (Q&A):** The first argument (the question/format text) is provided by the _programmer_ in advance; the second argument (the actual value) comes from the _user_ at runtime — but both are still just "inputs" to the function.

---

## 13. Linux / The Operating System Underneath

- CS50's cloud coding environment (`cs50.dev`) runs on **Linux**, a descendant of the older **Unix** operating system.
- Linux is extremely popular in **servers and the cloud** — it can support **thousands to millions of users** and tends to run without a GUI (making it faster/leaner), though it _can_ have one.
- Linux comes with a large set of **CLI commands**.

### Commands Used So Far

| Command         | Purpose                                                            |
| --------------- | ------------------------------------------------------------------ |
| `code`          | VS-Code-specific — creates/opens a file                            |
| `make`          | Compiles source code into an executable (a simplification for now) |
| `./programname` | Runs a compiled program                                            |

---

## 14. Essential Linux/CLI Commands

| Command                     | Purpose                                                 |
| --------------------------- | ------------------------------------------------------- |
| `ls`                        | **List** files/folders in the current directory         |
| `mkdir <name>`              | **Make directory** (create a new folder)                |
| `rmdir <name>`              | **Remove directory** (delete an empty folder)           |
| `rm <file>`                 | **Remove** a file (prompts y/n confirmation)            |
| `mv <source> <destination>` | **Move** a file (can also be used to **rename** a file) |
| `cp <source> <destination>` | **Copy** a file                                         |
| `cd <folder>`               | **Change directory** (move into a folder)               |
| `cd ..`                     | Move **up one level** (to the parent folder)            |
| `cd` (alone)                | Return to your **home/default directory**               |

### Practical Demonstration Highlights

```bash
rm hello                 # delete the compiled program (keeps hello.c)
mkdir hello               # create a new folder called "hello"
mv hello.c hello/          # move hello.c into that folder
ls                        # shows folder "hello/" (color-coded, trailing slash)
cd hello                  # move into the folder (prompt updates to show current location)
ls                        # now shows only hello.c
make hello                 # recompile inside this folder
rm hello                  # delete the compiled binary again
mv hello.c old.c            # rename a file (mv works for renaming too)
mv old.c hello.c            # rename back
cp hello.c backup.c         # make a backup copy
rm backup.c                # remove the backup
mv hello.c ..               # move the file UP to the parent folder (".." = parent directory)
cd ..                     # move up
cd                        # jump back to home/default directory
```

> **Key notational facts:**
>
> - A single dot `.` means **"this folder."**
> - Two dots `..` mean **"one level up" (the parent folder).**
> - There is no "triple dot" notation — different syntax is needed for going up multiple levels (covered later).
> - You **cannot** have a file and a folder with the exact same name in the exact same location (they'd collide) — but you _can_ have a file inside a folder that has the same name as the folder itself (no collision, since they're at different "levels").

---

## 15. Conditionals in C

### Scratch → C Translation

| Scratch                              | C                                             |
| ------------------------------------ | --------------------------------------------- |
| Orange "if" block (fork in the road) | `if (...)`                                    |
| Boolean expression inside the block  | Boolean expression inside parentheses `(...)` |
| Nested "say" block                   | Statement(s) inside curly braces `{ }`        |

```c
if (x < y)
{
    printf("x is less than y\n");
}
```

### If/Else

```c
if (x < y)
{
    printf("x is less than y\n");
}
else
{
    printf("x is not less than y\n");
}
```

### If/Else If/Else (Multi-Way Branch)

```c
if (x < y)
{
    printf("x is less than y\n");
}
else if (x > y)
{
    printf("x is greater than y\n");
}
else
{
    printf("x is equal to y\n");
}
```

> **Design lesson (important):** Using **three separate `if` statements** (all independent) instead of `if / else if / else` is technically correct but **poorly designed** — the computer wastes time checking conditions that are already logically impossible given a prior check. Using `else if`/`else` avoids these unnecessary/redundant checks. This exact mistake and its fix are demonstrated live in Section 19.

### Style Convention

- Curly braces are used even for the "hugging" visual effect similar to how Scratch blocks nest — though technically optional for single-line bodies in C, CS50's style guide requires them for consistency (so that all programmers' code looks the same, as is common practice at real companies).
- **4-space indentation** is the convention.

---

## 16. Comparison & Logical Operators

### Comparison Operators

| Operator | Meaning                                                                                        |
| -------- | ---------------------------------------------------------------------------------------------- |
| `=`      | **Assignment** (copy the right side's value into the variable on the left) — **not** equality! |
| `==`     | **Equality** ("is equal to")                                                                   |
| `<`      | Less than                                                                                      |
| `>`      | Greater than                                                                                   |
| `<=`     | Less than or equal to                                                                          |
| `>=`     | Greater than or equal to                                                                       |
| `!=`     | **Not equal to** ("bang equals")                                                               |

> **Why `==` instead of `=` for equality?** Since `=` was already claimed for assignment, language designers needed a different symbol for equality — hence doubling it to `==`. (Scratch, designed for kids, avoids this confusion by using a single `=` for equality instead.)

### Logical Operators

| Operator | Meaning         |
| -------- | --------------- |
| `\|\|`   | Logical **OR**  |
| `&&`     | Logical **AND** |

**Example — combining two equality checks:**

```c
if (c == 'y' || c == 'Y')
{
    printf("Agreed.\n");
}
```

> Using `&&` here would be **incorrect** — a single character variable cannot simultaneously equal both lowercase `y` AND uppercase `Y`, so that combination would never evaluate true.

---

## 17. Data Types in C

| Type     | Description                                                                                         |
| -------- | --------------------------------------------------------------------------------------------------- |
| `bool`   | True or false (from `cs50.h`, closely tied to Boolean expressions)                                  |
| `char`   | A **single character** (C distinguishes single characters from full strings, unlike some languages) |
| `float`  | A **floating-point** (decimal/real) number — typically uses **32 bits**                             |
| `double` | Like `float` but with **64 bits** — more precision (more significant digits), but still finite      |
| `int`    | A whole number (integer) — typically **32 bits**                                                    |
| `long`   | A whole number using **64 bits** — a much larger range than `int`, but still finite                 |
| `string` | Text (technically provided via `cs50.h`, not native C — more on this in future weeks)               |

### Bit-Width & Range Implications

- With **32 bits**, you can represent roughly **4 billion** distinct values total.
- For a _signed_ `int` (allowing negatives), that's roughly **±2 billion**.
- **Key tension:** There are infinitely many real numbers, but only a finite number of bits — so `float`/`double` can only ever **approximate** most real numbers (elaborated in Section 34).

---

## 18. Variables, Increment/Decrement Operators

### Declaring & Assigning

```c
int counter = 0;
```

- Just like `get_string`, C variables require both a **type** and a **name**, plus a terminating semicolon.

### Incrementing (3 Equivalent Ways)

| Style         | Code                     |
| ------------- | ------------------------ |
| Most explicit | `counter = counter + 1;` |
| Shorthand     | `counter += 1;`          |
| Most common   | `counter++;`             |

> `counter++` only increments by exactly 1 — there's no `+++` or similar for other amounts; use `+=` for larger increments (e.g., `counter += 2;`).

Similarly, decrementing: `counter--;` or `counter -= 1;`

---

## 19. Practical: Building a `compare.c` Program

### Version 1 (Incomplete — Missing Cases)

```c
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    int x = get_int("What's x? ");
    int y = get_int("What's y? ");

    if (x < y)
    {
        printf("x is less than y\n");
    }
}
```

> **Bug demonstrated:** Entering `x=2, y=1` produces **no output at all**, since there's no handling for the "not less than" case.

### Version 2 (Adding `else`)

```c
if (x < y)
{
    printf("x is less than y\n");
}
else
{
    printf("x is not less than y\n");
}
```

> Works, but entering `x=1, y=1` gives the imprecise message "x is not less than y" (technically true, but not very informative).

### Version 3 — The Design Mistake (All Independent `if`s)

```c
if (x < y)
{
    printf("x is less than y\n");
}
if (x > y)
{
    printf("x is greater than y\n");
}
if (x == y)
{
    printf("x is equal to y\n");
}
```

> **Flagged as bad design:** All three conditions get checked **every time**, even when the answer is already logically determined by the first check — wasting computer/user time.

### Version 4 — The Correct, Efficient Design

```c
if (x < y)
{
    printf("x is less than y\n");
}
else if (x > y)
{
    printf("x is greater than y\n");
}
else
{
    printf("x is equal to y\n");
}
```

> The final `else` needs **no condition at all**, since logically, if it's neither less-than nor greater-than, it must be equal.

---

## 20. Flowcharts — Visualizing Conditional Logic

For visual learners, code logic can be drawn as a **flowchart**:

- **Diamond shapes** represent Boolean/decision points (yes/no, true/false branches).
- **Rectangles** represent actions (e.g., printing a message).
- **Start** and **Stop** mark the program's entry/exit points.

### Comparing the "All Independent `if`s" vs. "if/else if/else" Flowcharts

- The **independent-`if`s** version has a **longer** flowchart — every path must pass through all three diamond-shaped questions, even after an answer is already known.
- The **if/else if/else** version has **shortcuts** — as soon as one condition is met, the flowchart proceeds directly to the ending, skipping the remaining unnecessary checks.

> This exercise reinforces that indentation and code structure are a textual representation of the same logical flow a flowchart would show — and that eliminating redundant checks is both a **stylistic** and a **performance** improvement.

---

## 21. Practical: Building an `agree.c` Program (chars & logical OR)

### Initial Version

```c
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    char c = get_char("Do you agree? ");

    if (c == 'y')
    {
        printf("Agreed.\n");
    }
    else if (c == 'n')
    {
        printf("Not agreed.\n");
    }
}
```

> **Note:** Single quotes (`'y'`, `'n'`) are used for **single characters** (`char`), as opposed to double quotes for full **strings**.

### Bug Discovered: Uppercase Input Doesn't Work

- Typing `Y` (uppercase) fails to register as "agreed" with the code above.
- `get_char` (like other CS50 functions) will **keep re-prompting** the user if they type more than one character — but it does **not** automatically treat `Y` the same as `y`.

### First (Redundant) Fix — Duplicated Logic

```c
else if (c == 'Y')
{
    printf("Agreed.\n");   // duplicated logic — bad design!
}
```

> **Flagged as bad design:** This duplicates the "Agreed." message logic — if you ever need to change that message, you'd have to update it in two places (a maintenance risk).

### Better Fix — Logical OR

```c
if (c == 'y' || c == 'Y')
{
    printf("Agreed.\n");
}
else
{
    printf("Not agreed.\n");
}
```

> This combines two Boolean expressions into one condition using `||` (logical OR), avoiding duplicated code.

---

## 22. Loops: `while`, `for`, and `do while`

### `while` Loop (Scratch's "repeat" → C's `while`)

There's no direct `repeat` keyword in C — a `while` loop achieves the same effect.

**Counting down:**

```c
int i = 3;
while (i > 0)
{
    printf("meow\n");
    i--;
}
```

**Counting up:**

```c
int i = 0;
while (i < 3)
{
    printf("meow\n");
    i++;
}
```

### `for` Loop (More Compact Alternative)

```c
for (int i = 0; i < 3; i++)
{
    printf("meow\n");
}
```

- Reads as: **initialize** (`int i = 0`), **check condition** (`i < 3`), execute body if true, then **update** (`i++`), and repeat the condition check — all condensed onto one line.
- Functionally identical to the `while` loop version, just more compact — and is the **more common convention** in practice.

### `do while` Loop (Condition Checked at the END, Not the Start)

```c
int n;
do
{
    n = get_int("What's n? ");
}
while (n < 0);
```

- Useful when you want to **do something at least once** before checking whether to repeat — e.g., you must ask the user a question before you can know whether their answer was valid.
- **Coyote/Road Runner meme analogy used in lecture:** The Road Runner checks a condition ("Am I on solid ground?") _before_ running (like a `while` loop checking first) — but Wile E. Coyote runs first and only checks _afterward_ whether he's still on solid ground (like a `do while` loop, checking at the end).

---

## 23. Practical: Building `cat.c` (Meowing Program)

### Bad Version — Copy-Pasting (No Loop)

```c
printf("meow\n");
printf("meow\n");
printf("meow\n");
```

> Flagged immediately as **bad design** — works, but is repetitive/inflexible.

### Improved — Using a Loop (Counting Down)

```c
int i = 3;
while (i > 0)
{
    printf("meow\n");
    i--;
}
```

### Improved — Counting Up (More "Normal Person" Style)

```c
int i = 0;
while (i < 3)
{
    printf("meow\n");
    i++;
}
```

### Most Compact — `for` Loop

```c
for (int i = 0; i < 3; i++)
{
    printf("meow\n");
}
```

> **Convention:** Start counting at 0, count **up to but not through** the target value — this way both the starting and ending boundary are visible/explicit in the loop's condition.

### Dynamic Version — Ask the User How Many Times

```c
int n = get_int("Meow how many times? ");
for (int i = 0; i < n; i++)
{
    printf("meow\n");
}
```

---

## 24. Infinite Loops & `continue`/`break`

### A Deliberate Infinite Loop

```c
while (true)
{
    printf("meow\n");
}
```

- `while (true)` runs **forever** unless manually interrupted.
- Demonstrated live in class — the terminal became flooded with endless "meow" output, and VS Code eventually warned about high CPU usage.
- **Fix:** Press **Ctrl+C** in the terminal to interrupt/kill a runaway process.

### Using an Infinite Loop _Intentionally_, With Manual Exit Logic

```c
while (true)
{
    n = get_int("What's n? ");
    if (n < 0)
    {
        continue;   // go back to the top of the loop, ask again
    }
    else
    {
        break;      // exit the loop entirely
    }
}
```

| Keyword     | Effect                                                                         |
| ----------- | ------------------------------------------------------------------------------ |
| `continue;` | Jumps back to the **top** of the loop (re-evaluates the condition and repeats) |
| `break;`    | Immediately **exits** the loop (jumps past its closing curly brace)            |

### Simplified — Only `break` Is Needed

```c
while (true)
{
    n = get_int("What's n? ");
    if (n >= 0)
    {
        break;
    }
}
```

> Since you only need to react to _one_ of the two outcomes (valid input), a single `if` + `break` suffices — using both `continue` and `break` together (as shown just above) is unnecessarily verbose.

### Cleanest — `do while` Loop (No Manual Break Needed)

```c
int n;
do
{
    n = get_int("What's n? ");
}
while (n < 0);
```

> This is the most elegant solution — the condition-checking is baked into the loop's own structure, with no need for `continue`/`break` at all.

---

## 25. Variable Scope

**Definition:** The region of code within which a variable exists and can be referenced — typically the pair of curly braces `{ }` in which it was declared.

### The Bug That Demonstrates Scope

```c
while (true)
{
    int n = get_int("What's n? ");   // n declared INSIDE the loop
    ...
}
// n is used again down here — but it's OUT of scope! Compiler error:
// "use of undeclared identifier 'n'"
```

### The Fix — Declare the Variable _Outside_ the Loop

```c
int n;              // declared here, OUTSIDE the loop — now visible for the whole function
do
{
    n = get_int("What's n? ");   // no need to redeclare the type — just assign
}
while (n < 0);
```

> **Key rule:** Once you specify a variable's **type** (e.g., `int n;`), you're **creating** it in memory. After that, you only need to reference it **by name** (no type needed again) to reuse/update it — as long as you're still within its scope.

This same scope concept applies to **function parameters** (Section 26) — a variable declared/used only inside a function is not visible outside that function, and vice versa.

---

## 26. Custom Functions & Prototypes

### Motivation

Just like Scratch let you create your own custom "meow" block (abstracting a repeated action), C lets you define your **own functions** — reusable, named pieces of code.

### A Function With No Input, No Output (Just a Side Effect)

```c
void meow(void)
{
    printf("meow\n");
}
```

- First `void` = the function **returns nothing**.
- `(void)` = the function **takes no input/arguments**.

### Using It in `main`

```c
int main(void)
{
    for (int i = 0; i < 3; i++)
    {
        meow();
    }
}

void meow(void)
{
    printf("meow\n");
}
```

### The Problem: Compiler Reads Top-to-Bottom

If `meow`'s definition is placed **below** `main` in the file, the compiler will throw:

```
error: call to undeclared function 'meow'
```

> C compilers do **not** proactively scan the whole file first — they take the code **literally, top to bottom**. If `meow` isn't yet known by the time `main` tries to use it, that's an error.

### The Fix: Function Prototypes

```c
void meow(void);   // prototype — a "promise" to the compiler

int main(void)
{
    for (int i = 0; i < 3; i++)
    {
        meow();
    }
}

void meow(void)
{
    printf("meow\n");
}
```

> A **prototype** is just the function's first line (its "signature") followed by a semicolon, placed **above `main`**. It tells the compiler _"trust me, this function will be defined later in the file."_ This lets you keep `main` (the most important, "main" part of your program) visually at the **top** of the file, where it's easiest to find.

### Functions That Take Input (Parameters)

```c
void meow(int n);   // prototype

int main(void)
{
    int n = get_int("Meow how many times? ");
    meow(n);
}

void meow(int n)
{
    for (int i = 0; i < n; i++)
    {
        printf("meow\n");
    }
}
```

> **Naming note:** Parameter names inside a function don't have to match the variable name used when calling it — e.g., a parameter could be named `times` instead of `n` for clarity, since it represents "the number of times to meow."

### Functions That RETURN a Value

```c
int get_n(void)
{
    int n;
    do
    {
        n = get_int("What's n? ");
    }
    while (n < 0);
    return n;
}
```

- A function that returns something declares its return type accordingly (e.g., `int get_n(void)` instead of `void get_n(void)`).
- Inside the function, `return n;` sends that value back to wherever the function was called.
- Usage: `int n = get_n();` — treating the custom function just like `get_int()` or any built-in CS50 function.

### Comments

```c
// This is a comment — a note to yourself or a colleague, ignored by the compiler
```

> Anything after `//` on a line is a **comment** — purely for humans, like a Scratch "sticky note," and has no effect on the program's behavior.

---

## 27. Correctness, Design & Style

CS50 (and the software industry broadly) evaluates code quality along **three axes**:

| Axis            | Meaning                                                                                                                                               |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Correctness** | Does the code work exactly as it's supposed to? (e.g., matches the assignment's or the product manager's requirements)                                |
| **Design**      | Is the code implemented _well_ — not wasting computation/human time, avoiding redundancy, choosing efficient logic?                                   |
| **Style**       | Is the code aesthetically consistent — proper indentation, meaningful variable names (not arbitrary `x`, `y`, `z` everywhere), consistent formatting? |

> **Key distinction:** Correctness is largely objective (it either works or it doesn't); **design is more subjective** — a human (TA, section leader, or AI tool) typically evaluates this; **style** matters to _other humans_ reading the code, not to the computer itself.

---

## 28. CS50 Tools: `check50`, `design50`, `style50`

| Tool           | Purpose                                                                                                                                                     |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`check50`**  | Automatically tests whether your code is **correct**, using a unique identifier ("slug") for each assignment problem — gives quick pass/fail-style feedback |
| **`design50`** | Built on top of the "CS50 duck" (an AI assistant); provides ChatGPT-like feedback on the **design/quality** of your code (not just correctness)             |
| **`style50`**  | Shows a **side-by-side comparison** of your code's current style vs. how it _should_ look, per CS50's official style guide                                  |

> Just like real companies maintain an official style guide so all contributors' code looks consistent, CS50 enforces a house style — even if some choices don't match every individual programmer's personal preferences (which is realistic — companies do this too).

---

## 29. Practical: Building "Mario" (1D & 2D Loops)

_Inspired by classic Super Mario Bros. level obstacles — question-mark blocks and brick columns/rows._

### Step 1 — A Simple Hard-Coded Row (No Loop Needed Yet)

```c
#include <stdio.h>

int main(void)
{
    printf("????\n");
}
```

### Step 2 — Using a Loop for a Configurable-Length Row

**First (buggy) attempt** — putting `\n` _inside_ the loop produces a **column**, not a row:

```c
for (int i = 0; i < 4; i++)
{
    printf("?\n");   // BUG: newline printed after EVERY character
}
```

**Fixed version** — move the `\n` _outside_ the loop:

```c
for (int i = 0; i < 4; i++)
{
    printf("?");
}
printf("\n");
```

### Step 3 — A Vertical Column (Same Logic, Reused)

```c
for (int i = 0; i < 3; i++)
{
    printf("#\n");
}
```

### Step 4 — A 2D Grid (Nested Loops — Rows and Columns)

```c
for (int i = 0; i < 3; i++)        // outer loop = ROWS
{
    for (int j = 0; j < 3; j++)    // inner loop = COLUMNS
    {
        printf("#");
    }
    printf("\n");                   // move to next row AFTER finishing all columns
}
```

- **Nesting loops is fine**, but the inner and outer loop variables must have **different names** (e.g., `i` and `j`) to avoid collision.
- Using `i`/`j`/`k` for loop counters is conventional; anything beyond that (e.g., `l`, `m`, `o`) is usually a sign you should use more descriptive names.
- **Better naming for clarity:**
  ```c
  for (int row = 0; row < 3; row++)
  {
      for (int column = 0; column < 3; column++)
      {
          printf("#");
      }
      printf("\n");
  }
  ```

### Step 5 — Removing "Magic Numbers" (Duplicated `3`)

Hard-coding the number `3` in **two places** (outer and inner loop conditions) is flagged as **bad design** — if you want to resize the grid later, you'd need to remember to change it in both places (error-prone).

**Fix — use a single variable:**

```c
int n = 3;
for (int row = 0; row < n; row++)
{
    for (int column = 0; column < n; column++)
    {
        printf("#");
    }
    printf("\n");
}
```

---

## 30. Constants

To go one step further than a plain variable, C supports declaring a value as **`const`** (constant) — signaling that it should **never change**, and having the compiler **enforce** this (protecting you from accidentally reassigning it later).

```c
const int N = 3;
```

> If you later mistakenly try to write `N = 5;` elsewhere in your code, the **compiler will reject it** — a small but meaningful design improvement over a plain (mutable) variable.

---

## 31. Arithmetic Operators & Building a Calculator

### Arithmetic Operators

| Operator | Meaning            |
| -------- | ------------------ |
| `+`      | Addition           |
| `-`      | Subtraction        |
| `*`      | Multiplication     |
| `/`      | Division           |
| `%`      | Modulo (remainder) |

### Simple Addition Calculator

```c
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    int x = get_int("What's x? ");
    int y = get_int("What's y? ");
    printf("%i\n", x + y);
}
```

> **Design note:** Introducing a third variable (e.g., `int z = x + y;`) just to use it once, immediately after, is often unnecessary — it's fine (and can even improve readability) to inline the expression directly inside `printf`, especially for short expressions.

### Multiplication Example

```c
int x = get_int("What's x? ");
printf("%i\n", x * 2);
```

### Efficiency Tip — Autocomplete & Command History

- **Tab-completion:** typing a partial filename (e.g., `cal`) and pressing **Tab** auto-completes it if the file exists.
- **Up/Down arrow keys:** cycle through previously typed terminal commands, saving retyping effort.

### The "Doubling Meme" Program (Integer Overflow Demo)

```c
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    int dollars = 1;
    while (true)
    {
        char c = get_char("Here's $%i. Double it and give to next person? ", dollars);
        if (c == 'y')
        {
            dollars *= 2;
        }
        else
        {
            break;
        }
    }
}
```

> This program repeatedly doubles a dollar amount — eventually demonstrating **integer overflow** (see Section 32) when the value grows too large for the data type to hold.

---

## 32. Integer Overflow

**Definition:** When a numeric variable's value exceeds the maximum number that its allocated bits can represent, causing it to **"wrap around"** — often resulting in a negative number, zero, or otherwise nonsensical value.

### Why This Happens

- Computer memory allocates a **finite number of bits** per value (commonly 8, 16, 32, or 64).
- With `int` (typically 32 bits, signed), you can only count up to roughly **±2 billion**.
- **Analogy:** With only 3 physical "light bulb" digits, you can count 0 through 7 (using all 3 bits) — but to represent 8, you'd need a 4th bit. Without it, the count **wraps back to 0** (or, depending on representation, may appear as negative due to a leading "sign" bit).

### Demonstrated Live

Running the doubling program repeatedly (pressing "y" many times) eventually causes `dollars` to:

1. Go **negative** unexpectedly (due to wraparound).
2. Eventually **return to 0**.

### Attempted Fix — Use `long` Instead of `int`

```c
long dollars = 1;
// ... printf using %li instead of %i
```

- `long` uses **64 bits** instead of 32 — a _much_ larger range — but **still finite**. With enough repeated doublings (demonstrated live), even a `long` **eventually overflows too**.

> **Core lesson:** There is no data type with infinite capacity — every fixed-width integer type will eventually overflow given enough growth. Awareness of a program's expected value ranges is essential when choosing data types.

---

## 33. Real-World Overflow Bugs (Boeing 787, Pac-Man)

### Boeing 787 Dreamliner Bug

- Reported by the **New York Times**: A Boeing 787 that has been **continuously powered for 248 days** could **lose all AC electrical power**, due to its Generator Control Units (GCUs) simultaneously entering fail-safe mode.
- **Root cause:** An internal software counter would **overflow** after 248 days of continuous power.
- **Short-term fix (before Boeing's official software patch):** **Reboot the plane** — rebooting resets all variables to their default (often zero) values, temporarily resolving the issue.
- **Underlying math:** 248 days is roughly the number of **tenths of a second** representable within a signed 32-bit integer's positive range — once that counter overflowed, the power-related failure occurred.

### Pac-Man's Level 256 Bug

- A well-known historical bug: Pac-Man could technically be played up to **level 255**.
- Due to a **missing conditional check** for the current level, reaching **level 256** would cause an integer overflow, resulting in a **garbled/glitched screen** with random characters.
- Reaching this level (informally called the "kill screen") became something of a **badge of honor** among expert players, precisely _because_ of this bug.

---

## 34. Floating-Point Imprecision & Truncation

### Truncation (Using `int` for Division)

```c
int x = get_int("What's x? ");
int y = get_int("What's y? ");
printf("%i\n", x / y);
```

- Dividing `1 / 3` using **integers** produces `0` — because integer division **discards** (truncates) anything after the decimal point.
- Even `4 / 3` (which should be `1.33`) yields just `1` under integer division.

### Fixing With `float`

```c
float x = get_float("What's x? ");
float y = get_float("What's y? ");
printf("%f\n", x / y);
```

- Now `1 / 3` correctly returns a decimal value like `0.333333`.

### Floating-Point Imprecision

Using a more precise format specifier (e.g., `%.50f` to show 50 decimal digits) reveals that `1/3` is **not** represented as an infinite, perfectly repeating `0.333...` — instead, seemingly random digits appear after a certain point.

**Why:** Computers use a **finite number of bits** to represent floating-point numbers, but there are **infinitely many** real numbers — so most floating-point values are actually **approximations**, not exact representations.

> **No perfect solution** — using more bits (`double` instead of `float`) increases precision but does not eliminate the fundamental issue; some tolerance for approximation (or special detection/handling) is generally required in real-world floating-point calculations.

---

## 35. The Y2K Problem & The Year 2038 Problem

### Y2K (Year 2000) Problem

- For decades, computers commonly stored **years using only 2 digits** (instead of 4) to save memory (e.g., storing "99" instead of "1999").
- When the year rolled from 1999 to 2000, systems risked confusing **"2000" with "1900"**, since only the last two digits were stored.
- This caused widespread concern/preparation efforts around the world; ultimately, major catastrophic failures were largely avoided due to remediation work done in advance.

### The Year 2038 Problem (Upcoming)

- Since the 1970s, computers have commonly tracked time using a **32-bit integer counter** representing the number of **seconds since January 1, 1970** (the Unix epoch).
- A signed 32-bit integer can only count up to about **2 billion** seconds from that starting point.
- **On January 19, 2038**, this counter is expected to **overflow**.
- **Potential consequence:** Affected devices/computers may incorrectly believe the date is **December 13, 1901** (the wraparound point), unless the underlying systems are fixed beforehand (e.g., by migrating to 64-bit time representations).

> **Takeaway:** These aren't just historical curiosities — integer overflow is a **real, recurring, high-stakes problem** in computing (aviation systems, classic video games, global infrastructure), and CS50's goal is to equip students with the understanding needed to **recognize and avoid** these pitfalls in their own code.

---

## 36. Key Takeaways

- **Source code** (human-writable/readable) is translated by a **compiler** into **machine code** (zeros and ones) that computers actually execute.
- **VS Code** provides a combined **GUI + CLI** environment; the terminal (CLI) is where compiling (`make`) and running (`./program`) actually happen.
- Nearly every Scratch concept from Week 0 has a **direct C equivalent**: functions (`printf`, custom functions), variables (with explicit **types**), conditionals (`if`/`else if`/`else`), and loops (`while`, `for`, `do while`) — only the **syntax** differs, not the underlying logic.
- C requires extra precision that Scratch didn't: **double quotes** for strings, **single quotes** for chars, **semicolons** to end statements, **format specifiers** (`%s`, `%i`, `%c`, `%f`, `%li`) to interpolate variables into printed text, and explicit **variable types**.
- **Header files** (`stdio.h`, `cs50.h`) provide access to reusable **library** functions — always `#include` what you use.
- **Variable scope** matters: a variable only exists within the curly braces `{ }` where it was declared.
- **Function prototypes** let you declare a function's signature above `main`, while defining its actual body later in the file — keeping `main` conveniently at the top.
- Code should be judged on three axes: **correctness** (does it work?), **design** (is it efficient/non-redundant?), and **style** (is it readable/consistently formatted?) — CS50 provides `check50`, `design50`, and `style50` to help evaluate each.
- **Integer overflow** and **floating-point imprecision** are fundamental, unavoidable consequences of computers using a **finite number of bits** to represent numbers — with real-world consequences ranging from video game glitches to aircraft power failures to the looming **Year 2038 problem**.

---

_End of Week 1 Notes — Compiled from course transcription for academic/study purposes._
