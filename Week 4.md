# CS50 — Week 4: Memory, Hexadecimal, Pointers & File I/O

> **Course:** CS50 (Introduction to Computer Science)
> **Week:** 4
> **Topic:** Moving beyond the CS50 library to understand memory, pointers, and file input/output in C

---

## Table of Contents

1. [Introduction — How Images Are Stored](#1-introduction--how-images-are-stored)
2. [Hexadecimal Number System](#2-hexadecimal-number-system)
3. [Memory Addresses](#3-memory-addresses)
4. [Pointers — Core Concepts](#4-pointers--core-concepts)
5. [The Mailbox Metaphor](#5-the-mailbox-metaphor)
6. [Dereferencing Pointers](#6-dereferencing-pointers)
7. [Strings Are Actually Pointers](#7-strings-are-actually-pointers)
8. [Pointer Arithmetic](#8-pointer-arithmetic)
9. [Comparing Values: `==` vs `strcmp`](#9-comparing-values--vs-strcmp)
10. [Copying Strings — The Wrong Way and the Right Way](#10-copying-strings--the-wrong-way-and-the-right-way)
11. [`malloc` and `free`](#11-malloc-and-free)
12. [NULL and Defensive Programming](#12-null-and-defensive-programming)
13. [Valgrind — Detecting Memory Errors](#13-valgrind--detecting-memory-errors)
14. [Garbage Values](#14-garbage-values)
15. [The Binky Video — Pointer Fundamentals Recap](#15-the-binky-video--pointer-fundamentals-recap)
16. [Pass by Value vs Pass by Reference (The Swap Problem)](#16-pass-by-value-vs-pass-by-reference-the-swap-problem)
17. [Memory Layout: Stack and Heap](#17-memory-layout-stack-and-heap)
18. [Stack Overflow / Heap Overflow / Buffer Overflow](#18-stack-overflow--heap-overflow--buffer-overflow)
19. [Removing CS50 Library Training Wheels: `scanf`](#19-removing-cs50-library-training-wheels-scanf)
20. [File I/O in C](#20-file-io-in-c)
21. [Building a Phonebook Program with Persistent Storage](#21-building-a-phonebook-program-with-persistent-storage)
22. [Building a Byte-by-Byte File Copy Program (`cp`)](#22-building-a-byte-by-byte-file-copy-program-cp)
23. [Preview: Image Filters with BMP Files](#23-preview-image-filters-with-bmp-files)
24. [Key Terms Glossary](#24-key-terms-glossary)

---

## 1. Introduction — How Images Are Stored

- Class began with student-made Post-it note art (green and purple notes) forming a picture of a cat — a physical analogy for how computers store images using only **0s and 1s**.
- A digital image has a finite **resolution**: the number of dots horizontally × the number of dots vertically = total number of **pixels**. More pixels means more bytes (measured in KB, MB, GB, etc.).
- Zooming into any digital image eventually reveals individual **pixels** — the smallest unit of a digital image, each with its own color.
- **Simplified 1-bit example:** If `0` = black and `1` = white, a grid of 0s and 1s can encode a simple black-and-white image (e.g., a smiley face hidden in a grid of binary digits).
- Modern images use far more bits per pixel — **16-bit, 24-bit, or more per color** — to represent the full spectrum of colors, not just black and white.

### RGB Color Representation

- **RGB** = Red, Green, Blue — the standard way computers represent color.
- Common in image editors (e.g., Photoshop) and web design.
- Colors are represented using **hexadecimal notation**, e.g.:

| Color | Hex Code  | RGB (decimal)   |
| ----- | --------- | --------------- |
| Black | `#000000` | (0, 0, 0)       |
| White | `#FFFFFF` | (255, 255, 255) |
| Red   | `#FF0000` | (255, 0, 0)     |
| Green | `#00FF00` | (0, 255, 0)     |
| Blue  | `#0000FF` | (0, 0, 255)     |

- This raises the question: **why do we use hexadecimal instead of decimal for colors and memory?** → Answered in the next section.

---

## 2. Hexadecimal Number System

### Why Hexadecimal?

- Programmers use **hexadecimal (base 16)** as a convention for representing colors and memory addresses — it is not mathematically special, just a widely adopted convention.
- Number systems recap:
  - **Binary** (base 2): digits 0–1
  - **Decimal** (base 10): digits 0–9
  - **Hexadecimal** (base 16): digits 0–9, then **A, B, C, D, E, F** (representing 10–15)

### Place Values

- Decimal place values: 1s, 10s, 100s...
- Hexadecimal place values: **16⁰ = 1s column, 16¹ = 16s column, 16² = 256s column**, etc.

### Counting in Hex

```
00, 01, 02, 03, 04, 05, 06, 07, 08, 09,
0A, 0B, 0C, 0D, 0E, 0F,
10, 11, 12, 13, ... FF
```

- `0A` = 10, `0F` = 15
- `10` in hex = 16 in decimal (**not** "ten" — pronounced "one-zero" in hex)
- `FF` in hex = 255 in decimal (16×15 + 15 = 255)

### Why Hex Is Convenient

- **One hex digit represents exactly 4 bits.**
  - `F` = `1111` in binary = 15 in decimal
  - `FF` = 8 bits (1 byte) = 255 in decimal
- This makes hex a compact, human-readable way to represent a **byte** (8 bits) using just 2 hex characters — far easier than reading out 8 binary digits.

### The `0x` Prefix

- Hex numbers are conventionally written with a **`0x`** prefix (e.g., `0x10`) to avoid ambiguity with decimal numbers.
- Example: `0x10` = 16 in decimal, **not** ten.
- Memory addresses are typically written in this hexadecimal notation, e.g., `0x7FFFD3C34ECC`.

---

## 3. Memory Addresses

- Computer memory can be visualized as a grid/canvas of **bytes**, each with a numbered location — its **address**.
- Programmers conventionally number these addresses in **hexadecimal**, not decimal.
- Example program (`addresses.c`):

```c
#include <stdio.h>

int main(void)
{
    int n = 50;
    printf("%i\n", n);
}
```

- This declares an integer variable `n` and prints its **value** (50).

### Printing an Address

- To print the _address_ of a variable (not its value), C provides:
  - **`&` (ampersand)** — the "address-of" operator
  - **`%p`** — the format code for printing a pointer/address (used in `printf` instead of `%i`)

```c
#include <stdio.h>

int main(void)
{
    int n = 50;
    printf("%p\n", &n);
}
```

- Output resembles: `0x7FFFD3C34ECC` — a real memory address, shown in hexadecimal.

---

## 4. Pointers — Core Concepts

### Definition

> A **pointer** is a variable that stores a memory **address** — specifically, the address of another value in memory.

### Declaring a Pointer

```c
int n = 50;
int *p = &n;
```

- `&n` → gets the **address** of `n`.
- `int *p` → declares `p` as a variable that stores **the address of an integer** (a pointer to an `int`), not an integer itself.
- **Convention:** write the asterisk immediately next to the variable name with a space before it: `int *p;` (though `int* p;` or `int * p;` also compile).

### Pointer Size

- On most modern systems, a pointer is **8 bytes (64 bits)**, regardless of what type it points to.
- **Why 64 bits?** Because with only 32 bits, you could only address up to **4 billion** distinct memory locations (4 GB) — insufficient for modern computers with much more RAM. 64-bit addressing supports vastly larger memory spaces.

### Common Error

If you forget the asterisk:

```c
int p = &n;   // ERROR
```

Compiler error: **"incompatible pointer to integer conversion"** — you're trying to assign an address (pointer) to a plain integer variable.

---

## 5. The Mailbox Metaphor

- Think of computer memory as a huge wall of **mailboxes** (like an apartment complex), each with an address.
- A pointer variable (like `p`) is itself a mailbox — but instead of holding a "useful" value, it holds the **address of another mailbox**.
- Analogy: **P** is like a "foam finger" pointing at another value in memory (`n = 50`).
- When drawing diagrams, programmers typically **abstract away actual addresses** and just draw **arrows** from the pointer to the value it points to.

---

## 6. Dereferencing Pointers

- The `*` (asterisk) operator has **multiple meanings** in C depending on context:
  1. **Multiplication** (e.g., `a * b`)
  2. **Declaring a pointer type** (e.g., `int *p;`)
  3. **Dereferencing** — "go to the address stored in this variable and get/set the value there"

### Example

```c
#include <stdio.h>

int main(void)
{
    int n = 50;
    int *p = &n;
    printf("%i\n", *p);   // dereference p → prints 50
}
```

- `*p` means: "go to the address stored in `p`, and retrieve the value found there."

---

## 7. Strings Are Actually Pointers

### The "White Lie" of `string`

- In CS50's early weeks, the `string` data type was a simplification.
- **Truth:** In C, there is no native `string` type. CS50's library defines:

```c
typedef char *string;
```

- This means `string` is simply a **synonym** for `char *` — a **pointer to a character** (specifically, the address of the _first character_ of a sequence of characters in memory).

### Why This Works

- A string is stored as a sequence of characters back-to-back in memory, terminated by the **null character** (`\0`).
- The variable holding a string doesn't store the whole string — it stores the **address of the first character**.
- Example: `char *s = "HI!";` stores the address where `'H'` lives; `'I'`, `'!'`, and `'\0'` follow at consecutive addresses.

### Demonstration

```c
#include <stdio.h>

int main(void)
{
    char *s = "HI!";
    printf("%p\n", s);       // address of first character
    printf("%p\n", &s[0]);   // same address
    printf("%p\n", &s[1]);   // one byte later
    printf("%p\n", &s[2]);
    printf("%p\n", &s[3]);   // address of the null terminator
}
```

- `s` and `&s[0]` yield the **same address**, confirming that a string variable _is_ the address of its first character.

### `typedef` Explained

- `typedef` lets you create your own type synonyms.
- Example:

```c
typedef int integer;   // now "integer" means the same as "int"
```

- CS50's library uses this exact mechanism:

```c
typedef char *string;
```

### Removing the Training Wheel

```c
// Without CS50 library:
#include <stdio.h>

int main(void)
{
    char *s = "HI!";
    printf("%s\n", s);
}
```

- This behaves identically to using `string`, since `string` **is** `char *`.

---

## 8. Pointer Arithmetic

- Since addresses are just numbers, you can perform **arithmetic on pointers** (add, subtract — multiplication/division is unusual but technically possible).
- Array bracket notation `s[i]` is actually **syntactic sugar** for pointer arithmetic:

| Array Notation | Equivalent Pointer Arithmetic |
| -------------- | ----------------------------- |
| `s[0]`         | `*(s + 0)` or `*s`            |
| `s[1]`         | `*(s + 1)`                    |
| `s[2]`         | `*(s + 2)`                    |

### Example

```c
char *s = "HI!";
printf("%c\n", *s);        // 'H'
printf("%c\n", *(s + 1));  // 'I'
printf("%c\n", *(s + 2));  // '!'
```

- You can also print a _string_ starting at any offset:

```c
printf("%s\n", s);         // "HI!"
printf("%s\n", s + 1);     // "I!"
printf("%s\n", s + 2);     // "!"
```

Each still works correctly because all three "strings" happen to share the same **null terminator**.

---

## 9. Comparing Values: `==` vs `strcmp`

### Comparing Integers (Works Fine)

```c
int i = 50, j = 50;
if (i == j)
{
    printf("same\n");
}
```

- Integers are stored directly in their variables, so `==` correctly compares their values.

### Comparing Strings (Doesn't Work as Expected)

```c
char *s = get_string("s: ");
char *t = get_string("t: ");

if (s == t)   // BUG: compares addresses, not contents!
{
    printf("same\n");
}
else
{
    printf("different\n");
}
```

- Even if the user types the _same word_ for `s` and `t`, this will print `"different"`.
- **Why?** `s` and `t` are pointers — they store the **addresses** of two separate chunks of memory (each call to `get_string` allocates new memory). `s == t` compares addresses, not the actual characters.

### The Fix: `strcmp`

```c
#include <string.h>

if (strcmp(s, t) == 0)
{
    printf("same\n");
}
else
{
    printf("different\n");
}
```

- `strcmp` (string compare) walks through both strings **character by character** and returns `0` if they are identical.
- This is why CS50 introduced `strcmp` in earlier weeks — it solves exactly this problem.

---

## 10. Copying Strings — The Wrong Way and the Right Way

### The Bug: Shallow Copy

```c
#include <cs50.h>
#include <ctype.h>
#include <stdio.h>
#include <string.h>

int main(void)
{
    char *s = get_string("s: ");
    char *t = s;              // BUG: copies the address, not the string!
    t[0] = toupper(t[0]);
    printf("s: %s\n", s);
    printf("t: %s\n", t);
}
```

- **Problem:** `t = s` copies the **pointer** (the address), so `s` and `t` point to the **same memory**.
- Capitalizing `t[0]` also changes `s[0]`, because they refer to the identical bytes in memory.
- Typing `"hi"` results in **both** `s` and `t` printing `"Hi"` — an unintended side effect.

### The Fix: True Copy Using `malloc`

```c
#include <cs50.h>
#include <ctype.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main(void)
{
    char *s = get_string("s: ");

    char *t = malloc(strlen(s) + 1);   // allocate new memory (+1 for null terminator)

    for (int i = 0, n = strlen(s); i <= n; i++)
    {
        t[i] = s[i];   // manually copy each character, including '\0'
    }

    t[0] = toupper(t[0]);

    printf("s: %s\n", s);
    printf("t: %s\n", t);

    free(t);
}
```

- `malloc(strlen(s) + 1)` requests enough bytes for the string **plus** the null terminator.
- The loop copies characters **including** the null terminator (`i <= n`, not `i < n`).
- Now `s` and `t` point to **separate** memory locations, so modifying `t` does not affect `s`.

### Simplifying with `strcpy`

```c
char *t = malloc(strlen(s) + 1);
strcpy(t, s);   // destination first, then source
```

- `strcpy(destination, source)` copies a string automatically — no manual loop needed.
- **Order matters:** destination comes first, then source (easy to mix up).

---

## 11. `malloc` and `free`

| Function    | Purpose                                                                                                            |
| ----------- | ------------------------------------------------------------------------------------------------------------------ |
| `malloc(n)` | **M**emory **alloc**ation — requests `n` bytes from the operating system and returns the address of the first byte |
| `free(ptr)` | Releases previously `malloc`'d memory back to the operating system                                                 |

### Example

```c
#include <stdlib.h>

int *x = malloc(3 * sizeof(int));  // space for 3 integers
x[0] = 72;
x[1] = 73;
x[2] = 33;
free(x);
```

- **`sizeof(type)`** dynamically asks the compiler/system how many bytes a data type occupies (e.g., `sizeof(int)` is typically 4). This is more **portable** than hardcoding `4`.

### Rule of Thumb

> **"If you `malloc`'d it, you must `free` it."**

- **Exception:** `get_string()` from the CS50 library automatically frees its own memory internally — you do **not** need to call `free()` on strings obtained via `get_string`.
- Forgetting to `free` memory you `malloc`'d causes a **memory leak** — the program continues to hold onto memory it no longer needs, and over time (especially in long-running programs like servers) this can slow down or crash a system.

---

## 12. NULL and Defensive Programming

- **`NULL`** is a special reserved memory address (`0x0`) that is intentionally never used for real data. It signals: **"nothing here" / "an error occurred."**
- Distinguish:
  - **`NUL`** (`\0`): the null-terminating character used to mark the end of a string (1 byte, all zero bits).
  - **`NULL`**: the special pointer/address value `0x0`.

### Why This Matters

- Functions like `get_string()` and `malloc()` **can fail** (e.g., if the system runs out of memory) and will return `NULL` to indicate failure.
- **Always check for `NULL` before using a pointer:**

```c
char *s = get_string("s: ");
if (s == NULL)
{
    return 1;   // abort — something went wrong
}

char *t = malloc(strlen(s) + 1);
if (t == NULL)
{
    return 1;   // malloc failed
}
```

### Edge Case: Empty Strings

```c
if (strlen(s) > 0)
{
    t[0] = toupper(t[0]);   // only safe to touch t[0] if the string isn't empty
}
```

- If the user types nothing, `strlen(s)` is `0`, and `t[0]` would actually be the null terminator — attempting to uppercase it is unsafe/meaningless.

---

## 13. Valgrind — Detecting Memory Errors

- **Valgrind** is a general-purpose (not CS50-specific) command-line tool for detecting **memory-related bugs**: invalid reads/writes, memory leaks, use of uninitialized memory, etc.
- Usage:

```bash
valgrind ./program_name
```

### Example Bug (Off-by-One + Memory Leak)

```c
#include <stdlib.h>

int main(void)
{
    int *x = malloc(3 * sizeof(int));
    x[1] = 72;
    x[2] = 73;
    x[3] = 33;   // BUG: valid indices are only 0, 1, 2 — index 3 is out of bounds
    // BUG: never called free(x) — memory leak
}
```

### Reading Valgrind Output

- `"Invalid write of size 4"` → the program wrote to memory it does not own (e.g., writing 4 bytes to an out-of-bounds index).
- `"definitely lost: 12 bytes in 1 blocks"` → a memory leak — 12 bytes (3 ints × 4 bytes) were `malloc`'d but never `free`'d.
- Valgrind output includes line-number references pointing to the exact source of the bug.

### Fixed Version

```c
#include <stdlib.h>

int main(void)
{
    int *x = malloc(3 * sizeof(int));
    if (x == NULL)
    {
        return 1;
    }
    x[0] = 72;
    x[1] = 73;
    x[2] = 33;
    free(x);
    return 0;
}
```

- Running Valgrind again shows: `"0 errors"` and no leaked bytes.

---

## 14. Garbage Values

- **Garbage values** are leftover, undefined values that exist in memory because it was never explicitly initialized by the programmer.
- Example:

```c
int scores[10024];

for (int i = 0; i < 10024; i++)
{
    printf("%i\n", scores[i]);   // prints garbage: random leftover values
}
```

- Since none of the elements were assigned values, printing them reveals unpredictable "garbage" — remnants of whatever was previously stored in those memory locations.
- **Lesson:** never read from memory you haven't explicitly initialized.

---

## 15. The Binky Video — Pointer Fundamentals Recap

A classic Stanford claymation video (by Nick Parlante) was shown, reinforcing core pointer vocabulary:

- **Pointer:** a variable that can store an address (initially "points to nothing").
- **Pointee:** the actual value/data that a pointer points to; must be separately **allocated**.
- **Dereferencing** ("the magic wand"): follows the pointer's arrow to access/modify its pointee.
- **Danger:** dereferencing an **uninitialized pointer** (one that was never assigned a valid pointee) is undefined behavior and can crash the program — illustrated when Binky tries to dereference pointer `y` before it was ever set to point anywhere valid.
- **Fix:** assign `y = x;` — this makes `y` point to the **same pointee** as `x` (pointer assignment doesn't touch the pointees themselves, just redirects the pointer).
- After `y = x`, dereferencing either pointer accesses the **same shared memory**.

### Code Equivalent

```c
int *x, *y;

x = malloc(sizeof(int));
*x = 42;

*y = 13;   // BUG: y was never assigned a valid pointee — undefined behavior!
```

Fixed:

```c
y = x;     // now y points to the same pointee as x
*y = 13;   // valid — safely modifies the shared pointee
```

---

## 16. Pass by Value vs Pass by Reference (The Swap Problem)

### Live Demo: The Glass-Swapping Analogy

- A volunteer was given two glasses of colored liquid (blue and orange) and asked to **swap their contents without mixing them** — this is only possible using a **third, temporary container** (analogous to a temporary variable).

### The Buggy Swap (Pass by Value)

```c
#include <stdio.h>

void swap(int a, int b);

int main(void)
{
    int x = 1;
    int y = 2;

    printf("x is %i, y is %i\n", x, y);
    swap(x, y);
    printf("x is %i, y is %i\n", x, y);   // UNCHANGED — bug!
}

void swap(int a, int b)
{
    int temp = a;
    a = b;
    b = temp;
}
```

- **Output shows `x` and `y` unchanged** after calling `swap`.
- **Why?** In C, arguments are passed **by value** — the function receives **copies** of `x` and `y` (stored in `a` and `b`, which exist in a _different scope_, i.e., a different memory frame). Swapping `a` and `b` has no effect on the original `x` and `y`.

### The Fix: Pass by Reference (Using Pointers)

```c
#include <stdio.h>

void swap(int *a, int *b);

int main(void)
{
    int x = 1;
    int y = 2;

    printf("x is %i, y is %i\n", x, y);
    swap(&x, &y);
    printf("x is %i, y is %i\n", x, y);   // correctly swapped!
}

void swap(int *a, int *b)
{
    int temp = *a;
    *a = *b;
    *b = temp;
}
```

- Now `a` and `b` store the **addresses** of `x` and `y` (a "treasure map"), not copies of their values.
- `*a` and `*b` dereference these addresses to read/write the **actual original variables**.
- This is the first construct in the course capable of truly swapping two variables' values through a function call.

---

## 17. Memory Layout: Stack and Heap

A computer's RAM (during program execution) is conceptually organized (from "top" to "bottom") as:

```
┌─────────────────────────────┐
│   Machine Code               │  ← compiled 0s and 1s of the program itself
├─────────────────────────────┤
│   Global Variables            │  ← variables declared outside main/functions
├─────────────────────────────┤
│                              │
│   Heap                      │  ← grows downward; used by malloc()
│                              │
├─────────────────────────────┤
│           ↑ (free space)     │
├─────────────────────────────┤
│                              │
│   Stack                     │  ← grows upward; used for function calls & local vars
│                              │
└─────────────────────────────┘
```

- **Machine code**: loaded at the top of memory when a program launches (copied from disk/SSD into RAM).
- **Global variables**: variables declared outside of any function; live just below machine code.
- **Heap**: memory dynamically allocated via `malloc()`. Successive `malloc` calls typically claim contiguous chunks, growing "downward."
- **Stack**: used for **function calls** and their **local variables**. Each function call gets its own **frame** of memory.
  - When `main()` calls `swap()`, `swap` gets a new frame **above** `main`'s frame (conceptually, growing "upward," like stacking cafeteria trays).
  - When a function returns, its frame's memory becomes reusable — explaining why **garbage values** appear: leftover bytes from previous function calls.

### Example Frame Walkthrough (Swap, Fixed Version)

1. `main`'s frame: `x = 1`, `y = 2`.
2. `swap` is called with `&x` and `&y` → `swap`'s frame: `a` = address of `x`, `b` = address of `y`, `temp` (uninitialized).
3. `temp = *a` → `temp` gets `1` (the value at `x`'s address).
4. `*a = *b` → the value at `x`'s address becomes `2`.
5. `*b = temp` → the value at `y`'s address becomes `1`.
6. When `swap` returns, its frame is discarded, but `x` and `y` in `main`'s frame have genuinely been swapped.

---

## 18. Stack Overflow / Heap Overflow / Buffer Overflow

- The **heap** (growing "down") and the **stack** (growing "up") occupy the same finite pool of memory — if a program uses too much of either, they can **collide**.
- **Stack overflow**: happens when too many function calls stack up without returning (e.g., infinite/unbounded recursion with no base case), exhausting available stack memory.
- **Heap overflow**: happens when too much memory is requested via `malloc` without being freed.
- These are the origin of the term used by the popular website **Stack Overflow**.
- **Buffer overflow**: a broader term for writing more data into a fixed-size chunk of memory (like an array) than it can actually hold — a very common source of software bugs and security vulnerabilities.

### Practical Guidance

- Only `malloc` as much memory as you actually need.
- Ensure recursive functions have proper base cases so they terminate.
- Be cautious with fixed-size buffers when reading unpredictable-length user input (see next section).

---

## 19. Removing CS50 Library Training Wheels: `scanf`

### Getting an Integer Without `get_int`

```c
#include <stdio.h>

int main(void)
{
    int n;
    printf("n: ");
    scanf("%i", &n);      // must pass the ADDRESS of n
    printf("n: %i\n", n);
}
```

- `scanf` (scan formatted input) reads input from the keyboard and needs the **address** of the variable to write into — hence `&n`.
- This mirrors the swap problem: to let a function _modify_ a variable, you must give it a pointer to that variable.

### The Trouble with Getting a String Without `get_string`

```c
#include <stdio.h>

int main(void)
{
    char *s;
    printf("s: ");
    scanf("%s", s);        // BUG: s was never allocated any memory!
    printf("s: %s\n", s);
}
```

- **Bug:** `s` is declared but never assigned a valid memory address — it holds a **garbage value**.
- `scanf("%s", s)` (note: **no** `&` before `s`, since `s` is already a pointer) tries to write the typed characters to whatever bogus address `s` happens to hold — this is **undefined behavior** and can corrupt memory or crash.
- Confirmed via Valgrind: `"use of uninitialized value"` errors.

### Possible Fixes

1. Allocate a fixed-size buffer:

```c
char s[4];   // only safe for very short strings (e.g., "hi" + null terminator)
```

2. Use `malloc` to size the buffer appropriately — but you must guess a maximum length in advance, which is fundamentally limiting.
3. **Better solution:** Use `get_string()` from the CS50 library, which internally calls `malloc` repeatedly (growing dynamically) as the user types, handling arbitrarily long input safely — this is why removing this particular training wheel is much harder than removing `get_int`.

> **Key takeaway:** Handling strings safely and dynamically in raw C is nontrivial. Higher-level languages (like Python, introduced in upcoming weeks) abstract this complexity away.

---

## 20. File I/O in C

- A **file** is a sequence of bytes stored on **persistent storage** — a hard disk drive (HDD, magnetic platter) or solid-state drive (SSD, no moving parts).
- Contrast with **RAM** (random access memory): **volatile** — its contents are lost when power is lost.
- Until now, no program in the course could **persist data** between runs — everything lived only in RAM and vanished when the program ended.

### Key File I/O Functions (from `<stdio.h>`)

| Function    | Purpose                                           |
| ----------- | ------------------------------------------------- |
| `fopen()`   | Opens a file (for reading, writing, or appending) |
| `fclose()`  | Closes a file, ensuring data is properly saved    |
| `fprintf()` | Prints formatted output to a file                 |
| `fscanf()`  | Reads formatted input from a file                 |
| `fread()`   | Reads raw bytes from a file                       |
| `fwrite()`  | Writes raw bytes to a file                        |

### File Modes

| Mode  | Meaning                                                      |
| ----- | ------------------------------------------------------------ |
| `"r"` | Read                                                         |
| `"w"` | Write (overwrites/truncates the file each time it's opened!) |
| `"a"` | Append (adds to the end of the existing file)                |

---

## 21. Building a Phonebook Program with Persistent Storage

### Initial Version

```c
#include <cs50.h>
#include <stdio.h>
#include <string.h>

int main(void)
{
    FILE *file = fopen("phonebook.csv", "w");

    char *name = get_string("Name: ");
    char *number = get_string("Number: ");

    fprintf(file, "%s,%s\n", name, number);

    fclose(file);
}
```

- `FILE *file` — `fopen` returns a **pointer** to a `FILE` struct representing the opened file.
- `fprintf(file, ...)` — like `printf`, but writes to the specified file instead of the screen.
- **CSV** = Comma-Separated Values — a lightweight, spreadsheet-like text format (importable into Excel, Google Sheets, Apple Numbers, etc.).

### The `"w"` Mode Bug

- Opening in `"w"` mode **truncates (erases)** the file's existing contents every time the program runs — meaning only the **most recent entry** survives, not a growing phonebook.

### The Fix: Append Mode

```c
FILE *file = fopen("phonebook.csv", "a");
```

- `"a"` (append) mode adds new entries to the end of the file **without erasing** previous contents.

### Defensive Version (Checking for `NULL`)

```c
#include <cs50.h>
#include <stdio.h>
#include <string.h>

int main(void)
{
    FILE *file = fopen("phonebook.csv", "a");
    if (file == NULL)
    {
        return 1;
    }

    char *name = get_string("Name: ");
    char *number = get_string("Number: ");

    fprintf(file, "%s,%s\n", name, number);

    fclose(file);
    return 0;
}
```

- Always check whether `fopen` succeeded (it returns `NULL` on failure, e.g., insufficient disk space).

> **Why pointers are essential here:** `fopen` returns a pointer to a data structure representing the open file. Without understanding pointers, file I/O syntax (`FILE *file`) would be unexplainable — this is why file I/O is introduced only after covering memory and pointers.

---

## 22. Building a Byte-by-Byte File Copy Program (`cp`)

### Motivation: Buffering

- A **buffer** is a finite chunk of memory (often an array) used to temporarily hold data — e.g., video streaming apps show a "buffering" progress bar while downloading chunks of video data ahead of playback.
- The custom `cp` (copy) program mimics this idea, copying a file **one byte at a time** from a source file to a destination file.

### Implementation

```c
#include <stdio.h>

typedef unsigned char byte;   // define "byte" as an 8-bit unsigned value

int main(int argc, char *argv[])
{
    FILE *src = fopen(argv[1], "r");
    FILE *dst = fopen(argv[2], "w");

    byte b;

    while (fread(&b, sizeof(byte), 1, src) != 0)
    {
        fwrite(&b, sizeof(byte), 1, dst);
    }

    fclose(dst);
    fclose(src);
}
```

### Key Details

- `int argc, char *argv[]` — command-line arguments; `argv[1]` and `argv[2]` are the source and destination filenames the user types after the program name.
- `typedef unsigned char byte;` — creates a custom type alias `byte`, defined as an **unsigned char** (a `char` guaranteed to only represent non-negative values, since we're treating it as raw data, not signed numeric text).
- `fread(&b, sizeof(byte), 1, src)` — reads **1** element of size `sizeof(byte)` from `src` into the address of `b`; returns the number of items successfully read (used as the loop condition — `0` means end-of-file or error).
- `fwrite(&b, sizeof(byte), 1, dst)` — writes that one byte to the destination file.
- **Binary mode note:** For files known to be binary (e.g., images), it's good practice to open them with `"rb"` / `"wb"` to avoid any text-mode translation issues.

### Testing

```bash
make cp
./cp phonebook.csv copy.csv
cat copy.csv
```

- Produces a byte-for-byte identical copy of the original file.

---

## 23. Preview: Image Filters with BMP Files

- Upcoming problem sets will involve **BMP (bitmap) files** — images represented as a grid of pixels, each with **R, G, B** color values, stored top-to-bottom, left-to-right.
- Using the file I/O and pointer techniques from this lecture, students will be able to implement classic image-processing filters, including:
  - **Grayscale** — reducing R, G, B values to equal, simplified tones.
  - **Sepia** — applying an old-photograph color tint.
  - **Reflection** — flipping the image horizontally (mirroring pixel order).
  - **Blur** — averaging neighboring pixel values to soften the image.
  - **Edge detection** — highlighting boundaries between distinct regions (e.g., a bridge against the sky) by detecting sharp changes in pixel values.

---

## 24. Key Terms Glossary

| Term                   | Definition                                                                                            |
| ---------------------- | ----------------------------------------------------------------------------------------------------- |
| **Pixel**              | The smallest unit of a digital image; has a specific color value                                      |
| **RGB**                | Red-Green-Blue color model used to represent colors digitally                                         |
| **Hexadecimal (hex)**  | Base-16 number system using digits 0–9 and A–F; commonly used for colors and memory addresses         |
| **`0x` prefix**        | Standard notation indicating a number is written in hexadecimal                                       |
| **Address**            | A specific location in computer memory, usually written in hex                                        |
| **Pointer**            | A variable that stores a memory address                                                               |
| **Pointee**            | The value/data that a pointer points to                                                               |
| **Dereference (`*`)**  | Accessing/modifying the value stored at the address a pointer holds                                   |
| **Address-of (`&`)**   | Operator that retrieves the memory address of a variable                                              |
| **`NULL`**             | Special address (`0x0`) representing "no valid address" / error                                       |
| **`NUL` (`\0`)**       | The null character marking the end of a string                                                        |
| **`malloc()`**         | Requests a block of memory from the operating system dynamically                                      |
| **`free()`**           | Releases dynamically allocated memory back to the operating system                                    |
| **Memory leak**        | Memory allocated with `malloc` that is never `free`'d                                                 |
| **Garbage value**      | Leftover, uninitialized data found in memory                                                          |
| **Pass by value**      | Function arguments are copied; changes inside the function don't affect the original variables        |
| **Pass by reference**  | Passing a pointer/address so a function can directly modify the original variable                     |
| **Stack**              | Memory region used for function calls and local variables; grows with each function call              |
| **Heap**               | Memory region used for dynamically allocated memory (via `malloc`); grows as more memory is requested |
| **Stack overflow**     | Running out of stack memory, often due to excessive/infinite function calls                           |
| **Buffer overflow**    | Writing more data into a fixed-size memory block than it can hold                                     |
| **Valgrind**           | A tool for detecting memory errors (leaks, invalid reads/writes, uninitialized memory)                |
| **`FILE *`**           | A pointer to a structure representing an open file                                                    |
| **`fopen`/`fclose`**   | Functions to open and close files                                                                     |
| **`fprintf`/`fscanf`** | Formatted file writing/reading functions                                                              |
| **`fread`/`fwrite`**   | Raw byte-level file reading/writing functions                                                         |
| **CSV**                | Comma-Separated Values — simple, spreadsheet-compatible text file format                              |
| **Buffer**             | A finite chunk of memory (often an array) used to temporarily hold data (e.g., during streaming)      |
| **BMP**                | Bitmap image file format; a grid of pixel data                                                        |

---

## Summary

This lecture moved beyond CS50's protective library abstractions to reveal how C actually manages **memory**, introducing:

1. **Hexadecimal notation**, the conventional way to represent memory addresses and colors.
2. **Pointers**, which store memory addresses and enable indirect access/modification of data.
3. The revelation that **`string` is just `char *`** underneath the hood.
4. **Pointer arithmetic** as the mechanism underlying array syntax.
5. Why comparing/copying strings requires special functions (`strcmp`, `strcpy`) rather than naive `==` or `=`.
6. **Dynamic memory management** (`malloc`/`free`) and the dangers of memory leaks and invalid memory access.
7. Tools like **Valgrind** for catching memory bugs.
8. The **stack vs. heap** memory model explaining scope, function calls, and garbage values.
9. **Pass by reference**, solving problems (like swapping values) that were previously impossible.
10. **File I/O**, enabling programs to finally persist data beyond a single execution — setting up for image-processing problem sets involving BMP files.
