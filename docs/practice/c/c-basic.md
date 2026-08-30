# C Language — Quick Reference Handout

---

## 1. Compiling & Running

```bash
gcc file.c -o file      # compile
./file                  # run
gcc -Wall -g file.c -o file   # -Wall = warnings, -g = debug symbols (use with gdb)
```

---

## 2. Basic Structure

```c
#include <stdio.h>   // like import

int main(void) {
    printf("Hello\n");
    return 0;         // exit code to OS, 0 = success
}
```

- No classes. Program = functions + structs + global data.
- Every `.c` file compiles independently → needs `.h` headers to share declarations.

---

## 3. Data Types (the biggest mental shift from JS/Python)

C has **fixed-size, static types**. No dynamic typing, no auto-growing numbers.

| Type | Size (typical) | Notes |
|---|---|---|
| `char` | 1 byte | also used as small int |
| `int` | 4 bytes | default whole number |
| `float` | 4 bytes | ~7 digit precision |
| `double` | 8 bytes | ~15-16 digit precision |
| `long`, `long long` | 4/8 bytes | bigger ints |
| `unsigned int` | 4 bytes | no negatives, doubles positive range |

```c
int x = 5;
double y = 3.14;
char c = 'A';       // single quotes = char
```

⚠️ **Overflow is silent.** `int` wraps around instead of throwing an error. Integer division truncates: `5 / 2 == 2`, not `2.5`.

---

## 4. Pointers — the whole point of learning C

A pointer = a variable that stores a **memory address**.

```c
int x = 10;
int *p = &x;    // p holds address of x  (& = "address of")
printf("%d", *p);  // *p = "value at that address" -> 10
*p = 20;           // changes x itself, since p points to it
```

**Read pointer declarations right-to-left near the star:**
`int *p` → "p is a pointer to an int"

### Why this matters for JS/Python devs:
- In JS/Python, passing an object passes a reference automatically. In C, **everything is pass-by-value by default** — even arrays/structs get copied unless you pass a pointer.
- This is *why* functions that modify data (like `swap`) need pointers:

```c
void swap(int *a, int *b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}
swap(&x, &y);  // pass addresses, not copies
```

### Pointer arithmetic
```c
int arr[5] = {1,2,3,4,5};
int *p = arr;      // array name decays to pointer to first element
p++;                // moves forward by sizeof(int) bytes, not 1 byte
printf("%d", *p);   // 2
```

This is *the* reason `arr[i]` is O(1): it's just `*(arr + i)` — pointer math, no searching.

---

## 5. Arrays

```c
int arr[5];             // fixed size, stack-allocated, NOT resizable
int arr[5] = {1,2,3,4,5};
arr[2] = 99;
```

- No `.length`. You must track size yourself (this is why "off-by-one" bugs are a C rite of passage).
- Arrays don't know their own size once passed to a function (they decay to a pointer) — you always pass size separately.

```c
void printArr(int *arr, int size) {
    for (int i = 0; i < size; i++) printf("%d ", arr[i]);
}
```

---

## 6. Memory: Stack vs Heap

| | Stack | Heap |
|---|---|---|
| What | local variables, function calls | manually allocated memory |
| Lifetime | dies when function returns | lives until you `free()` it |
| Speed | fast | slower |
| Control | automatic | manual (`malloc`/`free`) |

```c
#include <stdlib.h>

int *arr = malloc(5 * sizeof(int));  // heap allocation
if (arr == NULL) { /* allocation failed */ }

arr[0] = 1;

free(arr);       // YOU must release it
arr = NULL;       // avoid dangling pointer (using freed memory)
```

**This is the whole "why" of garbage collection.** JS/Python do this `malloc`/`free` dance for you automatically. Forgetting `free()` = memory leak. Using memory after `free()` = dangling pointer bug ("use-after-free"). Two `free()` calls on the same pointer = crash.

### Resizing (this is literally how dynamic arrays / `list.append` work under the hood)
```c
arr = realloc(arr, 10 * sizeof(int));  // grow to 10 ints, may move the block
```

---

## 7. Structs (your "objects," minus methods)

```c
typedef struct {
    int x;
    int y;
} Point;

Point p1 = {3, 4};
p1.x = 10;

Point *ptr = &p1;
ptr->x = 20;    // -> is shorthand for (*ptr).x
```

Build a linked list node like this:
```c
typedef struct Node {
    int data;
    struct Node *next;   // self-referential pointer
} Node;

Node *head = malloc(sizeof(Node));
head->data = 1;
head->next = NULL;
```

---

## 8. Strings

C strings are just `char` arrays ending in `'\0'` (null terminator). No built-in string type.

```c
char name[] = "Alice";   // auto null-terminated, 6 bytes: A l i c e \0
```

Common functions (`#include <string.h>`): `strlen`, `strcpy`, `strcat`, `strcmp`.
⚠️ These don't check bounds — classic buffer overflow source. Use `strncpy`/`snprintf` when possible.

---

## 9. Control Flow — basically identical to JS

```c
if (x > 0) { ... } else if (...) { ... } else { ... }
for (int i = 0; i < n; i++) { ... }
while (cond) { ... }
switch (x) { case 1: ...; break; default: ...; }
```

No `for...of`, no `forEach`. Just index-based loops.

---

## 10. Functions & Headers

```c
// math_utils.h
int add(int a, int b);       // declaration (prototype)

// math_utils.c
#include "math_utils.h"
int add(int a, int b) { return a + b; }   // definition
```

- Function must be declared before use (top of file, or via header).
- No default params, no overloading, no closures.

---

## 11. Common Gotchas (things that will bite you first)

1. **Segfault** = you dereferenced a bad pointer (NULL, freed, or uninitialized). Your #1 debugging companion.
2. Uninitialized variables contain garbage, not `0` or `undefined`.
3. `=` vs `==` — `if (x = 5)` compiles and is always true (assignment, not comparison). Classic bug.
4. Forgetting `&` when passing to `scanf`: `scanf("%d", &x);`
5. Array bounds aren't checked — writing past the end silently corrupts memory instead of throwing an error.
6. Always `free` what you `malloc`. Always check `malloc` didn't return `NULL`.

---

## 12. Suggested Build Order (ties back to DS&A practice)

1. Variables, pointers, `&`/`*`, pass-by-value vs pass-by-pointer
2. Arrays + manual bounds handling
3. `malloc`/`free`/`realloc` → build a dynamic array (mimic `list.append`)
4. Structs + self-referential structs → linked list
5. Stack/queue on top of array or linked list
6. Recursion + watch it blow the call stack on purpose (see the "why" of stack overflow)
7. Hash table (array of linked lists for collisions)
8. Trees/graphs (structs + pointers, same as linked list but branching)
