# Task 2: Memory Manipulation in Tetris Game

!!! note

    In this task, you'll be using the same `tetris` binary as in
    [Task 1](task-kprobe.md).

The game might not hold much appeal, but I'm eager to impress my friends with an
incredibly high score. Let's surprise them by modifying the score in the game.

The score is stored as a `int32_t` variable in memory. We'll locate the address
of the score and update it to a large number.

## Memory Regions

In order to modify the score, we need to find the address of the score in the
`tetris` process.

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

Merely identifying suspicious memory regions isn't sufficient; pinpointing the
exact address of the score in the `tetris` process is necessary.
To find the exact address of the score in the `tetris` process, we can play the
game briefly, pause it, and then search through these regions for the score
value.
If a word in a memory region matches the score value, it's likely where the
score is stored.
This involves digging into the `tetris` process's virtual memory to gather the
necessary information.

Under procfs, there's a file `/proc/[pid]/mem` that allows you to read and
write to the virtual memory of a process. Please refer to the man page of
[`proc`](https://man7.org/linux/man-pages/man5/proc.5.html) and read the section
about `/proc/[pid]/mem`.

!!! tip

    No need to worry about permission issues in this task.
    You can use `sudo` to read and write to `/proc/[pid]/mem`.

Here's a demo to help you understand how to hack into the virtual memory of a
process via `/proc/[pid]/mem`.

<script src="https://gist.github.com/shen-jiamin/62e554df61510efe7095ad8335e1346b.js?file=demo-hacker.c"></script>

<script async id="asciicast-viIoLKiGb0HKNsIlErYFtiYFC" src="https://asciinema.org/a/viIoLKiGb0HKNsIlErYFtiYFC.js"></script>

Please write a program in **C or Python** to locate the address of the score in
the `tetris` process. The program should accept a single command-line argument,
which is the process ID of the `tetris` process.

The program should first read the `maps` file of the `tetris` process to
determine the potential memory ranges where the score might be stored. Then, it
should read the score value from stdin and scan through the identified memory
regions to locate the score value. If it finds a memory location holding the
score value, it should print out the address of that memory location.

!!! tip

    The actual pathname of the executed command in a process can be found by
    reading the symbolic link `/proc/[pid]/exe`.

    ```console
    $ ls -al /proc/$$/exe
    lrwxrwxrwx 1 shenjm shenjm 0 Mar 13 00:16 /proc/73799/exe -> /usr/bin/zsh*
    ```

    [Here](https://wiki.sei.cmu.edu/confluence/display/c/POS30-C.+Use+the+readlink%28%29+function+properly)
    is an example of how to use the `readlink` function to read the symbolic link.

It's possible that within these regions, there are multiple words containing the
same value as the score. To eliminate false positives, you can continue playing
the game and pause it again to observe how these words change. If you find
memory locations that don't change as expected when the score updates, you can
exclude them from consideration and finally find the actual address of the
score.

## Hack the Score

Now that you have determined the address of the score, you can proceed to modify
it to a larger number, such as 1000000.

Please update your program as follows:

1. Accept one additional command-line argument: the new score.
2. Once the address of the score is found, write the new score to that memory
   location.

Finally, you should be able to perform the hacking using a single command:

```console
$ sudo ./hack-tetris <pid> <target-score>
```

!!! question

    Please submit the source code of the program written in either C or Python.
    If you choose Python, ensure that the first line of your program is `#!/usr/bin/env python3`.

Please note: any compilation errors, syntax errors, or runtime errors will
result in a score of 0 for this task.

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
