# 📘 C - Structures & `typedef`

## 📚 Resources
Read or watch:
- [0x0d. Structures.pdf](#)
- [struct (C programming language)](#)
- [Documentation: structures](#)
- [0x0d. Typedef and structures.pdf](#)
- [typedef](#)
- **Programming in C by Stephen Kochan - Chapter 8, Working with Structures (p.163-189)**
- [The Lost Art of C Structure Packing (Advanced - not mandatory)](#)

---

## 🎯 Learning Objectives
At the end of this lesson, you should be able to explain:
- What **structures** are in C, why and how to use them.
- How to use `typedef` to create custom types.
- When to use structures vs other data types.

---

## 🔹 What is a Structure?
A **structure** (`struct`) is a user-defined data type that groups multiple variables of different types under a single name. It allows **better organization and management** of complex data in C.

### **Syntax of a Structure**
```c
struct Person {
    char name[50];
    int age;
    float height;
};
```
This creates a new **struct type** named `Person`, containing:
- `name` → a string of 50 characters.
- `age` → an integer.
- `height` → a floating-point number.

### **Declaring Structure Variables**
```c
struct Person person1;
```

### **Initializing a Structure**
```c
struct Person person1 = {"Alice", 25, 1.70};
```

### **Accessing Structure Members**
```c
printf("Name: %s\n", person1.name);
printf("Age: %d\n", person1.age);
printf("Height: %.2f meters\n", person1.height);
```

---

## 🏗 Using `typedef` with Structures
### **What is `typedef`?**
`typedef` allows you to define a new name (alias) for an existing data type, making code more readable and easier to maintain.

### **Using `typedef` with Structures**
Instead of:
```c
struct Person {
    char name[50];
    int age;
    float height;
};
```
Every time we declare a variable, we must write `struct Person variable_name;`.

✅ **Using `typedef` simplifies this:**
```c
typedef struct Person {
    char name[50];
    int age;
    float height;
} Person;
```
Now, we can declare a variable with:
```c
Person person1;
```
This makes the code **cleaner and more readable**.

---

## 📂 Nested Structures
A structure can contain another structure as a member.

### **Example: Address in a Person Structure**
```c
typedef struct Address {
    char street[100];
    char city[50];
    int zipcode;
} Address;

typedef struct Person {
    char name[50];
    int age;
    Address address;
} Person;
```
#### **Accessing Nested Structure Members**
```c
Person person1 = {"Alice", 25, {"123 Main St", "New York", 10001}};
printf("City: %s\n", person1.address.city);
```

---

## 🔄 Passing Structures to Functions
### **Pass by Value (Copy of Structure)**
```c
void printPerson(Person p) {
    printf("%s is %d years old.\n", p.name, p.age);
}

int main() {
    Person p = {"Alice", 25};
    printPerson(p);
    return 0;
}
```

### **Pass by Reference (Using Pointers)**
```c
void updateAge(Person *p) {
    p->age += 1; // Using '->' to access members via pointer
}

int main() {
    Person p = {"Alice", 25};
    updateAge(&p);
    printf("Updated Age: %d\n", p.age);
    return 0;
}
```
Using pointers **avoids copying large structures** and allows modification of original values.

---

## 🛠 Memory Management with `malloc`
You can **dynamically allocate structures** using `malloc`.

```c
Person *p = malloc(sizeof(Person));
p->age = 30;
strcpy(p->name, "Bob");
free(p); // Always free allocated memory
```

---

## 🚨 Structure Packing & Alignment (Advanced)
By default, compilers **align** structures for performance. This may introduce **padding bytes**, increasing the structure size.

### **Example: Structure with Padding**
```c
typedef struct {
    char a;   // 1 byte
    int b;    // 4 bytes (with 3 bytes padding after 'a')
} PaddedStruct;
```
💡 **Solution: Use `__attribute__((packed))`**
```c
typedef struct __attribute__((packed)) {
    char a;
    int b;
} PackedStruct;
```
🔹 This removes **extra padding** but may affect performance.

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
- **Allowed standard functions**: `printf`, `malloc`, `free`, `exit`.
- **All header files should be include guarded (`#ifndef #define #endif`).**

---

## 🚀 Conclusion
- Structures allow **grouping multiple data types** into a single entity.
- `typedef` **simplifies declarations** and improves readability.
- **Passing by reference** is more efficient for large structures.
- **Dynamic allocation** allows flexible memory usage.
- **Memory alignment** affects structure size and efficiency.

🎯 **Next Steps:**
- Implement structures for real-world data (e.g., student records, employee databases).
- Practice **pointers to structures**.

🚀 **Happy coding!**

