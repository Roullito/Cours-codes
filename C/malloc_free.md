# 📘 C - `malloc` and `free`

## 📚 Resources
Read or watch:
- [0x0a - malloc & free - quick overview.pdf](#)
- [Dynamic memory allocation in C - malloc, calloc, realloc, free (stop at 6:50)](#)
- **man pages**: `man malloc`, `man free`

---

## 🎯 Learning Objectives
At the end of this lesson, you should be able to explain:
- The difference between **automatic** and **dynamic** memory allocation.
- What `malloc` and `free` are and how to use them.
- When and why to use `malloc`.
- How to use **Valgrind** to check for memory leaks.

---

## 🔹 Memory Allocation in C
### **Automatic vs Dynamic Allocation**
In C, memory can be allocated in two ways:

| Type              | Description |
|------------------|-------------|
| **Automatic Allocation** | Memory is allocated **automatically** when variables are declared inside a function. Memory is released when the function returns. |
| **Dynamic Allocation** | Memory is allocated **manually** at runtime using functions like `malloc`. It must be released using `free`. |

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

## 🏗 Understanding `malloc` and `free`
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

### **Allocating Arrays with `malloc`**
To allocate an array dynamically:
```c
int *array = malloc(sizeof(int) * 5);
```
- Allocates memory for 5 integers.
- Access elements using `array[index]`.
- Always **free the allocated memory** after use:
```c
free(array);
```

---

### **`free` - Deallocating Memory**
**Prototype:**
```c
void free(void *ptr);
```
- Releases memory previously allocated by `malloc`.
- If memory is not freed, it **causes memory leaks**.
- **Freeing a `NULL` pointer is safe** (it does nothing).

#### **Example: Correct Memory Deallocation**
```c
int *num = malloc(sizeof(int));
free(num); // Correct usage
```

#### **Incorrect Usage (Dangling Pointer)**
```c
int *num = malloc(sizeof(int));
free(num);
*num = 10;  // ❌ Undefined behavior! Memory has been freed.
```
- **After freeing memory, always set the pointer to `NULL`**:
```c
free(num);
num = NULL;
```

---

## 🚨 Common Issues with `malloc` and `free`
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
- **Only allowed standard functions: `malloc`, `free`** (no `printf`, `puts`, `calloc`, `realloc`).

---

## 🚀 Conclusion
- `malloc` **allocates memory dynamically**, allowing flexible memory management.
- `free` **deallocates memory**, preventing memory leaks.
- **Always free allocated memory and use Valgrind to check for leaks.**

🎯 **Next Steps:**
- Practice with `malloc` to allocate arrays and structures dynamically.
- Use `Valgrind` to verify memory usage.

🚀 **Happy coding!**

