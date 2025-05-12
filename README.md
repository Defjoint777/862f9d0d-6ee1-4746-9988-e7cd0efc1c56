<!---
{
  "depends_on": ["https://github.com/STEMgraph/8d3e51c2-4811-4275-976c-04e3b3215998"],
  "author": "Stephan Bökelmann",
  "first_used": "2025-03-17",
  "keywords": ["shell", "environment"]
}
--->

# Interacting with the Shell: The Environment

## 1) Introduction

As you begin to write your own shell functions and automate tasks in the command line, it's crucial to understand how these functions—and the variables they rely on—behave within the terminal environment. 

Defining custom functions or variables directly in your current shell session may feel intuitive and efficient, but it introduces certain risks. One of the most critical is the potential for **name collisions**: when you *source* a file containing functions or identifiers, you might unintentionally **overwrite existing definitions**, causing unpredictable behavior. This becomes especially problematic when code is shared between systems, reused in different contexts, or integrated into larger scripts.

While it's sometimes valid to override existing definitions on purpose, in most cases it is safer and more maintainable to execute functions within **isolated or encapsulated environments**. Doing so helps protect against side effects, preserves modularity, and ensures that changes in one environment do not leak into another.

In this chapter, you will learn how to:

- Recognize the **ephemeral** nature of variables in shell sessions,
- Use **nested terminal sessions** to create isolated environments,
- Explore how shell level tracking works via the special variable `$SHLVL`,
- Distinguish between **local identifiers** (visible only within the current shell) and **global identifiers** (exported to subprocesses),
- Understand the concept of a process **environment**, and how environment variables are inherited,
- Safely pass configuration or data to subprocesses without polluting the parent shell environment.

By working through practical exercises, you'll gain hands-on experience with variable scoping and shell session management. These foundational skills are essential for writing reliable, reusable, and portable shell scripts—whether you're automating tasks on a single machine or deploying scripts across heterogeneous systems.

Let’s begin by exploring how simple variable assignments behave across nested shell sessions and build our understanding from there.


## 2) Tasks 

1. **Local Identifiers and Nested Shells**: Open a new terminal emulator and assign a value to an identifier and inspect it with the `echo` command. After that, close the terminal by calling `exit`:

```bash
my_var=42
echo $my_var
exit
```

2. **Persistence**: Open a **new terminal**, and inspect the value again:

```bash
echo $my_var
```

You should see an empty result — the variable `my_var` was only defined in the original session.

3. **Session within a Session**: Run `bash` from inside your terminal. It might look like nothing happened — but in fact, you've launched a new terminal session **within** the current one! Call `exit` in this shell and you'll return to the parent shell session.  Try nesting a few shells using `bash` repeatedly, then type `exit` until all sessions are closed. This creates a **stack** of terminal sessions — also called **nested shells**. Commands are always executed in the **innermost** shell. The termination of sessions follows **Last-In-First-Out (LIFO)**, a concept we’ll revisit often. To inspect the current nesting level, run `echo $SHLVL`. This environment variable (`SHLVL`) tracks the number of nested shells.

<details>

  <summary>What's bash?</summary>

  `bash` stands for **Bourne Again Shell**, based on the original Bourne shell and the standard command-line interpreter for many systems (Linux, MacOS, Windows via WSL or Git Bash).

  Different OSs may use different shells (e.g., `zsh` on MacOS, `CMD` or `PowerShell` on Windows), but the **core ideas are shared**. Bash remains the **lingua franca** of shell scripting due to its widespread adoption.

</details>

4. **Understanding Local Scope**: Open a new terminal and define an identifier: `my_var=42`. Now start a nested shell and inspect the value of the identifier using `echo`. Observe the output and exit the nested session. Now inspect the value again in the parent session by using `echo`.


5. **Global Scope** : Start a fresh terminal and define an identifier: `my_var=42`. Now use the `export` function to make this a `global identifier` which shall be visible in all nested shells `export my_var`. Start a nested shell and inspect the value again.

<details>

  <summary>Trick!</summary>

  You can also define and export in one line:

  ```bash
  export my_var=42
  ```

</details>


6. **The Shared Environment**: Exported identifiers are visible in all **nested subshells** and **subprocesses**. That’s why we call them **global identifiers**. The memory region that holds all exported identifiers is called the **environment**. All subprocesses share this environment. You can inspect it using the `env` command.

## 3) Questions
- Why do you think nested terminal sessions are useful?
- we get flexability to deside which variables should be globalized ahd which dont.
- 
- What happened to the identifier `my_var`?
- first it was visible only in the season of Bash and after we globalized the variable. That means we took a normal variable from one place in memory to otherone.
- 
- Can you explain it using the term **local identifier**?
- it means if we make 1 variable to local identifier this variable is availible in all subshells and lands in [env] part of Bash.
- 
- What happened to `my_var`?
- its became vissible to all "Kidsprocesses" or subprocesses.
- 
- Use the term **global identifier** in your explanation.
- to make one Variable to global identifier we need to export this variable in env part of Shell


## 4) Advice
However, exporting identifiers isn’t always necessary or advisable. Sometimes it’s cleaner to define them **only in the subprocess**, either by:

- opening a new shell and defining it there, or
- prepending the definition to the process call:

```bash
my_var=42 my_process
```
