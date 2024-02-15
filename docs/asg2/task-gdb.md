# Task 3: Debugging with GDB

!!! info

    Whenever we mention the "ABI documentation", we are referring to the
    document available at
    <https://refspecs.linuxfoundation.org/elf/x86_64-abi-0.99.pdf>.
    This link is also available on Canvas.

!!! info

    We assume you have some basic familiarity with GDB.
    If GDB is completely new to you, consider reviewing these resources from
    CS:APP beforehand:

    - Beej's Guide to GDB:
      <https://beej.us/guide/bggdb/>
    - An x86-64 GDB Cheat Sheet:
      <http://csapp.cs.cmu.edu/3e/docs/gdbnotes-x86-64.txt>

    For additional support, you might find the following resources helpful:

    - The Official GDB Manual, *Debugging with GDB*:
      <https://sourceware.org/gdb/current/onlinedocs/gdb/>

## Examining the Stack

Write a C program by creating a file named `hello.c` with the following content:

```c
// hello.c
#include <stdio.h>

int main() {
    printf("Hello, world!\n");
    return 0;
}
```

Compile the program using the command below.
This command compiles your program with debug information, which is crucial for
debugging.
Run the compiled program using GDB with the command:

```
gcc -Og -g -o hello hello.c
gdb ./hello
```

At this point, the program is loaded into GDB but has not started executing.
To begin execution up to the start of the `main` function, use the `start`
command.

```
(gdb) start
```

Upon executing the `start` command, GDB will output something similar to:

```
Temporary breakpoint 1, main () at hello.c:4
```

This message indicates that a temporary breakpoint has been set at the beginning
of the `main` function and the program is paused at this point.
You can examine the state of the program at this breakpoint.

To view the register values at this point, use the `info registers` command in
GDB.
This command displays the current values of all registers.

```
(gdb) info registers
```

To examine the first 8 values on the stack in hexadecimal format, use the
`x/8xg $rsp` command.
This command uses the `x` command to examine memory and prints 8 quadwords (`g`
stands for giant/quadword) in hexadecimal format from the location pointed to by
the stack pointer (`rsp`).

```
(gdb) x/8xg $rsp
```

Refer to the Figure 3.4 in the ABI documentation.
Find out the values stored in the registers that are intended for passing the
first three arguments to the `main` function.

To exit GDB, use the `quit` command.

Restart the `hello` program in GDB, this time using the `starti` command.
You'll notice the program pauses at `_start` instead of `main`.
What is the difference between `start` and `starti`?

Then, output the first few quadwords on the stack.
Among these, you'll identify values that appear to be addresses, not too far
from the stack's start.
Try printing the content at these addresses using various formats to understand
what are they.
For help with format specifiers, check the GDB manual's section on
[Examining Memory](https://sourceware.org/gdb/current/onlinedocs/gdb.html/Memory.html).

Finally, check the ABI documentation to find the figure or table that supports
your observations.

## Attach GDB to Kernel via QEMU

!!! info

    For more details on QEMU's
    [command-line options](https://www.qemu.org/docs/master/system/invocation.html)
    and
    [key bindings](https://www.qemu.org/docs/master/system/mux-chardev.html)
    in the character backend multiplexer,
    you can refer to the QEMU documentation.

Launch QEMU with the command below to start your Linux kernel.
This command sets up QEMU to wait for a debugger connection.
KASLR is disabled to simplify debugging.

```
qemu-system-x86_64 -s -S -nographic -kernel bzImage -append 'console=ttyS0 nokaslr'
```

Next, open GDB to connect to the running QEMU instance by using the command:

```
gdb vmlinux
```

Inside GDB, connect to QEMU with:

```
(gdb) target remote localhost:1234
```

This allows you to set breakpoints, continue execution, and inspect the kernel's state.
Let's monitor changes to the `jiffies_64` variable.
Use the `watch` command:

```
(gdb) watch jiffies_64
```

Continue the execution with:

```
(gdb) continue
```

The execution will pause when `jiffies_64` changes.
To analyse how `jiffies_64` is updated, use commands `info stack` or `bt` to
print the call stack.

```
(gdb) bt
           <ommited>
#7  0xffffffff810d2bac in handle_level_irq (desc=0xffff888003860c00) at kernel/irq/chip.c:648
#8  0xffffffff8102c4c9 in generic_handle_irq_desc (desc=<optimized out>) at ./include/linux/irqdesc.h:161
#9  handle_irq (regs=<optimized out>, desc=<optimized out>) at arch/x86/kernel/irq.c:238
#10 __common_interrupt (regs=<optimized out>, vector=48) at arch/x86/kernel/irq.c:257
#11 0xffffffff81de17a6 in common_interrupt (regs=0xffffffff82803e18, error_code=<optimized out>) at arch/x86/kernel/irq.c:247
Backtrace stopped: Cannot access memory at address 0xffffc90000004010
```

You'll find that the update is triggered by an interrupt, starting from
`common_interrupt`.

Explore the kernel source code guided by the stack trace helps understand the `jiffies_64` update mechanism.

!!! info "Hint"

    When analysing the kernel source code, particularly at
    `generic_handle_irq_desc`, you'll notice it invokes a method from `struct
    irq_desc`:

    ```c
    static inline void generic_handle_irq_desc(struct irq_desc *desc) {
        desc->handle_irq(desc);
    }
    ```

    The stack trace shows that the `handle_level_irq` function is called from here.
    This indicates that `handle_level_irq` is the method bound to `handle_irq`
    in the `struct irq_desc` instance.

    The stack trace shows that `handle_level_irq` is invoked with an argument `desc`,
    which is an instance of `irq_desc` at the memory address
    `0xffff888003860c00`, you can inspect this object in GDB:

    ```
    (gdb) print *(struct irq_desc *)0xffff888003860c00
    ```

    This GDB command will display the contents of the `irq_desc` object at the
    specified address, allowing you to examine its properties and better understand
    how the kernel handles interrupts and updates variables like `jiffies_64`.

- Which interrupt line (IRQ) is allocated for this specific interrupt?
- What is the name of the `clock_event_device` that processes the event
  triggered by this interrupt, as identified by the
  [`name` field](https://elixir.bootlin.com/linux/v6.7/source/include/linux/clockchips.h#L125)
  in its structure?
