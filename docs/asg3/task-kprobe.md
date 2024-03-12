# Task 1: Hook System Calls with KProbes

!!! note

    - Download the executable `tetris` from the link below:
      [Tetris Executable](https://drive.google.com/drive/folders/1CRxxn9FHcAwXgMPwo-yFGdfSuOUv4qkm?usp=sharing)
    - The SHA-256 hash of the file is provided below for verification:
      You can regenerate the hash using the `sha256sum` command and compare it
      manually, or use the provided `tetris.sha256` file to verify the hash.
      ```
      $ sha256sum tetris
      22cab9532516f63a6d3541ab7d9fd0dcd437881e0293670679e06c300ab9c3b5  tetris
      $ sha256sum -c tetris.sha256
      tetris: OK
      ```
    - The executable is a statically linked 64-bit ELF binary.
      It should execute without issues on your Ubuntu system.
      If any problems arise, please report them
      [here](https://github.com/nus-cs5250/24s/issues).

Before beginning, execute the `tetris` binary and get familiarized with the
game.

## How Time Flies

In this game, the bricks fall from the top of the screen by one row at a
specific time interval. As you can imagine, there should be some mechanism to
keep track of time and trigger the movement of the bricks. This can be achieved
in two general ways:

- One approach is to use a loop, where the program sleeps for a specific time
  interval and then moves the bricks. In this method, the program invokes a
  `sleep` system call to pause execution for the desired interval.
- Another approach involves using a timer to prompt the movement of the bricks.
  In this method, the program sets up a timer and registers a callback function
  to execute when the timer expires.

Since both methods require the use of system calls, let's use `strace` to
observe how the game tracks time.

You can get an initial idea of `strace` by running the following command:

```
strace cat /dev/null
```

You should see a list of system calls made by the `cat` command. The arguments
and return values of the system calls are also printed.

However, if you run `strace ./tetris` directly, the output will be corrupted due
to interleaved output both from the game and `strace`. So you should write the
output of `strace` to a file and later inspect it.

```
strace -o <filename> ./tetris
```

Give a filename of your choice to the `-o` option, execute the command, and
enjoy the game for a while. Then press `q` to quit the game and inspect the
output file.

!!! question

    What is the name of the system call that the game uses to keep track of time?

Please read the man page of the system call or ask ChatGPT if you need to learn
how to use it. Write a small program to demonstrate the use of the system call.
You will use this program later.

The `strace` outputs many system calls. To facilitate the analysis, some flag
can be used to filter the output. Read the man page of `strace` to find the flag
that can be used to filter the output of `strace` to only show the system call
you identified in the previous question.

!!! question

    Give the complete command to run `strace` so that:

    - The output is written to a file.
    - Only the system call that the game uses to keep track of time is shown.
    - It's fine if there are some other outputs not related to system calls,
      e.g., information about signals or exit status.

## Wait! It's Too fast!

Now that you have identified the system call used by the game to keep track of
time, let's try to slow down the passage of time, giving you more opportunities
to make decisions.

To achieve this, we'll employ a kernel feature known as KProbes. KProbes allows
inserting a probe at a specific kernel address and registering a callback
function to be invoked when the probe is hit. This callback function can modify
the arguments and return value of the probed function, or even bypass its
execution.

### A First Glance at KProbes

To use KProbes, you'll need to develop a kernel module. Here's a simple example
of a kernel module using KProbes to intercept the `__do_sys_ni_syscall` system
call and print the arguments passed to it.

<script src="https://gist.github.com/shen-jiamin/62e554df61510efe7095ad8335e1346b.js?file=hook.c"></script>

Lines 16-18 of the code implement a simple filter, ensuring the probe triggers
only when the `current` process has a `comm` field matching the specified string
argument `comm`. When loading the module, you must provide an appropriate value
to the `comm` argument to ensure the probe triggers only when the intended
program is running.

Here's a user program that invokes the `__do_sys_ni_syscall` system call.

<script src="https://gist.github.com/shen-jiamin/62e554df61510efe7095ad8335e1346b.js?file=test.c"></script>

After building and installing the kernel module, execute the user program.
Subsequently, inspect the kernel log to observe the output of the kernel module.
You will find the arguments passed to the `__do_sys_ni_syscall` system call
there.

```
hook: --- [[ Module loaded ]] ---
hook: pre_handler: __do_sys_ni_syscall@000000009e4e93a8
hook: triggered in test [29832]
hook: regs->di  = 0x10
hook: regs->si  = 0x20
hook: regs->dx  = 0x30
hook: regs->r10 = 0x40
hook: regs->r8  = 0x50
hook: regs->r9  = 0x60
hook: --- [[ Module unloaded ]] ---
```

!!! question

    At line 23 of the kernel module, we have
    `regs = (struct pt_regs *)regs->di`.
    Why do we need such an indirection to get the arguments of the system call?

    Hint:

    - [How Does a Kprobe Work?](https://www.kernel.org/doc/html/v6.7/trace/kprobes.html#how-does-a-kprobe-work)
    - [Notes on System Entry.pdf](https://canvas.nus.edu.sg/courses/53045/files/3626447?module_item_id=318796)

### First Attempt

To hook the system call, you need to find the symbol name of the service routine
implementing the system call. You've done this in Pop Quiz 6, and you can do it
again. However, with the `vmlinux` file available, you can use `gdb` to find the
symbol name of the service routine.

```console
$ gdb vmlinux

<some output omitted here>

(gdb) print sys_call_table
$1 = {...}
```

This command prints the contents of the `sys_call_table` variable, which is a
lengthy array of function pointers. To locate the symbol name of the service
routine, you can look up the syscall table to find the syscall number of the
system call and then print only that entry in the syscall table.

!!! question

    What is the GDB command to print the entry in the syscall table for the
    system call you identified in the previous question?

Having identified the symbol name of the service routine, you can proceed to
modify the kernel module to hook the desired system call. After modifying the
kernel module, build it and load it into the kernel. In a previous step, you
wrote a small program to demonstrate the use of the system call. Now, execute
the program and review the kernel log to observe the output.

Take note of the address of the system call service routine. You may find that
it does not fall within the region of the "kernel text", as indicated in the
[memory layout
documentation](https://www.kernel.org/doc/html/v6.7/arch/x86/x86_64/mm.html).
Why is this the case? How can you fix this problem?

!!! question

    What is the problem with the address of the system call service routine?
    How can you fix this problem?

The subsequent step involves writing code to extend the duration before the next
movement of the bricks. This can be accomplished by modifying the arguments of
the system call. However, the important argument is passed as a pointer,
pointing to a user space address. Directly changing the value in user space
might trigger the game's cheat detection mechanism, leading it to refuse
execution. Therefore, it would be better if we modify the value in kernel space.
However, the original service routine assumes that the pointer points to a valid
user space address. If it turns out that the pointer points to an address in
kernel space, the original service routine could crash. This presents a
challenging scenario!

### Second Attempt

Let's find a workaround. Find the definition of the system call in the kernel
source code and observe how it handles the pointer argument. Can you identify
another function to hook where the important argument is already in kernel
space?

Modify the kernel module to:

- Hook the new function you found, simplifying the task.
- Increment the duration by one second.
- Adjust the `MODULE_AUTHOR` to your Matriculation Number.

After loading the kernel module, run the game and observe if the time flows slower.

!!! question

    Please provide the source code of the modified kernel module.
