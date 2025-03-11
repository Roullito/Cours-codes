# 📘 C - `malloc`, `free`, `calloc`, and `realloc`

## 📚 Resources
Read or watch:
- [Do I cast the result of malloc?](#)
- **man pages**: `man malloc`, `man free`, `man exit`, `man calloc`, `man realloc`

---

## 🎯 Learning Objectives
At the end of this lesson, you should be able to explain:
- The difference between **automatic** and **dynamic** memory allocation.
- What `malloc`, `free`, `calloc`, and `realloc` are and how to use them.
- Why and when to use `malloc`, `calloc`, and `realloc`.
- How to use **Valgrind** to check for memory leaks.
- How to use the `exit` function properly.

---

## 🔹 Memory Allocation in C
### **Automatic vs Dynamic Allocation**
In C, memory can be allocated in two ways:

| Type              | Description |
|------------------|-------------|
| **Automatic Allocation** | Memory is allocated **automatically** when variables are declared inside a function. Memory is released when the function returns. |
| **Dynamic Allocation** | Memory is allocated **manually** at runtime using functions like `malloc`, `calloc`, or `realloc`. It must be released using `free`. |

### **Example of Automatic Allocation**
```c
void example(void) {
    int num = 42;  // Automatically allocated on the stack
    char str[] = "Hello"; // Stored in stack memory
}
```
- The variables `num` and `str` are **allocated and freed automatically** when `example()` exits.
- **String literals** (`"Hello"`) are stored in **read-only memory**.

### **Example of Dynamic Allocation**
```c
#include <stdlib.h>

void example(void) {
    int *num = malloc(sizeof(int));  // Allocating memory on the heap
    *num = 42;
    free(num);  // Free the allocated memory
}
```
- The integer pointer `num` stores memory allocated **dynamically**.
- It remains allocated **until explicitly freed with `free()`**.

---

## 🏗 Understanding `malloc`, `free`, `calloc`, and `realloc`
### **`malloc` - Memory Allocation**
**Prototype:**
```c
void *malloc(size_t size);
```
- Allocates `size` bytes of memory on the **heap**.
- Returns a pointer to the allocated memory **or `NULL` if allocation fails**.
- The memory is **not initialized**.

#### **Example: Allocating Memory for an Integer**
```c
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    int *ptr = malloc(sizeof(int));
    if (ptr == NULL) {
        printf("Memory allocation failed!\n");
        return 1;
    }
    *ptr = 42;
    printf("Value: %d\n", *ptr);
    free(ptr);  // Free allocated memory
    return 0;
}
```

### **`calloc` - Contiguous Memory Allocation**
**Prototype:**
```c
void *calloc(size_t nmemb, size_t size);
```
- Allocates memory for `nmemb` elements of `size` bytes each.
- **Initializes the allocated memory to zero.**
- Returns `NULL` if allocation fails.

#### **Example: Allocating an Array with `calloc`**
```c
int *array = calloc(5, sizeof(int));
```
- This allocates memory for **5 integers** and initializes all elements to `0`.
- Always **free the allocated memory** after use:
```c
free(array);
```

### **`realloc` - Resizing Memory Allocation**
**Prototype:**
```c
void *realloc(void *ptr, size_t size);
```
- Changes the size of memory allocated by `malloc` or `calloc`.
- The **newly allocated memory is not initialized**.
- If `ptr` is `NULL`, it behaves like `malloc`.
- If `size` is `0`, it behaves like `free`.

#### **Example: Expanding an Array**
```c
int *array = malloc(3 * sizeof(int));
array = realloc(array, 5 * sizeof(int));
```
- If reallocation is successful, the original memory block may be moved.
- Always **check if `realloc` returns `NULL`** before using the resized array.

---

### **`free` - Deallocating Memory**
**Prototype:**
```c
void free(void *ptr);
```
- Releases memory previously allocated by `malloc`, `calloc`, or `realloc`.
- If memory is not freed, it **causes memory leaks**.
- **Freeing a `NULL` pointer is safe** (it does nothing).

#### **Example: Correct Memory Deallocation**
```c
int *num = malloc(sizeof(int));
free(num); // Correct usage
```

---

## 🚨 Common Issues with Dynamic Allocation
### **1️⃣ Forgetting to Free Memory (Memory Leak)**
```c
void function() {
    int *arr = malloc(100 * sizeof(int));
    // No free(arr), memory is leaked
}
```
**Solution:** Always call `free(arr);` before the function exits.

### **2️⃣ Using Memory After Free (Dangling Pointer)**
```c
int *ptr = malloc(sizeof(int));
free(ptr);
*ptr = 10;  // ❌ Undefined behavior
```
**Solution:** Set `ptr = NULL;` after freeing.

### **3️⃣ Not Checking `malloc` Return Value**
```c
int *ptr = malloc(sizeof(int));
if (ptr == NULL) {
    printf("Allocation failed!\n");
    return 1;
}
```
Always **check if `malloc` returns `NULL`** before using the pointer.

---

## 🛠 Using `Valgrind` to Detect Memory Leaks
`Valgrind` is a tool to check for memory leaks.

### **Installing Valgrind**
```sh
sudo apt update
sudo apt install valgrind
```

### **Running Valgrind**
```sh
valgrind --leak-check=full ./program
```

Example output showing a memory leak:
```
==1234== HEAP SUMMARY:
==1234== 10 bytes in 1 blocks are definitely lost in loss record 1 of 1
```

Example output when all memory is freed correctly:
```
==1234== All heap blocks were freed -- no leaks are possible
```

---

## 📜 Project Requirements
- **Allowed editors**: `vi`, `vim`, `emacs`
- **Compilation**: Ubuntu 20.04 LTS with:
  ```sh
  gcc -Wall -Werror -Wextra -pedantic -std=gnu89 file.c -o file
  ```
- **Each file must end with a new line.**
- **Follow Betty coding style (`betty-style.pl`, `betty-doc.pl`).**
- **No global variables.**
- **No more than 5 functions per file.**
- **Only allowed standard functions: `malloc`, `free`, `calloc`, `realloc`**.

---

## 🚀 Conclusion
- `malloc`, `calloc`, and `realloc` provide **flexible memory management**.
- Always **check for `NULL`** and **free memory properly**.
- Use **Valgrind** to ensure no memory leaks.

🚀 **Happy coding!**

