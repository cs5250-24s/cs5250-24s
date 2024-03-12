# Task 2: Memory Manipulation in Tetris Game

!!! note

    In this task, you'll be using the same `tetris` binary as in
    [Task 1](task-kprobe.md).

The game might not hold much appeal, but I'm eager to impress my friends with an
incredibly high score. Let's surprise them by modifying the score in the game.

The score is stored as a `int32_t` variable in memory. We'll locate the address
of the score and update it to a large number.

## Memory Regions

The memory regions mapped to a process can be found in `/proc/[pid]/maps`. For
instance, to view the memory regions of the `bash` process:

```console
$ pgrep bash
769525
$ cat /proc/769525/maps
<output omitted here>
```

Please check the manual page of
[`proc`](https://man7.org/linux/man-pages/man5/proc.5.html) for the meaning of
the columns.

Run the `tetris` game and pause it. Find its process ID using the `ps` or
`pgrep` command. Then, print the memory regions of the `tetris` process.

!!! question

    In which memory regions is it likely that the score is stored?

    1. Provide a complete copy of the output of `/proc/[pid]/maps` for the
       `tetris` process.
    2. List the memory regions that you believe are probable candidates for
       storing the score.
    3. Explain your rationale for why these memory regions are likely to
       contain the score.

## Find the Address of the Score

We can find the specific address of the score by searching the memory regions of
the `tetris` process for the value of the score.

Under procfs, there's another file `/proc/[pid]/mem` that allows you to read and
write to the virtual memory of a process. Please refer to the man page of
[`proc`](https://man7.org/linux/man-pages/man5/proc.5.html) and read the section
about `/proc/[pid]/mem`.

!!! tip

    No need to worry about permission issues in this task. You can use
    `sudo` to read and write to `/proc/[pid]/mem`.

You can play the game for a while and then pause it. Then search through the
memory regions (just look at the memory regions that you found suspicious in the
previous question) and find a word that has the same value as the score. If
there are multiple words with the same value, you can continue playing the game
and pause it again to see how these words change.

Please write a program to find the address of the score in the `tetris` process.
You can use **C or Python** to write the program.

The program accepts a single command-line argument, which is the process ID of
the `tetris` process. It first prints the process ID to stdout. Then, it reads
the `maps` file of the `tetris` process to identify the range of memory
addresses where the score might be stored. Next, it reads the value of the score
to search from stdin and examines the memory regions of interest for this value.
If there are multiple suspicious memory locations, it prompts for a new score
and repeats the process, ruling out memory locations that do not match the
expected value. This continues until the program identifies the sole memory
location consistently holding the score value. Finally, it prints the address of
the score to **both** stdout and stderr.

Please note:

- Your program should print and only print 2 lines to stdout: the process ID and
  the address of the score. All other outputs, should go to stderr.
  - Process ID shall be printed as a decimal number followed by a newline.
  - The address of the score shall be printed as a hexadecimal number prefixed
    with `0x` followed by a newline.
- If the process ID is invalid or it fails to find the address of the score,
  your program should print an error message to stderr and exit with a non-zero
  exit code.
- If you use Python, the first line of your program MUST be `#!/usr/bin/env python3`.

!!! question

    Please submit the source code of the program in C or Python.

## Modify the Score

Now that you have found the address of the score, you can modify the score to a
large number, e.g., 1000000.

Please write a **C program** to modify the score in the `tetris` process. The
program accepts only one command-line argument: the new score. It should read
the process ID and the address of the score from stdin. Then it modifies the
score to the new score. If the modification is successful, it exits with a zero
exit code. Otherwise, it exits with a non-zero exit code.

!!! question

    Please submit the source code of the C program.

Finally, you should be able to do the hacking using one command, similar to:

```console
$ sudo ./find-score <pid> | sudo ./modify-score <new-score>
```
## I'm So Scared!

In this task, we demonstrated how a root user can read and write to the virtual
memory of a process running on the system, posing a security risk. If you are
using a shared system like the [SoC Compute
Cluster](https://dochub.comp.nus.edu.sg/cf/services/compute-cluster), the system
administrator might potentially access your process and extract sensitive
runtime information as well.

!!! question

    Please discuss security concern with a LLM such as ChatGPT.
    Ask if there are any methods to prevent such unwanted actions.
    Evaluate the provided response for its effectiveness.
    If multiple solutions are suggested, choose only ONE of them to discuss.
