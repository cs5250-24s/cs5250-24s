# Introduction

!!! info

    Please read the [general instructions](../index.md) first.

    The assignment is due **17:59 on 1 Apr 2024**.
    Late submissions will lose **1 mark per hour**.

    If you discover any errors or need any clarification or have any question,
    please create an issue [here](https://github.com/nus-cs5250/24s/issues).

## Part A (35 marks)

!!! note

    - You will be working with Linux kernel version 6.7.0.
    - Start with the default configuration and apply the following updates:

      1. Enable the following features in your kernel configuration:
        - `CONFIG_DEBUG_INFO_DWARF_TOOLCHAIN_DEFAULT`
      2. If you require VirtualBox shared folders, ensure the following options
         are enabled in your kernel configuration:
        - `CONFIG_VBOXGUEST`
        - `CONFIG_VBOXSF_FS`
      3. Additional features can be enabled as necessary.
         However, ensure to preserve a copy of your kernel configuration and
         include it in the submitted zip file.

### [Task 1: Hook System Calls with KProbes](task-kprobe.md)

<!-- 2 + 2 + 5 + 2 + 3 + 6 = 20 -->

**Question 1 (2 marks):**
What is the name of the system call that the game uses to keep track of time?

Please submit your response as a field in the YAML file (specified below).

**Question 2 (2 marks):**
Give the complete command to run `strace` so that:

- The output is written to a file.
- Only the system call that the game uses to keep track of time is shown.
- It's fine if there are some other outputs not related to system calls, e.g.,
  information about signals or exit status.

Please submit your response as a field in the YAML file (specified below).

**Question 3 (5 marks):**
At line 23 of the sample kernel module, the `kprobe` is set to hook the `regs =
(struct pt_regs *)regs->di`. Why do we need such an indirection to get the
arguments of the system call?
Hint:
[[How Does a Kprobe Work?]](https://www.kernel.org/doc/html/v6.7/trace/kprobes.html#how-does-a-kprobe-work)
[[Notes on System Entry]](https://canvas.nus.edu.sg/courses/53045/files/3626447?module_item_id=318796)

Please write your response in a Markdown file.

**Question 4 (2 marks):**
What is the GDB command to print the entry in the syscall table for the system
call you identified in the previous question?

Please submit your response as a field in the YAML file (specified below).

**Question 5 (3 marks):**
What is the problem with the address of the system call service routine? How
can you fix this problem?

Please write your response in a Markdown file.

**Question 6 (6 marks):**
Please provide the source code of the modified kernel module.
_Check the task description for the requirements of the program._

Please submit your code in a file named `hook.c`.

### [Task 2: Memory Manipulation in Tetris Game](task-memhack.md)

<!-- 4 + 5 + 3 + 3 = 15 -->

**Question 7 (4 marks):**
In which memory regions is it likely that the score is stored?

1. Provide a complete copy of the output of `/proc/[pid]/maps` for the `tetris`
   process.
2. List the memory regions that you believe are probable candidates for storing
   the score.
3. Explain your rationale for why these memory regions are likely to contain the
   score.

Please write your response in a Markdown file.

**Question 8 (5 marks):**
Please submit the source code of the program that finds the address of the score
in the `tetris` process. You can write the program in C or Python.
_Check the task description for the requirements of the program._

Please submit your code in a file named `find-score.c` or `find-score.py`.

**Question 9 (3 marks):**
Please submit the source code of the C program that modifies the score in the
`tetris` process.
_Check the task description for the requirements of the program._

Please submit your code in a file named `modify-score.c`.

**Question 10 (3 marks):**
Please discuss security concern with a LLM such as ChatGPT.
Ask if there are any methods to prevent such unwanted actions.
Evaluate the provided response for its effectiveness.
If multiple solutions are suggested, choose only ONE of them to discuss.

Please write your response in a Markdown file.

### Submission Guidelines

Here's the template for your YAML file `saq.yaml`, including your responses to
Questions 1 and 3.
All the values are placeholders and should be replaced with your responses.

```YAML
matric_no: "A0123456A"
qn1_syscall: "..."
qn2_strace: "strace ..."
qn4_gdb: "print ..."
```

Your submission should be a zip file containing the following files:

| Filename                          | Description                                                                                      |
| --------------------------------- | ------------------------------------------------------------------------------------------------ |
| `saq.yaml`                        | Your response to Question 1, 2 and 4, use the template above.                                    |
| `qn3.md`                          | Your response to Question 3 in Markdown format.                                                  |
| `qn5.md`                          | Your response to Question 5 in Markdown format.                                                  |
| `hook.c`                          | Source code of the modified kernel module.                                                       |
| `qn7.md`                          | Your response to Question 7 in Markdown format.                                                  |
| `find-score.c` or `find-score.py` | Source code of the program searching for the address of score.                                   |
| `modify-score.c`                  | Source code of the program modifying the score.                                                  |
| `qn10.md`                         | Your response to Question 10 in Markdown format.                                                 |
| `.config`                         | (Optional) Kernel configuration, if you have enabled additional features beyond those specified. |

Please submit your zip file to
[Assignment 3 (Part A)](https://canvas.nus.edu.sg/courses/53045/assignments/108790)
on Canvas.

## Part B (15 marks)

Please complete the quiz
[Assignment 3 (Part B)](https://canvas.nus.edu.sg/courses/53045/quizzes/37036)
on Canvas.
