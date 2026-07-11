# CS50 — Week 5: Data Structures

> **Course:** CS50 (Introduction to Computer Science)
> **Week:** 5
> **Topic:** Abstract data types, arrays, linked lists, trees, hash tables, and tries — the last week focused on C before transitioning to Python

---

## Table of Contents

1. [Introduction — Trade-offs and the Transition Ahead](#1-introduction--trade-offs-and-the-transition-ahead)
2. [Abstract Data Types: Queues and Stacks](#2-abstract-data-types-queues-and-stacks)
3. [Implementing a Queue in C](#3-implementing-a-queue-in-c)
4. [Implementing a Stack in C](#4-implementing-a-stack-in-c)
5. [Dictionaries as an Abstract Data Type](#5-dictionaries-as-an-abstract-data-type)
6. [Arrays Revisited — Static Allocation Problems](#6-arrays-revisited--static-allocation-problems)
7. [Dynamically Resizing Arrays with `malloc`](#7-dynamically-resizing-arrays-with-malloc)
8. [Using `realloc`](#8-using-realloc)
9. [New Syntax: The Arrow Operator (`->`)](#9-new-syntax-the-arrow-operator--)
10. [Linked Lists — Concept and Motivation](#10-linked-lists--concept-and-motivation)
11. [Defining a Node in C](#11-defining-a-node-in-c)
12. [Building a Linked List Step by Step](#12-building-a-linked-list-step-by-step)
13. [Traversing and Printing a Linked List](#13-traversing-and-printing-a-linked-list)
14. [Running Time of Linked List Operations](#14-running-time-of-linked-list-operations)
15. [Inserting in Sorted Order — Four Scenarios](#15-inserting-in-sorted-order--four-scenarios)
16. [Freeing a Linked List](#16-freeing-a-linked-list)
17. [Recap: Arrays vs Linked Lists](#17-recap-arrays-vs-linked-lists)
18. [Trees and Binary Search Trees](#18-trees-and-binary-search-trees)
19. [Implementing a Binary Search Tree in C](#19-implementing-a-binary-search-tree-in-c)
20. [The Balance Problem](#20-the-balance-problem)
21. [The Quest for Constant Time — Hashing](#21-the-quest-for-constant-time--hashing)
22. [Hash Functions](#22-hash-functions)
23. [Hash Tables](#23-hash-tables)
24. [Collisions and Chaining](#24-collisions-and-chaining)
25. [Tries — Achieving True Constant Time](#25-tries--achieving-true-constant-time)
26. [Trade-offs Summary Table](#26-trade-offs-summary-table)
27. [Key Terms Glossary](#27-key-terms-glossary)

---

## 1. Introduction — Trade-offs and the Transition Ahead

- This is the **last week focused on C** — next week the course transitions to **Python**, a higher-level language that makes solving problems much faster for humans to write, though not necessarily faster for the computer to execute.
- The overarching theme for this week (and next) is **trade-offs** — particularly trade-offs between **time** (speed) and **space** (memory usage).
- Focus of the week: **data structures** — ways of organizing data in memory to implement useful, real-world abstractions such as stacks, queues, and dictionaries.

---

## 2. Abstract Data Types: Queues and Stacks

### The "Jack and Lou" Story (Animation)

- A short animated story (by Professor Shannon Duvall, Elon University) illustrated the difference between two ways of organizing belongings:
  - **Jack** kept his clothes in a **box/pile** — always grabbing from the top, meaning older items at the bottom were rarely used (this behavior mirrors a **stack**).
  - **Lou** suggested using a **line/queue** in a closet — placing new clothes on one side and always retrieving from the opposite end, ensuring everything gets used before being reused (this behavior mirrors a **queue**).

### Abstract Data Types (ADTs)

> An **abstract data type** defines _what_ a data structure does (its behavior/interface) — independent of _how_ it is actually implemented in memory.

### Queue

- **FIFO** — **F**irst **I**n, **F**irst **O**ut.
- Real-world analogy: lining up at a store — the first person in line is served first.
- Two fundamental operations:
  - **Enqueue** — join the back of the line.
  - **Dequeue** — leave from the front of the line.

### Stack

- **LIFO** — **L**ast **I**n, **F**irst **O**ut.
- Real-world analogies:
  - A stack of trays in a cafeteria — you always take from the top.
  - An email inbox — the newest email appears at the top and often gets addressed first.
- Two fundamental operations:
  - **Push** — add to the top of the stack.
  - **Pop** — remove from the top of the stack.

---

## 3. Implementing a Queue in C

```c
const int CAPACITY = 50;

typedef struct
{
    person people[CAPACITY];
    int size;
} queue;
```

- `people` — a fixed-size array holding up to `CAPACITY` (50) persons.
- `size` — tracks how many people are **currently** in the queue (distinct from the total _capacity_).

### The Fundamental Limitation

- This implementation can hold **at most 50 people** — a **51st** person cannot be added.
- **Possible fix:** recompile with a larger number (e.g., 500 or 5000) — but this creates a trade-off:
  - Too small → cannot accommodate demand (e.g., a big concert).
  - Too large → wastes memory if far fewer people actually show up.
- This is an inherent limitation of **static allocation** — deciding a fixed size **at compile time**, before the program runs.
- Additionally, implementing dequeue requires tracking **which index represents the "front"** of the queue as elements are removed — added bookkeeping complexity.

---

## 4. Implementing a Stack in C

```c
const int CAPACITY = 50;

typedef struct
{
    person people[CAPACITY];
    int size;
} stack;
```

- Structurally **identical** to the queue implementation above (just renamed).
- **Easier in one sense:** removing the most recently added element is simple — it's always at the "end" of the array (position `size - 1`), so no need to track a separate "front" pointer.
- **Same limitation as the queue:** capped at a fixed size (e.g., 50) decided in advance.

---

## 5. Dictionaries as an Abstract Data Type

- A **dictionary** is another common abstract data type — think of an actual paper dictionary: a two-column table with a **word** on the left and its **definition** on the right.
- Generalized in computer science: the left column is called the **key**, and the right column is called the **value** — together forming a **key–value pair**.
- Real-world examples: an actual dictionary (word → definition), a phonebook (name → number), a Contacts app (name → contact details).
- Like queues and stacks, dictionaries can be implemented in different ways — some **faster**, some **slower**, depending on the underlying data structure chosen.

---

## 6. Arrays Revisited — Static Allocation Problems

### Recap of Arrays

- An **array** is a chunk of memory where values are stored **contiguously** (back-to-back).
- Example:

```c
#include <stdio.h>

int main(void)
{
    int list[3];

    list[0] = 1;
    list[1] = 2;
    list[2] = 3;

    for (int i = 0; i < 3; i++)
    {
        printf("%i\n", list[i]);
    }
}
```

### The Core Problem

- Arrays require deciding the size **in advance** (statically).
- If you later want to add a **4th** value, ideally it would sit right next to the original 3 in memory — but that adjacent memory might already be occupied by other data (e.g., a string like `"HI!\0"`), so you **cannot simply extend** the array in place.
- **Solution requires:**
  1. Allocating a new, larger chunk of memory.
  2. Copying the old values into the new chunk.
  3. Adding the new value.
  4. Freeing the old chunk of memory.

---

## 7. Dynamically Resizing Arrays with `malloc`

### Initial Dynamic Allocation

```c
#include <stdio.h>
#include <stdlib.h>

int main(void)
{
    int *list = malloc(3 * sizeof(int));
    if (list == NULL)
    {
        return 1;
    }

    list[0] = 1;
    list[1] = 2;
    list[2] = 3;

    for (int i = 0; i < 3; i++)
    {
        printf("%i\n", list[i]);
    }

    free(list);
    return 0;
}
```

- `malloc(3 * sizeof(int))` requests enough memory for 3 integers (`sizeof(int)` used instead of hardcoding `4`, for **portability**).
- `list` is now a **pointer** (`int *`), not a static array — but bracket notation (`list[0]`, `list[1]`...) still works due to C's pointer/array equivalence.
- **Always check for `NULL`** after `malloc` — indicates allocation failure.

### The Wrong Way to "Resize"

```c
list = malloc(4 * sizeof(int));   // BUG!
```

- **Problem:** This **overwrites** the `list` pointer with a brand-new address, **losing track of the original 3-integer memory block** — a classic **memory leak**. The original memory can never be freed or reused again during the program's execution.

### The Correct (Manual) Way

```c
#include <stdio.h>
#include <stdlib.h>

int main(void)
{
    int *list = malloc(3 * sizeof(int));
    if (list == NULL)
    {
        return 1;
    }

    list[0] = 1;
    list[1] = 2;
    list[2] = 3;

    int *tmp = malloc(4 * sizeof(int));
    if (tmp == NULL)
    {
        free(list);          // free original memory before aborting
        return 1;
    }

    for (int i = 0; i < 3; i++)
    {
        tmp[i] = list[i];    // copy old values into new memory
    }
    tmp[3] = 4;               // add the new 4th value

    free(list);               // free the old block
    list = tmp;               // point list at the new block

    for (int i = 0; i < 4; i++)
    {
        printf("%i\n", list[i]);
    }

    free(list);
    return 0;
}
```

### Key Lessons

- **Always check for `NULL`** after every `malloc` call.
- **Free the original memory** if a subsequent `malloc` fails, _before_ returning/aborting — otherwise you leak the first allocation even though the program is about to end.
- Use a **temporary variable** (`tmp`) to hold the new memory address so you don't lose track of the _old_ memory before it's copied and freed.
- Order of operations matters: free the _old_ list only after copying its contents into the _new_ one, and only update the `list` variable to point at the new memory _after_ the old one has been freed.

---

## 8. Using `realloc`

- `realloc` (from `<stdlib.h>`) simplifies the manual resize-and-copy process shown above.
- It attempts to:
  1. **Grow the existing block in place** if there's contiguous free memory available (fast — no copying needed).
  2. Otherwise, **allocate a new block elsewhere and automatically copy the old data** into it.

### Syntax

```c
int *tmp = realloc(list, 4 * sizeof(int));
if (tmp == NULL)
{
    free(list);
    return 1;
}
tmp[3] = 4;
list = tmp;
```

- **First argument:** the address of the memory block you want resized (e.g., the pointer returned by an earlier `malloc`).
- **Second argument:** the new total size desired.
- Removes the need for a manual copying loop — `realloc` handles that internally.
- Still must:
  - Check for `NULL` (allocation can still fail, e.g., due to memory fragmentation).
  - Use a temporary variable, in case `realloc` fails and returns `NULL` — the original pointer must remain valid so it can still be freed.

### Q&A Highlights

- **How does `realloc` know how much data to copy?** The C standard library keeps internal bookkeeping (akin to a lookup table) of how much memory was originally allocated at a given address.
- **Why not just reuse `tmp` instead of reassigning to `list`?** Reusing `list` is considered better style — `tmp` is meant to be a throwaway/temporary variable, and relying on it later increases the risk of confusing bugs.
- Languages like Python don't require declaring array sizes upfront because **someone else has already written this exact kind of resizing logic** into the language's implementation.

---

## 9. New Syntax: The Arrow Operator (`->`)

- Previously learned syntax:
  - **`.` (dot operator)** — access a field inside a `struct` directly (e.g., `person.name`).
  - **`*` (asterisk / dereference operator)** — follow a pointer to access the value at an address.
- New combined syntax:

```c
(*n).number   // dereference n, then access its "number" field
n->number     // equivalent shorthand — the "arrow" operator
```

- The **arrow operator (`->`)** combines dereferencing and field access into a single, more readable syntax.
- Visually, it mirrors the arrows drawn in memory diagrams throughout the course.

---

## 10. Linked Lists — Concept and Motivation

### The Core Idea

- Rather than requiring values to be **contiguous** in memory (like arrays), a **linked list** allows values to live **anywhere** in memory, connected together via pointers.
- Each element (called a **node**) stores:
  1. **Data** — the actual value of interest (e.g., an integer).
  2. **Metadata** — a pointer to the _next_ node in the list.
- The **last node's** pointer is set to **`NULL`**, signaling the end of the list.
- A single external variable (e.g., `list`) stores just the **address of the first node** — this is sufficient to traverse the entire structure.

### Benefits

- **Dynamic growth** — new nodes can be added anywhere there's free memory; no need to relocate/copy existing data.
- Solves the fundamental weakness of arrays (needing to guess a fixed size upfront).

### Terminology Note

- In Python, the built-in `list` type behaves like a **dynamic array** (often implemented similarly to what other languages call a **vector**) — not identical to the classic "linked list" structure discussed here, though conceptually related in that it, too, grows and shrinks automatically.

---

## 11. Defining a Node in C

```c
typedef struct node
{
    int number;
    struct node *next;
} node;
```

### Why `struct node` Appears Twice

- The **first mention** — `struct node { ... }` — gives the structure an internal name (`node`) _before_ the closing `typedef` alias is established.
- Inside the struct, the pointer field must be declared as `struct node *next;` (not just `node *next;`), because at that point in the file, the compiler has only seen the tag `struct node`, not yet the final `typedef` alias `node`.
- This mirrors the earlier lesson about needing function **prototypes** before a function is used — the compiler reads top-to-bottom, left-to-right.

---

## 12. Building a Linked List Step by Step

### Initializing an Empty List

```c
node *list = NULL;
```

- The list starts as a single pointer, initialized to `NULL` — representing an empty list.

### Allocating and Inserting a New Node (Prepending)

```c
#include <cs50.h>
#include <stdio.h>
#include <stdlib.h>

typedef struct node
{
    int number;
    struct node *next;
} node;

int main(void)
{
    node *list = NULL;

    for (int i = 0; i < 3; i++)
    {
        node *n = malloc(sizeof(node));
        if (n == NULL)
        {
            return 1;
        }

        n->number = get_int("Number: ");
        n->next = NULL;

        n->next = list;   // point new node at current head of list
        list = n;         // update list to point at the new node
    }
}
```

### Step-by-Step Explanation

1. `malloc(sizeof(node))` allocates enough memory for one full node (data + pointer), returning its address into a temporary pointer `n`.
2. Always **check for `NULL`** after `malloc`.
3. `n->number = ...` — go to the new node and set its `number` field (via the arrow operator).
4. `n->next = NULL` — initialize the pointer field defensively, even though it will usually be overwritten shortly after.
5. **Prepending logic** (the crucial insight):
   - `n->next = list;` — first, point the _new_ node's `next` field at whatever the list _currently_ points to (preserving the existing chain).
   - `list = n;` — _then_ update the `list` variable itself to point at the new node.
   - **Order matters:** if you update `list = n` _before_ setting `n->next = list`, you **orphan** (lose track of) the rest of the list — a memory leak, since nothing points to it anymore.

### Result

- Because each new node is **prepended** (inserted at the front), the resulting list ends up in **reverse order** of insertion (e.g., inserting 1, 2, 3 produces the list `3 → 2 → 1 → NULL`).

---

## 13. Traversing and Printing a Linked List

### Using a `while` Loop

```c
node *ptr = list;
while (ptr != NULL)
{
    printf("%i\n", ptr->number);
    ptr = ptr->next;
}
```

- `ptr` acts as a **temporary pointer** ("another foam finger") that starts at the head of the list.
- On each iteration: print the current node's value, then move `ptr` to point at the next node.
- Loop terminates once `ptr` becomes `NULL` (end of list reached).

### Equivalent Using a `for` Loop

```c
for (node *ptr = list; ptr != NULL; ptr = ptr->next)
{
    printf("%i\n", ptr->number);
}
```

- Functionally identical — more compact, though arguably less immediately readable to some.

---

## 14. Running Time of Linked List Operations

Using the simple **prepend-only** implementation above:

| Operation                                   | Running Time | Why                                                                                                      |
| ------------------------------------------- | ------------ | -------------------------------------------------------------------------------------------------------- |
| **Insertion** (prepend)                     | **O(1)**     | Only a constant number of pointer updates are needed, regardless of list length — no traversal required. |
| **Search**                                  | **O(n)**     | In the worst case, the target value is at the very end of the list — must traverse the whole thing.      |
| **Traversal** (e.g., printing all elements) | **O(n)**     | By definition, every element must be visited.                                                            |
| **Deletion**                                | **O(n)**     | May need to traverse to find the target node before removing it.                                         |

- **Downside of pure prepending:** the list ends up in **reverse insertion order**, with no meaningful sorted structure.

### Alternative: Appending to the End

- Appending (always adding to the tail) also requires traversing to find the current end of the list.
- **Running time of insertion via append: O(n)** — strictly worse than prepend's O(1), unless you maintain an additional pointer to the tail (a valid but extra-memory-cost optimization).

---

## 15. Inserting in Sorted Order — Four Scenarios

To maintain a **sorted** linked list regardless of insertion order, four distinct cases must be handled:

1. **Empty list** — the new node becomes the entire list.
2. **Prepend** — the new value is smaller than the current first element; insert at the head.
3. **Append** — the new value is larger than all existing elements; insert at the tail.
4. **Insert in the middle** — the new value belongs between two existing nodes.

### Simplified Code Skeleton

```c
node *n = malloc(sizeof(node));
if (n == NULL)
{
    return 1;
}
n->number = number;
n->next = NULL;

// Case 1: empty list
if (list == NULL)
{
    list = n;
}
// Case 2: belongs at the beginning
else if (n->number < list->number)
{
    n->next = list;
    list = n;
}
else
{
    // Traverse to find the correct spot
    for (node *ptr = list; ptr != NULL; ptr = ptr->next)
    {
        // Case 3: reached the end — append
        if (ptr->next == NULL)
        {
            ptr->next = n;
            break;
        }
        // Case 4: belongs in the middle
        if (n->number < ptr->next->number)
        {
            n->next = ptr->next;
            ptr->next = n;
            break;
        }
    }
}
```

- **Important subtlety:** if a `malloc` call fails partway through building a multi-node list (e.g., on the 2nd or 3rd node), previously allocated nodes must still be **freed** before returning an error — otherwise, earlier successful allocations leak. This adds real complexity to writing fully "correct," leak-free code.

---

## 16. Freeing a Linked List

- You **cannot** simply call `free(list)` once — `free()` has no concept of a "linked list"; it only knows about the single block of memory whose address you give it.
- You must **traverse the entire list**, freeing **one node at a time**, while being careful not to **lose track (orphan)** the rest of the list in the process.

### Example

```c
void unload(node *list)
{
    node *ptr = list;
    while (ptr != NULL)
    {
        node *next = ptr->next;   // save the next node before freeing current one
        free(ptr);
        ptr = next;
    }
}
```

- **Key trick:** save a pointer to the _next_ node **before** freeing the current node — otherwise, once a node is freed, its `next` field can no longer be safely read.
- Best practice: encapsulate this logic into its own function (e.g., `unload`) so it can be reused wherever needed (e.g., mid-program error handling, or cleanup at the end of `main`).

---

## 17. Recap: Arrays vs Linked Lists

| Feature                             | Arrays                                            | Linked Lists                                              |
| ----------------------------------- | ------------------------------------------------- | --------------------------------------------------------- |
| Memory layout                       | Contiguous                                        | Scattered (connected via pointers)                        |
| Size                                | Fixed at compile-time (or requires costly resize) | Dynamic — grows/shrinks as needed                         |
| Search (`binary search`, if sorted) | **O(log n)**                                      | **O(n)** — cannot jump to the "middle" without traversing |
| Insertion                           | Potentially costly (requires resizing/copying)    | O(1) if prepending; O(n) otherwise                        |
| Deletion                            | Costly (requires shifting elements)               | O(n) (must traverse to find/unlink node)                  |
| Extra memory overhead               | None (just the data)                              | One extra pointer per node                                |

- **Fundamental trade-off:** arrays offer _fast binary search_ but are inflexible in size; linked lists are _flexible in size_ but lose the ability to do binary search (since finding the "middle" element requires traversal, which itself costs O(n)).
- **Note:** a **doubly linked list** (pointers in both directions — to `next` _and_ `previous`) can simplify certain implementation details (e.g., avoiding "look-ahead" logic), but does **not** fundamentally improve the asymptotic running time of search/insert/delete.

---

## 18. Trees and Binary Search Trees

### Motivation

- Goal: combine the **speed** of arrays (binary search, O(log n)) with the **dynamism** of linked lists (growing/shrinking without costly copying).
- Solution: **trees** — the first _two-dimensional_ data structure covered in the course (arrays and linked lists are both effectively one-dimensional).

### Structure

- The topmost node is called the **root**.
- Each node may have up to **two children**: a **left child** and a **right child**.
- Nodes with no children are called **leaves**.

### Binary Search Tree (BST) Property

> For every node: all values in its **left subtree** are **less than** the node's value, and all values in its **right subtree** are **greater than** the node's value.

- This property is **recursive** — it holds true not just for the root, but for _every_ subtree within the tree.

### Example

```
        4
      /   \
     2     6
    / \   / \
   1   3 5   7
```

- Searching for `5`: start at `4` → `5 > 4`, go right to `6` → `5 < 6`, go left to `5` → found (2 steps).

### Running Time

| Operation | Running Time |
| --------- | ------------ |
| Search    | **O(log n)** |
| Insertion | **O(log n)** |
| Deletion  | **O(log n)** |

- The height of a balanced binary tree with `n` elements is approximately **log₂(n)**.

### Cost — Increased Memory Overhead

- Each node now stores: 1 integer + **2 pointers** (left and right child) — roughly **3× the memory** of a plain integer, compared to just 4 bytes per element in an array.
- Considered an acceptable trade-off given modern memory availability — cheap for small datasets, but potentially significant at very large scale (e.g., millions of elements).

---

## 19. Implementing a Binary Search Tree in C

### Node Definition

```c
typedef struct node
{
    int number;
    struct node *left;
    struct node *right;
} node;
```

### Recursive Search Function

```c
bool search(node *tree, int number)
{
    if (tree == NULL)
    {
        return false;
    }
    else if (number < tree->number)
    {
        return search(tree->left, number);
    }
    else if (number > tree->number)
    {
        return search(tree->right, number);
    }
    else // number == tree->number
    {
        return true;
    }
}
```

### Key Design Notes

- **Base case:** an empty (`NULL`) tree/subtree means the number cannot be found — return `false`.
- **Recursive cases:** depending on whether the target is smaller or larger than the current node's value, recurse into the **left** or **right** subtree respectively — each subtree is itself a smaller, valid binary search tree.
- **Elegance:** this function elegantly leverages the **recursive nature** of the data structure itself — searching a subtree is exactly the same operation as searching the whole tree, just with a smaller input.

---

## 20. The Balance Problem

- The efficiency of a binary search tree (O(log n)) depends entirely on the tree remaining **reasonably balanced**.
- **Worst-case scenario:** if values are inserted in **already-sorted order** (e.g., 1, 2, 3, 4, 5, 6 in sequence), each new value becomes the right child of the previous one — the tree **degenerates into a linked list**.
  - This results in **O(n)** running time for search, insertion, and deletion — losing all the benefit of using a tree in the first place.
- **Real-world solution (not implemented in this lecture):** self-balancing tree variants (e.g., AVL trees, red-black trees) automatically **rebalance** the tree upon insertion/deletion (e.g., via rotations), at a small additional time cost, in order to **guarantee** the tree's height stays close to log₂(n).

---

## 21. The Quest for Constant Time — Hashing

### The Ultimate Goal: O(1)

- **Constant time** — the number of steps required does **not** depend on how much data is stored, whether there are 10, 10,000, or 10,000,000 elements.
- Visually: a straight horizontal line on a graph of "time vs. number of elements," in contrast to linear (O(n)) or logarithmic (O(log n)) growth curves.

### Hashing — The Core Idea

> **Hashing** maps an (effectively) infinite domain of possible input values to a finite, fixed range of output values (often called "buckets").

### Real-World Analogy

- Sorting a deck of 52 playing cards: instead of sorting all 52 individually, first **bucketize** them by suit (4 buckets: hearts, diamonds, clubs, spades), reducing the problem to four much smaller, simpler sub-problems.

---

## 22. Hash Functions

> A **hash function** takes an input (e.g., a name) and deterministically outputs the index of the bucket it belongs to.

### Example: Hashing by First Letter (26 Buckets, A–Z)

```c
#include <ctype.h>

unsigned int hash(const string name)
{
    return toupper(name[0]) - 'A';
}
```

- Converts the first letter to uppercase (to be case-insensitive), then subtracts the ASCII value of `'A'` (65) to get a number from `0` (for names starting with A) to `25` (for names starting with Z).
- **`const`** — indicates the function will not modify the caller's original string, even though it's received by reference (via a pointer) — a defensive programming practice.
- **`unsigned int`** — signals the return value should never be negative, since it represents an array index — another defensive refinement.

---

## 23. Hash Tables

> A **hash table** combines an **array** (of "buckets") with **linked lists** (one per bucket) — described as one of the most broadly useful ("Swiss Army knife") data structures in computer science.

### Structure

```c
typedef struct node
{
    char *name;
    struct node *next;
} node;

node *table[26];   // array of 26 pointers, one per letter of the alphabet
```

- Each slot in the `table` array initially points to `NULL` (empty bucket).
- Inserting a name: compute its hash (e.g., first letter → index), then **prepend** (or append) a new node to the linked list at that index — exactly like building a standalone linked list, just localized to one "bucket."

### Purpose

- This is a natural, lower-level way to implement the **dictionary abstract data type** discussed earlier — associating keys (e.g., names) with values (e.g., phone numbers).

---

## 24. Collisions and Chaining

> A **collision** occurs when two or more different inputs hash to the **same bucket**.

- With only 26 buckets (based on first letter), many different names will inevitably share a bucket (e.g., many Nintendo character names might start with "M" or "L").
- **Solution: chaining** — each bucket holds a **linked list** of all names that hash to that index, rather than overwriting ("clobbering") previous entries or trying to cram multiple values into a single array slot.

### Running Time with Collisions

- If we assume names are **uniformly distributed** across `k` buckets, each bucket's linked list has an average length of about `n / k`.
- Running time for search becomes **O(n / k)** — faster in practice than a plain linked list by a constant factor of `k`, but **still O(n) asymptotically**, since `k` is a constant and Big-O notation ignores constant factors.

### Reducing Collisions with a "Smarter" Hash Function

- Instead of hashing on just the **first letter** (26 buckets), hash on the **first three letters** (26³ = 17,576 possible buckets).
- **Benefit:** far fewer collisions.
- **Cost:** vastly more memory required for the (mostly empty/unused) bucket array — a classic **space-for-speed trade-off**.

---

## 25. Tries — Achieving True Constant Time

> A **trie** ("retrieval tree") is a tree made out of arrays — each node is itself an array of pointers (e.g., size 26, one slot per letter of the alphabet), and each pointer, if non-null, leads to the node representing the _next_ letter of some stored word/name.

### Structure

```c
typedef struct node
{
    struct node *children[26];   // one slot per letter A-Z
    char *number;                 // or other associated value; NULL if this node doesn't mark a complete stored name
} node;
```

- Traversing from the root, each successive node represents the **next letter** in the name being looked up or inserted.
- A special marker (e.g., a non-null `number` field, or a separate boolean flag) indicates that a complete, valid name **ends** at this node.
- Names are stored **implicitly** by the pattern of non-null pointers — the actual string is never stored as one contiguous block; it's represented "letter by letter" via the path from the root.

### Example

- Storing "TOAD": root → `T` node → `O` node → `A` node → `D` node, and the `D` node is flagged as the end of a valid name.
- Storing "TOE" and "TOM" afterward can **reuse** the shared `T` and `O` nodes, branching off only where the names differ.

### Lookup Process

- To check if "TOAD" exists: hash on `'T'` (root), follow to the `O` node, hash on `'O'`, follow to `A`, hash on `'A'`, follow to `D`, hash on `'D'`, and check if that final node is flagged as a valid ending.
- **Number of steps = length of the word being searched** — completely **independent of how many total words are stored** in the trie (whether 3 or 3 million).

### Running Time

| Operation | Running Time                                                                         |
| --------- | ------------------------------------------------------------------------------------ |
| Search    | **O(1)** (constant, relative to number of stored items — bounded by max word length) |
| Insertion | **O(1)** (same reasoning)                                                            |

### The Trade-off

- **Massive memory overhead:** every node requires an array of 26 (or more) pointers, the vast majority of which remain `NULL`/unused for most paths, since not every letter combination corresponds to a real word/name.
- This is why, despite offering the theoretically best (constant) running time, **tries are used less often in practice** than hash tables — the memory cost is usually too high relative to the benefit, especially at scale.

### Real-World Analogy: Sweetgreen Salad Pickup Shelves

- A real-world hash table example: a restaurant (Sweetgreen) organizes pickup orders into labeled shelf sections by customer name range (e.g., A–E, F–J, K–N, O–Z).
- **Real-world collision problem:** if the alphabet ranges aren't chosen carefully (e.g., one letter per shelf), some sections (like "A", "B", "C") may overflow frequently while others (like "Q", "Z") sit empty — motivating **wider, better-balanced bucket ranges** in practice, exactly the kind of trade-off computer scientists reason about with real hash tables.

---

## 26. Trade-offs Summary Table

| Data Structure                    | Search                       | Insert                         | Delete   | Extra Memory                     | Key Weakness                          |
| --------------------------------- | ---------------------------- | ------------------------------ | -------- | -------------------------------- | ------------------------------------- |
| **Array (sorted)**                | O(log n)                     | O(n) (resize/shift)            | O(n)     | None                             | Fixed size; costly resize             |
| **Linked List**                   | O(n)                         | O(1) (prepend) / O(n) (sorted) | O(n)     | 1 pointer/node                   | No binary search possible             |
| **Binary Search Tree** (balanced) | O(log n)                     | O(log n)                       | O(log n) | 2 pointers/node                  | Can degrade to O(n) if unbalanced     |
| **Hash Table**                    | ~O(n/k), asymptotically O(n) | ~O(n/k)                        | ~O(n/k)  | k buckets + pointers             | Collisions; still O(n) asymptotically |
| **Trie**                          | O(1) (bounded by key length) | O(1)                           | O(1)     | Very high (many unused pointers) | Massive memory overhead               |

---

## 27. Key Terms Glossary

| Term                         | Definition                                                                                                          |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| **Abstract Data Type (ADT)** | A data type defined by its behavior/operations, independent of its underlying implementation                        |
| **Queue**                    | FIFO structure; operations: enqueue (add), dequeue (remove)                                                         |
| **Stack**                    | LIFO structure; operations: push (add), pop (remove)                                                                |
| **Dictionary**               | Abstract data type storing key–value pairs                                                                          |
| **Static allocation**        | Memory size decided at compile-time; cannot grow/shrink at runtime                                                  |
| **Dynamic allocation**       | Memory requested/resized at runtime, via `malloc`/`realloc`/`free`                                                  |
| **`realloc()`**              | Resizes a previously allocated memory block, copying data automatically if it must move                             |
| **Arrow operator (`->`)**    | Shorthand combining pointer dereference and struct field access (equivalent to `(*ptr).field`)                      |
| **Node**                     | A generic container holding data plus metadata (e.g., pointer(s) to other nodes)                                    |
| **Linked List**              | A sequence of nodes connected via pointers, not necessarily contiguous in memory                                    |
| **Prepend**                  | Insert a new node at the beginning of a list                                                                        |
| **Append**                   | Insert a new node at the end of a list                                                                              |
| **Orphan (memory)**          | To lose all references to a piece of allocated memory, creating a memory leak                                       |
| **Tree**                     | A hierarchical, two-dimensional data structure with a root and child nodes                                          |
| **Root**                     | The topmost node of a tree                                                                                          |
| **Leaf**                     | A node with no children                                                                                             |
| **Binary Search Tree (BST)** | A tree where every node's left subtree contains smaller values and right subtree contains larger values             |
| **Balanced tree**            | A tree whose height is kept close to log₂(n), ensuring efficient operations                                         |
| **Hashing**                  | Mapping an infinite/large domain of inputs to a finite range of outputs (buckets)                                   |
| **Hash function**            | The specific algorithm used to compute which bucket an input maps to                                                |
| **Hash table**               | A data structure combining an array of buckets with linked lists (chaining) to implement a dictionary               |
| **Collision**                | When two different inputs hash to the same bucket                                                                   |
| **Chaining**                 | Resolving collisions by storing all colliding entries in a linked list within the same bucket                       |
| **Trie**                     | A tree of arrays, where each level represents one character of a key, enabling O(1) lookup relative to dataset size |
| **Big O notation**           | Describes the upper-bound growth rate of an algorithm's running time relative to input size                         |
| **O(1)**                     | Constant time — independent of input size                                                                           |
| **O(log n)**                 | Logarithmic time — grows very slowly as input size increases                                                        |
| **O(n)**                     | Linear time — grows proportionally with input size                                                                  |

---

## Summary

This lecture was the capstone of CS50's coverage of **C** and **memory management**, culminating in a survey of the fundamental data structures used throughout computer science:

1. **Abstract data types** (queues, stacks, dictionaries) define behavior independent of implementation.
2. **Arrays** are fast (binary search, O(log n)) but rigid in size, requiring costly `malloc`/`realloc` and copying to grow.
3. **Linked lists** solve the sizing problem via dynamic, pointer-based allocation, but lose binary search, degrading key operations to O(n).
4. **Binary search trees** merge array-like speed (O(log n)) with linked-list-like dynamism, at the cost of extra memory (two pointers per node) and the risk of becoming unbalanced.
5. **Hash tables** (arrays of linked lists) approach constant time via hashing, but still carry an asymptotic O(n) worst case due to collisions — mitigated, but not eliminated, by using more/better-distributed buckets.
6. **Tries** achieve true O(1) lookup (relative to dataset size) by representing data as paths through arrays of pointers, at the cost of very high memory overhead.

The central theme throughout: there is no single "best" data structure — every design decision trades off **time, memory, and code complexity**, and choosing the right structure depends on the specific constraints of the problem at hand. This sets the stage for the following week's transition to **Python**, where many of these implementation details are handled automatically by the language itself.
