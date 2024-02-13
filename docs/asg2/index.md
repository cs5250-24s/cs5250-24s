# Introduction

!!! info

    Please read the [general instructions](../index.md) first.

    The assignment is due **17:59 on 4 Mar 2024**.
    Late submissions will lose **1 mark per hour**.

## Part A (35 marks)

!!! info

    You will be working with Linux kernel version 6.7.0.
    Please start with the default configuration and make the following
    modifications:

    - Activate the `DEBUG_INFO_DWARF_TOOLCHAIN_DEFAULT` option.
    - Should you require VirtualBox shared folders, ensure that `CONFIG_VBOXGUEST`
      and `CONFIG_VBOXSF_FS` are enabled in your kernel configuration.
      These settings are necessary for the proper functioning of VirtualBox guest
      additions, including shared folders support.

    Feel free to enable additional features as needed.
    However, please make sure to save a copy of your kernel configuration and
    include it in the zip file you submit.

### [Task 1: Developing a Kernel Module](task-module.md)

<!-- 11 marks -->

**Question 1 (2 marks):**
Identify the file and line number where the `module_init` macro is defined for
our scenario.
Please put your response in the relevant field of the JSON file elaborated
below.

For example, if it were defined in
[`kernel/sched/core.c`](https://elixir.bootlin.com/linux/v6.7/source/kernel/sched/core.c#L6568)
at line 6568, you should refer to it as `kernel/sched/core.c#L6568`.

!!! warning

    Make sure that you refer to the v6.7.0 version of the kernel source code.

**Question 2 (2 marks):**
Why is `printk` used instead of `printf` within kernel modules?
Please put your response in a file named "qn2.md".

**Question 3 (3 marks):**
Did you observe the output "Greetings from xxx" when you loaded the module?
If not, is this string compiled into the module?
Provide a brief explanation of how to make the message at the "DEBUG" level
appear in the kernel log.
Please put your response in a file named "qn3.md".

**Question 4 (4 marks):**
Submit the source code for your kernel module in a file named "modcpid.c".
This module needs to take two parameters and output the process ID and
executable name for the given PID.

- Ensure the module compiles without error using the provided Makefile.
- The module must be able to load and unload without any error.
- The first line of your source code must indicate the command used for loading
  the module with the proper parameters.

### [Task 2: Creating a New System Call](task-syscall.md)

[//]: # (12 marks)

<!-- 12 marks -->

**Question 5 (8 marks):**
Please provide the patch file for the changes you made to the kernel source code
to create the new system call. The patch file should have suffix `.patch` and
adhere to the following requirements:

- The patch file must be able to be applied to the v6.7.0 version of the kernel
  source code without any errors. If the patch file cannot be applied, you will
  receive zero marks for this question.
- The patched kernel must be able to compile without any errors. If the patched
  kernel cannot compile, you will receive zero marks for this question.

**Question 6 (4 marks):**
Please provide the source code for your user-mode program in a C file named
`getcpid.c`.
This program should take the PID of a process as input, invoke the new system
call you created, and output the PIDs of all its children, one per line.
Please ensure that the program compiles without error.

### [Task 3: Debugging with GDB](task-gdb.md)

<!-- 12 marks -->

**Question 7 (4 marks):**
Which figure in the ABI document supports your findings on the stack layout?
Please put your response in the relevant field of the JSON file.

**Question 8 (4 marks):**
Which interrupt line (IRQ) is allocated for this specific interrupt?
Please put your response, an integer, in the relevant field of the JSON file.

**Question 9 (4 marks):**
What is the name of the `clock_event_device` that processes the event
triggered by this interrupt, as identified by the
[`name` field](https://elixir.bootlin.com/linux/v6.7/source/include/linux/clockchips.h#L125)
in its structure?

Please put the name of the `clock_event_device` you found in the relevant field
of the JSON file.

### Submission Guidelines

Here's the template for your JSON file `saq.json`, including your responses to
Questions 1, 7, 8, and 9.
All the values are placeholders and should be replaced with your responses.

```json
{
  "matric_no": "A0123456A",
  "qn1/module_init": "/path/to/file#L1234",
  "qn7/stack_layout": "Figure X.Y",
  "qn8/irq": 48,
  "qn9/clock_event_device": "name-of-the-device"
}
```

Your submission should be a zip file containing the following files:

| Filename    | Description                                                                                            |
| ----------- | ------------------------------------------------------------------------------------------------------ |
| `saq.json`  | Your response to Question 1, 7, 8, and 9 in JSON format. Use the template above.                       |
| `qn2.md`    | Your response to Question 2, formatted in Markdown.                                                    |
| `qn3.md`    | Your response to Question 3, formatted in Markdown.                                                    |
| `modcpid.c` | The source code of your kernel module.                                                                 |
| `*.patch`   | The patch file including your implementation of the syscall `getcpid`.                                 |
| `getcpid.c` | A C program that prints the PIDs of all children of a given process.                                   |
| `.config`   | (Optional) Kernel configuration, if you have enabled additional features beyond those specified above. |

You can verify the contents of your zip file using the `unzip -l` command.
Please note that the files listed above are for illustrative purposes only.
The actual files in your submission should not be empty.

```console
$ unzip -l asg2.zip
Archive:  asg2.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
        0  2024-02-11 17:06   saq.json
        0  2024-02-11 17:06   qn2.md
        0  2024-02-11 17:06   qn3.md
        0  2024-02-11 17:06   modcpid.c
        0  2024-02-11 17:06   xxxxxxxxxx.patch
        0  2024-02-11 17:06   getcpid.c
---------                     -------
        0                     6 files
```

!!! warning

    Your zip file shall have only one file ending with ".patch".
    If you have more than one file ending with ".patch", you will lose additional marks.

Please submit your zip file to
[Assignment 2 (Part A)](https://canvas.nus.edu.sg/courses/53045/assignments/105001)
on Canvas.

## Part B (15 marks)

Please complete the quiz
[Assignment 2 (Part B)](https://canvas.nus.edu.sg/courses/53045/quizzes/35637)
on Canvas.
