# CS50 — Week 3: Algorithms, Searching, Sorting & Recursion

### Lecture Notes — David J. Malan

---

## 📑 Table of Contents

1. [Introduction & Attendance-Taking Demo](#1-introduction--attendance-taking-demo)
2. [Analyzing Algorithm Growth Rates (Informal)](#2-analyzing-algorithm-growth-rates-informal)
3. [Searching: The Locker Demonstration](#3-searching-the-locker-demonstration)
   - 3.1 [Setup and Volunteers](#31-setup-and-volunteers)
   - 3.2 [Linear Search — Jose's Approach](#32-linear-search--joses-approach)
   - 3.3 [Binary Search — Caitlin's Approach](#33-binary-search--caitlins-approach)
4. [Pseudocode for Searching](#4-pseudocode-for-searching)
   - 4.1 [Linear Search Pseudocode](#41-linear-search-pseudocode)
   - 4.2 [Binary Search Pseudocode](#42-binary-search-pseudocode)
5. [Big O, Omega, and Theta Notation](#5-big-o-omega-and-theta-notation)
   - 5.1 [Why We Use Asymptotic Notation](#51-why-we-use-asymptotic-notation)
   - 5.2 [Big O (Upper Bound)](#52-big-o-upper-bound)
   - 5.3 [Omega (Lower Bound)](#53-omega-lower-bound)
   - 5.4 [Theta (Tight Bound)](#54-theta-tight-bound)
   - 5.5 [Running Times of Linear and Binary Search](#55-running-times-of-linear-and-binary-search)
   - 5.6 [When Is Sorting Worth the Cost?](#56-when-is-sorting-worth-the-cost)
6. [Implementing Search in C](#6-implementing-search-in-c)
   - 6.1 [Linear Search on Integers](#61-linear-search-on-integers)
   - 6.2 [Linear Search on Strings — the `strcmp` Pitfall](#62-linear-search-on-strings--the-strcmp-pitfall)
   - 6.3 [Building a Phone Book with Parallel Arrays](#63-building-a-phone-book-with-parallel-arrays)
7. [Custom Data Types: `struct`](#7-custom-data-types-struct)
   - 7.1 [The Problem with Parallel Arrays](#71-the-problem-with-parallel-arrays)
   - 7.2 [Defining and Using a `struct`](#72-defining-and-using-a-struct)
8. [Sorting: The Pez Dispenser Demonstration](#8-sorting-the-pez-dispenser-demonstration)
   - 8.1 [Setup and Volunteers](#81-setup-and-volunteers)
   - 8.2 [Selection Sort — Acted Out](#82-selection-sort--acted-out)
   - 8.3 [Bubble Sort — Acted Out](#83-bubble-sort--acted-out)
9. [Pseudocode and Analysis: Selection Sort](#9-pseudocode-and-analysis-selection-sort)
   - 9.1 [Pseudocode](#91-pseudocode)
   - 9.2 [Running Time Derivation](#92-running-time-derivation)
10. [Pseudocode and Analysis: Bubble Sort](#10-pseudocode-and-analysis-bubble-sort)
    - 10.1 [Pseudocode](#101-pseudocode)
    - 10.2 [Running Time Derivation](#102-running-time-derivation)
    - 10.3 [Optimization: Early Termination](#103-optimization-early-termination)
    - 10.4 [Visualization Comparison](#104-visualization-comparison)
11. [Recursion](#11-recursion)
    - 11.1 [What Is Recursion?](#111-what-is-recursion)
    - 11.2 [Recursive Binary Search Revisited](#112-recursive-binary-search-revisited)
    - 11.3 [Base Cases vs. Recursive Cases](#113-base-cases-vs-recursive-cases)
    - 11.4 [Real-World Example: Mario Pyramid](#114-real-world-example-mario-pyramid)
12. [Implementing the Mario Pyramid: Iterative vs. Recursive](#12-implementing-the-mario-pyramid-iterative-vs-recursive)
    - 12.1 [Iterative Version](#121-iterative-version)
    - 12.2 [Recursive Version](#122-recursive-version)
    - 12.3 [Missing Base Case Compiler Error](#123-missing-base-case-compiler-error)
    - 12.4 [Memory Limits of Deep Recursion](#124-memory-limits-of-deep-recursion)
13. [Merge Sort](#13-merge-sort)
    - 13.1 [Pseudocode](#131-pseudocode)
    - 13.2 [Merging Two Sorted Halves](#132-merging-two-sorted-halves)
    - 13.3 [Full Walkthrough on 8 Numbers](#133-full-walkthrough-on-8-numbers)
    - 13.4 [Running Time Analysis: O(n log n)](#134-running-time-analysis-on-log-n)
    - 13.5 [Space/Time Trade-off](#135-spacetime-trade-off)
14. [Visualization Comparison: Selection vs. Bubble vs. Merge Sort](#14-visualization-comparison-selection-vs-bubble-vs-merge-sort)
15. [Key Terms Glossary](#15-key-terms-glossary)

---

## 1. Introduction & Attendance-Taking Demo

The lecture opens by connecting **algorithms** (step-by-step instructions for solving a problem) introduced conceptually in Week 0, to concrete implementations. The day's two central topics: **searching** and **sorting**.

**Live demonstration — three ways to take attendance in the lecture hall:**

1. **Counting by 1s** — pointing and reciting "1, 2, 3, 4..." for every person.
2. **Counting by 2s** — faster, but still fundamentally similar in nature.
3. **Divide-and-conquer counting (audience participation):**
   - Everyone stands and mentally holds the number **1**.
   - Pair off with a neighbor, sum your two numbers, then **one person in each pair sits down** holding the combined total.
   - Repeat: still-standing people pair off again, sum, one sits — looping until very few people remain standing.
   - The instructor manually finishes summing the last few standing participants.
   - **Result:** The audience-computed total (141) closely matched (but wasn't perfectly identical to) a manual one-by-one count (~160) — the discrepancy attributed to **human execution error** ("bugs") in the mental arithmetic, not a flaw in the algorithm itself.

**Connection to Week 0:** This divide-and-conquer approach mirrors the earlier **phone book search** example — repeatedly cutting the problem in half is dramatically faster than processing it linearly, especially as the problem size grows.

---

## 2. Analyzing Algorithm Growth Rates (Informal)

Using the three attendance algorithms as a reference point, the lecture introduces an intuitive, graphical way to compare algorithm performance **before** formalizing it mathematically:

| Algorithm                    | Growth Pattern                         | Description                                                                                  |
| ---------------------------- | -------------------------------------- | -------------------------------------------------------------------------------------------- |
| Count by 1s                  | **Linear** (straight line, slope 1)    | Time grows in direct 1:1 proportion to room size                                             |
| Count by 2s                  | **Linear**, but with a shallower slope | Still a straight line, but consistently faster — takes half as many steps                    |
| Divide-and-conquer (pairing) | **Logarithmic** (flattens out)         | Doubling the room size adds only **one more step**; quadrupling adds only **two more steps** |

**Key intuitive takeaway:** As problem size (**N**) grows very large, the divide-and-conquer approach's time cost grows **far more slowly** than either linear approach — this same shape/pattern will recur throughout the lecture (binary search, merge sort) and is the central reason computer scientists care deeply about _how_ an algorithm's cost scales, not just whether it works.

---

## 3. Searching: The Locker Demonstration

### 3.1 Setup and Volunteers

**Setup:** Seven physical lockers on stage, each containing a hidden Monopoly-style bill of a different denomination. The goal: find the **$50 bill**.

- **Input:** 7 closed lockers (all values hidden).
- **Output:** A boolean — true (found) or false (not found).

**Key constraint emphasized:** A computer (and by extension, code) can only examine **one memory location at a time** — like opening one locker door, looking inside, then closing it before moving to the next. There's no "bird's-eye view" of all values simultaneously, even though a human observer in the room can visually spot the answer instantly.

**Indexing convention reiterated:** Lockers are numbered starting from **0** (not 1) — locker 6 implies there are 7 total lockers (0 through 6).

### 3.2 Linear Search — Jose's Approach

**Volunteer Jose** was asked to find the $50 bill by starting from the **leftmost locker** and checking each one in order, left to right.

**Outcome:** The $50 bill happened to be in the **last** locker checked — requiring him to open **all seven** lockers before succeeding.

**Key discussion points:**

- Starting from the right instead of the left wouldn't fundamentally change the algorithm — it might get lucky (find it faster) or unlucky (find it slower), but the _approach_ (checking sequentially) is the same.
- **Random-order checking** doesn't help either, if nothing is known in advance about how the values are arranged — and it introduces the added overhead of needing to **track which lockers have already been checked**.
- **Conclusion:** Without prior information about the data's arrangement (e.g., sorted order), simple sequential ("linear") checking is essentially the best available strategy.

### 3.3 Binary Search — Caitlin's Approach

**Volunteer Caitlin** was given an **advantage**: the numbers had been pre-sorted from smallest to largest, left to right, by a colleague (Kelly).

**Strategy:** Start in the **middle** locker, then repeatedly narrow the search to the middle of whichever half remains — a direct application of the divide-and-conquer principle.

**Outcome:** Caitlin found the $50 bill in just a few steps by successively halving the search space (middle → middle of remaining half → etc.), rather than checking every locker sequentially.

**Important caveat raised in the lecture:** Because both volunteers likely had some intuition about typical Monopoly money denominations (from having just discussed them), the demonstration wasn't a perfectly "blind" test — but the underlying algorithmic principle (successively halving a _sorted_ search space) remains valid and generalizable.

---

## 4. Pseudocode for Searching

### 4.1 Linear Search Pseudocode

**Plain-English version:**

```
For each door, from left to right:
    if 50 is behind the door:
        return true
return false
```

**Critical correctness detail — indentation/control-flow matters:**

- An **incorrect** version using `if`/`else` would return `false` immediately after checking just the _first_ door if it didn't contain 50 — never checking the remaining six doors. This is flagged as a serious logic bug: once a function `return`s a value, execution stops immediately, so an early, unconditional `else return false` would abort the search prematurely.
- The **correct** version places the `return false` as a final, un-indented statement _after_ the entire loop completes — only reached if every door was checked and none contained the target.

**More formal (array-notation) version:**

```
for i from 0 to n − 1:
    if 50 is behind doors[i]:
        return true
return false
```

- **Why `n − 1`, not `n`?** Since indexing starts at 0, an array of `n` total elements has valid indices `0` through `n − 1`. Looping "through `n`" would imply `n + 1` total elements — one too many.

### 4.2 Binary Search Pseudocode

**Plain-English version, with an added safety check:**

```
If there are no doors left to search:
    return false
If 50 is behind the middle door:
    return true
Else if 50 is less than the value behind the middle door:
    search the left half
Else if 50 is greater than the value behind the middle door:
    search the right half
```

**Formalized (array-notation) version:**

```
if 50 is behind doors[middle]:
    return true
else if 50 < doors[middle]:
    search doors[0] through doors[middle − 1]
else if 50 > doors[middle]:
    search doors[middle + 1] through doors[n − 1]
```

- **Computing the middle index:** `middle = n / 2`, using **integer division** (which rounds down) — e.g., for 7 doors, `7 / 2 = 3.5`, which truncates to index `3` (the true middle of a 7-element, 0-indexed array).
- **Why exclude the middle index itself from each half?** Since the middle door was _already checked_ in the current step, re-including it in either the "left half" or "right half" search would waste a redundant comparison.

---

## 5. Big O, Omega, and Theta Notation

### 5.1 Why We Use Asymptotic Notation

Rather than precisely counting every individual operation an algorithm performs, computer scientists use **broad-strokes notation** to communicate, in a standardized way, how an algorithm's cost **scales** as the problem size (**N**) grows — especially useful since modern computers are fast enough that differences barely matter at small scale, but become very noticeable at large scale (thousands, millions, or billions of elements).

**Simplification principle:** Only the **dominant (fastest-growing) term** in a mathematical expression matters for this notation — lower-order terms and constant multipliers are dropped, since as N gets arbitrarily large, they contribute negligibly to the overall growth trend. (E.g., `N/2` and `N` are treated as equivalently "linear" for these purposes.)

**The term "asymptotic"** refers to describing how a value behaves as it grows arbitrarily large, without necessarily ever reaching some fixed boundary.

### 5.2 Big O (Upper Bound)

- **Big O notation**, written `O(...)`, describes an algorithm's **upper bound** — essentially, its **worst-case** performance.
- Common examples introduced: `O(1)` (constant), `O(log n)`, `O(n)`, `O(n log n)`, `O(n²)`.

### 5.3 Omega (Lower Bound)

- **Omega notation**, written `Ω(...)`, describes an algorithm's **lower bound** — its **best-case** performance (how few steps it might take if especially lucky).

### 5.4 Theta (Tight Bound)

- **Theta notation**, written `Θ(...)`, is used **only when Big O and Omega are identical** — i.e., when best case and worst case share the same growth rate. This conveys more precise information in a single term, rather than needing to state both bounds separately.

### 5.5 Running Times of Linear and Binary Search

| Algorithm         | Big O (worst case) | Omega (best case)                                                 | Theta                      |
| ----------------- | ------------------ | ----------------------------------------------------------------- | -------------------------- |
| **Linear search** | O(n)               | Ω(1) _(target could be the very first element checked)_           | _(not equal, so no Theta)_ |
| **Binary search** | O(log n)           | Ω(1) _(target could be exactly the first middle element checked)_ | _(not equal, so no Theta)_ |

**Key comparative insight:** Binary search's _upper bound_ (`O(log n)`) is dramatically better than linear search's (`O(n)`) as N grows large — but binary search **requires the data to be sorted in advance**, which linear search does not.

**Correctness warning:** Using binary search on an **unsorted** array is not just less efficient — it is **outright incorrect**, since the algorithm's left/right decisions rely entirely on the (false) assumption that smaller values are reliably positioned on one side and larger values on the other.

### 5.6 When Is Sorting Worth the Cost?

The lecture explicitly frames this as a **cost/benefit trade-off decision**, not an automatic "always sort first" rule:

- If data will be searched **many times** (e.g., a search engine like Google, queried repeatedly by many users), the one-time cost of sorting can be **amortized** across many subsequent fast searches — making the upfront investment worthwhile.
- If data only needs to be searched **once**, sorting first may not be worth the extra effort — a simple (even unsorted, linear, or "hopeful" random) search might be more practical overall.
- **Anecdote shared:** In graduate school research, the lecturer sometimes deliberately wrote **simpler but less efficient** ("correct but not optimal") code and let it run overnight, rather than spending extra time optimizing — accepting the risk that an undiscovered bug could waste the entire overnight run if something went wrong.

**General framing:** Today's lesson isn't that one approach is always superior, but that engineers should make these efficiency trade-offs **knowingly**, based on actual usage patterns and constraints (time, memory, money, effort).

---

## 6. Implementing Search in C

### 6.1 Linear Search on Integers

```c
#include <cs50.h>
#include <stdio.h>

int main(void)
{
    int numbers[] = {20, 500, 10, 5, 100, 1, 50};

    int n = get_int("Number: ");

    for (int i = 0; i < 7; i++)
    {
        if (numbers[i] == n)
        {
            printf("Found\n");
            return 0;
        }
    }
    printf("Not Found\n");
    return 1;
}
```

- **Static array initialization** with curly braces (`{20, 500, ...}`) lets the compiler automatically infer the array's size — explicitly specifying the count (e.g., `numbers[7]`) is optional and, per the lecture, arguably riskier, since a manual count could be miscounted relative to the actual number of listed values.
- **Return values as exit status:** `return 0` signals success (found); `return 1` signals failure (not found) — consistent with the exit-status convention introduced in Week 2.

### 6.2 Linear Search on Strings — the `strcmp` Pitfall

**First (buggy) attempt — using `==` to compare strings:**

```c
if (strings[i] == s)   // INCORRECT for comparing string contents
```

- This compiles, but **fails to correctly match strings** even when their visible text is identical — searches for "battleship," "boot," and "top hat" all incorrectly report "Not Found."

**Why `==` fails for strings (but works fine for integers):**

- Comparing two integers is straightforward — either their (single) values match or they don't.
- Comparing two strings requires checking **potentially many characters**, one by one, since strings can be of different lengths and contain different character sequences at each position — `==` does not perform this character-by-character comparison automatically (a deeper explanation of _why_ is deferred to a later week on pointers/memory).

**Correct approach — using `strcmp` from `string.h`:**

```c
#include <string.h>
...
if (strcmp(strings[i], s) == 0)   // 0 means the strings are equal
```

**`strcmp` documentation behavior:**

- Returns **`0`** if the two strings are exactly equal.
- Returns a **negative** or **positive** number otherwise — indicating which string would come first in dictionary-style ("ASCII-betical," based on character codes rather than true linguistic alphabetization) order.
- **Why return more than just true/false?** This richer return value is what makes `strcmp` also useful for **sorting** strings later on (by comparing relative order), not just checking for equality.

**Common compile error encountered:** Forgetting `#include <string.h>` produces an "undeclared library function" error for `strcmp`, since the compiler has no prototype for it without that header.

### 6.3 Building a Phone Book with Parallel Arrays

**Initial approach — two separate, same-length arrays kept in sync "by convention":**

```c
string names[] = {"Kelly", "David", "John Harvard"};
string numbers[] = {"+16174951000", "+16174951000", "+19494682750"};
```

**Why phone numbers are stored as `string`, not `int`:**

- Real-world phone numbers often include non-digit characters (`+`, `-`, parentheses).
- **Leading zeros** (common in international dialing prefixes) would be silently dropped if stored as an actual integer, since leading zeros have no mathematical significance in numeric types — but they must be **preserved** as literal characters in a phone number.

**Search logic:**

```c
string name = get_string("Name: ");
for (int i = 0; i < 3; i++)
{
    if (strcmp(names[i], name) == 0)
    {
        printf("%s\n", numbers[i]);
        return 0;
    }
}
printf("Not Found\n");
return 1;
```

**Design flaw explicitly identified:** This approach relies entirely on the **"honor system"** — trusting that `names[i]` and `numbers[i]` always correspond to the _same_ person at _every_ index, for _every_ future update. This becomes increasingly fragile and error-prone as the dataset grows (3 people is manageable; 3 million is not) — motivating the introduction of a proper combined data structure next.

---

## 7. Custom Data Types: `struct`

### 7.1 The Problem with Parallel Arrays

C's built-in types (`int`, `float`, `char`, `bool`, `string`, etc.) don't include anything like a "Person" type that bundles a name and phone number together. Without such a mechanism, developers are stuck either:

- Creating many individual variables (`name1`, `number1`, `name2`, `number2`, ...) — already rejected as poor design in Week 2, or
- Using **parallel arrays** (as above) — which relies on error-prone, unenforced alignment between separate arrays.

### 7.2 Defining and Using a `struct`

**Syntax for defining a custom composite type:**

```c
typedef struct
{
    string name;
    string number;
}
person;
```

- **`typedef struct { ... } TypeName;`** defines a new custom data type (`person` in this case) that bundles multiple related fields together.
- **Style convention (per the course's style guide):** the new type's name is placed on the same line as the closing curly brace (`} person;`), even though this may look unconventional.

**Using the new type — an array of `person` structs:**

```c
person people[3];

people[0].name = "Kelly";
people[0].number = "+16174951000";

people[1].name = "David";
people[1].number = "+16174951000";

people[2].name = "John Harvard";
people[2].number = "+19494682750";
```

- **Dot notation** (`.`) is used to access a specific field within a struct instance (e.g., `people[0].name`).

**Updated search logic, now operating on structs:**

```c
string name = get_string("Name: ");
for (int i = 0; i < 3; i++)
{
    if (strcmp(people[i].name, name) == 0)
    {
        printf("%s\n", people[i].number);
        return 0;
    }
}
printf("Not Found\n");
return 1;
```

**Design benefit emphasized:** Each person's name and number are now **encapsulated together** in a single struct instance — eliminating the risk of the two pieces of data becoming misaligned across separate arrays, since they now travel together as one unit at each array index.

---

## 8. Sorting: The Pez Dispenser Demonstration

### 8.1 Setup and Volunteers

**Setup:** Eight student volunteers, each holding a numbered Mario-themed Pez dispenser (0 through 7), initially arranged **out of order**: `7, 2, 5, 4, 1, 6, 0, 3`.

**Unscripted/organic sort:** When first asked to simply "sort yourselves from smallest to largest," the volunteers quickly self-organized correctly — but the instructor notes this **ad hoc, parallel human approach** doesn't translate well to a step-by-step, sequential computer algorithm, and likely wouldn't scale efficiently to hundreds or thousands of elements.

### 8.2 Selection Sort — Acted Out

**Approach demonstrated:** Repeatedly scan the _entire remaining unsorted portion_ of the list to find its **smallest** element, then swap it into its correct position.

**Step-by-step summary of the demonstration:**

1. Scan all 8 volunteers left to right, tracking (in the instructor's own memory) the smallest value seen so far — ultimately finding `0` (held by "Jayden").
2. **Swap** Jayden (value 0) into the leftmost position, displacing whoever was previously standing there ("Precious") into Jayden's old spot.
3. Repeat the _entire scanning process_ again — this time considering only the remaining unsorted positions — to find the next-smallest value (`1`), swap it into position 2, and so on.
4. Continue until all 8 positions are correctly filled.

**Key implementation detail emphasized:** Because this operates on a **fixed-size array**, elements can't simply be "shifted over" to make room — displaced elements must be explicitly **swapped** into the vacated position, since arbitrary/unallocated memory can't be assumed to be safely usable.

**Memory/step trade-off noted:** The instructor didn't need to memorize the entire list's arrangement — only a single "current smallest value found so far" variable was needed on each pass, at the cost of needing to **re-scan** the remaining list on every subsequent pass.

### 8.3 Bubble Sort — Acted Out

**Approach demonstrated:** Repeatedly compare **only adjacent pairs** of elements, swapping any pair found out of order, sweeping left to right across the list.

**Step-by-step summary:**

1. Compare positions 0–1: swap if out of order. Then compare 1–2, then 2–3, and so on across the entire list.
2. After one full left-to-right sweep, the **single largest** element will have "bubbled" all the way to the correct final (rightmost) position — hence the algorithm's name.
3. Repeat the sweep (ignoring the now-confirmed-correct rightmost element each time) until the entire list is sorted.

**Observed pattern:** Each successive pass fixes one additional element's correct final position (largest, then second-largest, etc.) — visually and conceptually distinct from selection sort's "hunt for the global smallest each time" approach, though both ultimately require a similar number of total comparisons in the general case.

---

## 9. Pseudocode and Analysis: Selection Sort

### 9.1 Pseudocode

```
for i from 0 to n − 1:
    find the smallest number between numbers[i] and numbers[n − 1]
    swap that smallest number with numbers[i]
```

### 9.2 Running Time Derivation

**Step-counting logic:**

- On the first pass, finding the smallest among `n` total elements requires comparing each candidate against the current best — effectively **`n − 1`** comparisons.
- After placing the first element correctly, the second pass only needs to consider the remaining `n − 1` elements, requiring **`n − 2`** comparisons — and so on, down to a final single comparison.

**Total steps:**

```
(n − 1) + (n − 2) + (n − 3) + ... + 1
```

This is a well-known arithmetic series, equal to:

```
n(n − 1) / 2  =  (n² − n) / 2  =  n²/2 − n/2
```

**Applying Big O simplification:** As `n` grows large, the **`n²`** term dominates over the linear `n` term and constant divisor — so selection sort is classified as:

- **Big O: O(n²)**

**Lower bound (Omega) analysis:**

- Critically, the pseudocode above makes **no special provision** for detecting an already-sorted list — it unconditionally performs the same nested scanning process regardless of the input's actual starting order.
- Therefore, even in the **best-case scenario** (already-sorted input), selection sort as written still performs the same total number of comparisons.
- **Omega: Ω(n²)**

**Since Big O and Omega are identical:**

- **Theta: Θ(n²)** — selection sort's running time is **the same in both best and worst cases**, making it a "safe" but uniformly slow algorithm for large inputs.

---

## 10. Pseudocode and Analysis: Bubble Sort

### 10.1 Pseudocode

```
repeat n times:
    for i from 0 to n − 2:
        if numbers[i] and numbers[i + 1] are out of order:
            swap them
```

- **Why `n − 2`, not `n − 1`, for the inner loop's upper bound?** The comparison inside the loop checks `numbers[i]` **and** `numbers[i + 1]` — so if `i` were allowed to reach `n − 1`, the comparison would attempt to access `numbers[n]`, which is **out of bounds** for an array indexed `0` through `n − 1`. Capping `i` at `n − 2` ensures `i + 1` never exceeds the array's valid range.

### 10.2 Running Time Derivation

**Step-counting logic (from the pseudocode structure directly):**

- Outer loop: runs **`n`** times (or `n − 1`, as a minor optimization — see below).
- Inner loop: runs approximately **`n − 1`** times per outer iteration.
- Each inner iteration performs a **constant** number of operations (one comparison, and possibly one swap) — constant-time work doesn't affect the overall asymptotic growth rate.

**Total: roughly `n × (n − 1)` ≈ `n² − n`**, which simplifies under Big O to:

- **Big O: O(n²)** — the same worst-case classification as selection sort.

### 10.3 Optimization: Early Termination

**Key enhancement over the "naive" pseudocode:** If a complete left-to-right pass through the list results in **zero swaps**, the list must already be fully sorted — so the algorithm can **terminate immediately**, without performing any further redundant passes.

**Impact on best-case (Omega) analysis:**

- If the input is **already sorted**, exactly **one** full pass (making no swaps) is sufficient to confirm this and exit.
- **Omega: Ω(n)** — a meaningful improvement over selection sort's unconditional Ω(n²).

**Theta is not applicable:** Since Big O (`O(n²)`) and Omega (`Ω(n)`) differ, bubble sort **cannot** be described using Theta notation — its best-case and worst-case performance differ substantially, unlike selection sort's uniformly bad performance.

### 10.4 Visualization Comparison

Using an animated bar-chart visualization tool, the lecture visually contrasts:

- **Selection sort:** Repeated full scans to find each successive minimum — visibly "walking" back and forth across the remaining unsorted portion on each pass.
- **Bubble sort:** Adjacent pairwise comparisons sweeping left to right — visibly "bubbling" the largest remaining element toward its correct position on each pass.

**Shared takeaway:** Both algorithms are visibly slow and involve a very large number of repeated comparisons, especially apparent even with just a few dozen bars — motivating the search for a fundamentally faster approach (merge sort, covered next).

---

## 11. Recursion

### 11.1 What Is Recursion?

**Definition:** A **recursive function** is one that **calls itself** — analogous to a mathematical function defined partly in terms of itself (e.g., `f(x) = f(...)` appearing on the right-hand side of its own definition).

**Initial concern raised:** If a function calls itself, doesn't that risk an **infinite loop**? The resolution: each recursive call must be given a **progressively smaller version of the problem**, ensuring eventual termination — paired with a **base case** that stops the recursion once the problem becomes trivially small.

### 11.2 Recursive Binary Search Revisited

The binary search pseudocode from [Section 4.2](#42-binary-search-pseudocode) is revisited explicitly as an example of recursion **already used earlier in the lecture** (and conceptually, back in Week 0's phone book example):

```
if there are no doors left:
    return false
if 50 is behind the middle door:
    return true
else if 50 < doors[middle]:
    search doors[0] through doors[middle − 1]     ← recursive call, smaller problem
else if 50 > doors[middle]:
    search doors[middle + 1] through doors[n − 1]  ← recursive call, smaller problem
```

- The lines **"search the left half"** and **"search the right half"** are literally instructions to **re-run the same algorithm** on a strictly smaller subset of the data — the defining characteristic of recursion.
- **Connection back to Week 0:** The original phone-book search pseudocode used explicit "go back to line 3/8" loop-style instructions (a more mechanical, procedural style); this can be **rewritten more elegantly** by collapsing those explicit jump instructions into simple "search the left/right half" recursive calls instead — achieving the same result with a different (recursive) formulation.

### 11.3 Base Cases vs. Recursive Cases

| Concept            | Description                                                                                                                     |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------- |
| **Base case**      | A condition with an immediate, direct answer requiring no further self-calls (e.g., "if there are no doors left, return false") |
| **Recursive case** | A condition requiring the function to call itself again, but with a **smaller** version of the original problem                 |

**Critical requirement for correctness:** Every recursive call must operate on a **strictly smaller** problem than the current call — otherwise, the recursion never terminates (an infinite loop/call chain).

### 11.4 Real-World Example: Mario Pyramid

**Recursive definition of a height-4 pyramid (from Super Mario Bros.):**

- A pyramid of height 4 = a pyramid of height 3 **plus one more row**.
- A pyramid of height 3 = a pyramid of height 2 **plus one more row**.
- A pyramid of height 2 = a pyramid of height 1 **plus one more row**.
- A pyramid of height 1 = **a single brick** _(the base case — no further recursive reference)_.

**Lighthearted aside — Google's "recursion" search Easter egg:** Searching Google for the word "recursion" triggers a joke "Did you mean: recursion?" suggestion that loops back to the same search results page — a well-known in-joke among computer scientists.

---

## 12. Implementing the Mario Pyramid: Iterative vs. Recursive

### 12.1 Iterative Version

```c
#include <cs50.h>
#include <stdio.h>

void draw(int n);

int main(void)
{
    int height = get_int("Height: ");
    draw(height);
}

void draw(int n)
{
    for (int i = 0; i < n; i++)
    {
        for (int j = 0; j < i + 1; j++)
        {
            printf("#");
        }
        printf("\n");
    }
}
```

- **Outer loop** (`i`): iterates once per row of the pyramid.
- **Inner loop** (`j`): prints `i + 1` hashes per row — ensuring row 0 (the first row) prints exactly **1** brick, row 1 prints **2**, and so on.
- **Function prototype requirement:** Since `main` is placed first (a style convention) but calls `draw`, which is defined _below_ `main`, a **prototype** for `draw` must be added above `main` so the compiler recognizes it in advance — again described as essentially the _only_ generally-endorsed case for reasonable copy-pasting of code.

### 12.2 Recursive Version

**Reasoning translated directly into code:** _"A pyramid of height N is a pyramid of height N−1, plus one more row."_

```c
void draw(int n)
{
    draw(n - 1);              // draw a pyramid of height n−1 first
    for (int i = 0; i < n; i++)
    {
        printf("#");
    }
    printf("\n");
}
```

- This produces the smaller pyramid **first** (via the recursive call), then appends **one additional row** using a single, non-nested loop.

### 12.3 Missing Base Case Compiler Error

**Attempting to compile the above as-is produces a genuine compiler error:**

```
error: all paths through this function will call itself
```

- **Clang refuses to compile** the function as written, since — without any conditional check — it would call itself unconditionally and indefinitely.

**Fix — adding a base case:**

```c
void draw(int n)
{
    if (n <= 0)
    {
        return;     // no value returned, since the function's return type is void
    }
    draw(n - 1);
    for (int i = 0; i < n; i++)
    {
        printf("#");
    }
    printf("\n");
}
```

- **Why `n <= 0` rather than just `n == 0`?** Using `<= 0` defensively guards against being passed a **negative** number (an edge case), rather than assuming the input will always cleanly reach exactly zero.
- With this base case in place, the recursive calls (`n`, `n−1`, `n−2`, ... down to `0`) will eventually stop, since the function simply returns without recursing further once `n` reaches 0 or below.

**Simplification observation:** With the base case's `if` body reduced to a single statement, the surrounding curly braces become optional in C (single-statement conditionals don't strictly require braces) — allowing for an even more visually compact final version.

### 12.4 Memory Limits of Deep Recursion

**Live demonstration of a failure case:** Supplying an extremely large height value (many digits) to the recursive version causes a **crash/error** not seen with the iterative version.

**Explanation offered (teased for the following week's deeper coverage):** Each recursive call consumes **additional memory** on the call stack; since a computer's memory is finite, sufficiently deep recursion can **exhaust available memory**, causing the program to fail — a real trade-off between the _elegance_ of a recursive solution and its _memory_ cost, compared to the iterative version's flat, constant memory usage regardless of how large the loop count grows.

---

## 13. Merge Sort

### 13.1 Pseudocode

```
if only one number:
    quit (already sorted)
else:
    sort the left half of the numbers
    sort the right half of the numbers
    merge the sorted halves
```

- **Base case:** A list containing only **one element** is trivially already sorted — no work needed.
- **Recursive cases:** Sorting the left half and sorting the right half are each themselves full invocations of the same merge sort algorithm, applied to progressively smaller sublists.
- **The "magic" step:** Merging two _already-sorted_ halves together — explained in detail next.

### 13.2 Merging Two Sorted Halves

**Demonstrated mechanism:** Given two already-sorted lists (e.g., `[0, 1, 3, 6]` and `[2, 4, 5, 7]`), repeatedly compare the **current leftmost unclaimed element** of each list, and move the smaller of the two into the final merged (sorted) output — advancing only the pointer for whichever list contributed that element.

**Key efficiency insight:** Unlike selection sort or bubble sort, this merging process involves **no backtracking** — each element in both input lists is examined and moved **exactly once**. For two lists totaling `n` elements combined, merging takes exactly **`n` steps**.

### 13.3 Full Walkthrough on 8 Numbers

**Starting list:** `6, 3, 4, 1, 5, 2, 7, 0`

**Step-by-step recursive breakdown (abbreviated):**

1. **Sort the left half** `[6, 3, 4, 1]`:
   - Sort its left half `[6, 3]` → split into `[6]` and `[3]` (both base cases) → merge → `[3, 6]`.
   - Sort its right half `[4, 1]` → split into `[4]` and `[1]` (base cases) → merge → `[1, 4]`.
   - Merge `[3, 6]` and `[1, 4]` → `[1, 3, 4, 6]`.
2. **Sort the right half** `[5, 2, 7, 0]`:
   - Sort its left half `[5, 2]` → merge → `[2, 5]`.
   - Sort its right half `[7, 0]` → merge → `[0, 7]`.
   - Merge `[2, 5]` and `[0, 7]` → `[0, 2, 5, 7]`.
3. **Merge the two fully-sorted halves** `[1, 3, 4, 6]` and `[0, 2, 5, 7]` → final result: `[0, 1, 2, 3, 4, 5, 6, 7]`.

**Observation emphasized:** Unlike the earlier sorting demonstrations (selection sort, bubble sort), this process involved **no repeated back-and-forth re-comparison of the same elements** — work was cleanly divided across a small number of distinct "conceptual levels" (splitting, then merging upward), rather than sprawling across many repetitive linear passes.

### 13.4 Running Time Analysis: O(n log n)

**Key structural observation:** For a list of size `n = 8`, the merging process occurred across exactly **3 distinct levels** of recursive depth (each level merging progressively larger sorted sublists back together) — and at **each level**, the total work done (across all merges happening at that level) sums to **`n`** total element-comparisons.

**Why 3 levels for n = 8?** This relationship is precisely `log₂(n)`, since `log₂(8) = log₂(2³) = 3` — repeatedly halving 8 elements takes exactly 3 divisions to reach single elements.

**Total work = (number of levels) × (work per level) = `log₂(n) × n`**, conventionally written as:

- **Big O: O(n log n)**

**Lower bound (Omega) analysis:**

- Unlike bubble sort, this pseudocode includes **no special-case optimization** for detecting already-sorted input — every invocation unconditionally performs the full split-and-merge process regardless of the input's actual starting order.
- **Omega: Ω(n log n)**

**Since Big O and Omega are identical:**

- **Theta: Θ(n log n)** — merge sort's performance is **consistent** across best and worst cases, and critically, **asymptotically much faster** than either selection sort or bubble sort's `Θ(n²)` / `O(n²)` performance as `n` grows large.

### 13.5 Space/Time Trade-off

**Explicit cost acknowledged:** Unlike selection sort and bubble sort (which sort elements largely "in place," within the original array), merge sort's merging step requires **additional temporary storage** — effectively needing roughly **twice as much memory** at points during execution (to hold both the original sublists and the newly merged result) before consolidating back down.

**Framing:** This is presented as a deliberate **trade-off** between **time efficiency** (dramatically better: `O(n log n)` vs. `O(n²)`) and **space efficiency** (worse: extra memory required) — reinforcing a theme that will recur throughout the course: algorithmic improvements often come at some resource cost elsewhere, and engineers must weigh these trade-offs deliberately.

---

## 14. Visualization Comparison: Selection vs. Bubble vs. Merge Sort

The lecture closes with a side-by-side animated visualization (bar-chart style) simultaneously running:

- **Selection sort** (top)
- **Merge sort** (middle)
- **Bubble sort** (bottom)

...all operating on the same randomized dataset, at the same underlying computer speed.

**Visual/qualitative takeaway:** Merge sort visibly and dramatically outpaces both selection sort and bubble sort, converging to a fully sorted arrangement in a small fraction of the time — a direct, intuitive illustration of the practical real-world difference between **O(n²)** and **O(n log n)** growth rates as problem size increases.

---

## 15. Key Terms Glossary

| Term                    | Definition                                                                                                                                                                           |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Algorithm**           | Step-by-step instructions for solving a problem                                                                                                                                      |
| **Linear search**       | Searching a list sequentially, one element at a time, from one end to the other                                                                                                      |
| **Binary search**       | Searching a **sorted** list by repeatedly checking the middle element and discarding half the remaining search space based on a less-than/greater-than comparison                    |
| **Big O notation**      | Describes an algorithm's **upper bound** (worst-case) running time as problem size grows                                                                                             |
| **Omega notation**      | Describes an algorithm's **lower bound** (best-case) running time                                                                                                                    |
| **Theta notation**      | Used when Big O and Omega are identical, indicating consistent best/worst-case performance                                                                                           |
| **Asymptotic notation** | Notation (Big O, Omega, Theta) describing how a value/cost grows as the problem size grows arbitrarily large                                                                         |
| **`strcmp`**            | Standard C library function (`string.h`) that compares two strings, returning 0 if equal, and a negative/positive value indicating relative order otherwise                          |
| **Parallel arrays**     | Two or more separate arrays whose corresponding indices are (by convention only, not by any language enforcement) assumed to represent related data                                  |
| **`struct`**            | A custom, programmer-defined composite data type bundling multiple related fields together                                                                                           |
| **`typedef`**           | C keyword used (often alongside `struct`) to define a new named data type                                                                                                            |
| **Selection sort**      | Sorting algorithm that repeatedly finds the smallest remaining element and swaps it into its correct position; O(n²) / Ω(n²) / Θ(n²)                                                 |
| **Bubble sort**         | Sorting algorithm that repeatedly compares and swaps adjacent out-of-order pairs, causing larger elements to "bubble" toward the end; O(n²), improved to Ω(n) with early termination |
| **Merge sort**          | Recursive, divide-and-conquer sorting algorithm that sorts two halves independently, then merges the sorted halves; O(n log n) / Ω(n log n) / Θ(n log n)                             |
| **Recursion**           | A technique where a function calls itself, operating on a progressively smaller version of the original problem                                                                      |
| **Base case**           | The condition in a recursive function that requires no further self-calls, providing an immediate answer                                                                             |
| **Recursive case**      | The condition in a recursive function requiring a further, smaller self-call to eventually reach an answer                                                                           |
| **Iteration**           | Solving a problem using loops, as an alternative to recursion                                                                                                                        |
| **Divide and conquer**  | A problem-solving strategy that repeatedly breaks a problem into smaller subproblems, solves those independently, and combines their results                                         |

---

## 📌 Overarching Lessons of the Lecture

1. **Algorithm design matters as much as correctness.** Multiple algorithms can correctly solve the same problem, but their _efficiency_ — how their cost scales as input size grows — can differ dramatically (e.g., O(n²) vs. O(n log n)).
2. **Asymptotic notation (Big O, Omega, Theta) is a communication tool**, not a precise measurement — it lets engineers reason about and compare algorithms at a high level, focused on what matters most as problems scale up.
3. **Sorting is a worthwhile investment only in certain contexts.** The cost of sorting data must be weighed against how often that sorted data will subsequently be searched or used — a recurring engineering trade-off, not a universal rule.
4. **Recursion offers an elegant alternative to iteration**, often mirroring how problems are naturally described in plain language ("sort the left half, sort the right half, merge") — but requires a well-defined base case to guarantee termination, and can carry real memory costs at scale.
5. **Custom data structures (`struct`) solve real design problems.** Bundling related data together (rather than relying on parallel arrays and an "honor system" of consistent indexing) produces more robust, maintainable code.
6. **Efficiency gains often come with trade-offs.** Merge sort's dramatically better time complexity comes at the cost of additional memory — reinforcing that "better" algorithms usually involve deliberate trade-offs between competing resources (time, space, code complexity), not a free win across every dimension.

---

_End of Week 3 notes._
