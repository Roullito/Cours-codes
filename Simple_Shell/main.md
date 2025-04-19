## Function: main (main.c)

```c
int main(int ac, char **av, char **env)
{
    (void)ac;
    (void)av;
    shell_loop(env);
    return (0);
}
```

### Line-by-Line Explanation

#### `int main(int ac, char **av, char **env)`
- Entry point of every C program.
- `ac`: Argument count (how many arguments passed to the program). Not used here, but required by convention.
- `av`: Argument vector (array of strings representing arguments). Also unused in this implementation.
- `env`: Environment variables. This is the important parameter here. We pass this to `shell_loop` so our shell knows the environment.

#### `(void)ac; (void)av;`
- We explicitly mark these variables as unused to prevent compiler warnings.

#### `shell_loop(env);`
- This is where the main logic of our shell begins.
- `shell_loop` is responsible for:
  - Prompting the user (in interactive mode)
  - Reading user input
  - Parsing input into command + arguments
  - Checking if the command is a built-in
  - Executing it or calling execve

#### `return (0);`
- Signals successful execution of the program to the operating system.
- Standard convention for exiting a C program.

---

### What happens when we run ./simple_shell?
1. `main()` is called.
2. We ignore `ac` and `av`.
3. We call `shell_loop()` and pass it the current environment.
4. Once the loop breaks (either via EOF or built-in `exit`), we return 0 and the shell closes.

---

### Summary
- `main` is very minimal.
- Its sole job is to launch the `shell_loop`, which handles the rest.
- This respects the separation of concerns and keeps `main` under 40 lines and < 5 functions per file rule (Holberton norm).

