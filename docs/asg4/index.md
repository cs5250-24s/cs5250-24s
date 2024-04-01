# Introduction

!!! info

    Please read the [general instructions](../index.md) first.

    The assignment is due **17:59 on 22 Apr 2024**.
    Late submissions will lose **1 mark per hour**.

    If you discover any errors or need any clarification or have any question,
    please create an issue [here](https://github.com/nus-cs5250/24s/issues).

## Part A (35 marks)

!!! note

    Please accept the assignment on GitHub Classroom first.
    The invitation link is available on
    [Canvas](https://canvas.nus.edu.sg/courses/53045/assignments/111552).

### [Task 1: Character Device Driver](task-chardev.md)

**Question 1 (6 marks)**

Please explain, in detail, how the `container_of` macro works in the
`onebyte_open` function, and how it's used to retrieve the `onebyte_data`
structure.

Additionally, explain why the `onebyte_data` structure is stored as the private
data of the file instead of being stored as a global variable?

**Question 2 (2 marks)**

Provide the exact `mknod` command you used to create the device file.

### [Task 2: PCI Device](task-pcidev.md)

**Question 3 (4 marks)**

Why can't we read from the file `resource0`?

Hint:

- According to the
  [documentation](https://www.kernel.org/doc/html/v6.7/PCI/sysfs-pci.html), `rw`
  access is allowed for `IORESOURCE_IO` regions only in files named
  `resource0..N`.
- You should refer to the information from the configuration space.

**Question 4 (5 marks)**

Please finish the implementation under the directory `task2-mmap/` in the
repository.

### [Task 3: Interrupt handler](task-interrupt.md)

**Question 5 (18 marks)**

Please finish the implementation under the directory `task3-factorial/` in the
repository.

### Submission Guidelines

Please accept the assignment on GitHub Classroom first. The invitation link is
available on
[Canvas](https://canvas.nus.edu.sg/courses/53045/assignments/111552). Then,
proceed to complete the tasks in the repository and push your changes
accordingly. I'll pull your work from the repository after the deadline.

Please note the following instructions:

1. The deadline posted on GitHub is the hard deadline — you'll lose write access
   to the repository after that time. However, late penalties will apply
   starting from the due date on Canvas.

2. I will evaluate two versions of your code for grading: (1) the last commit
   before the due date on Canvas and (2) the last commit pushed to the
   repository with late penalties applied. The higher grade of the two versions
   will be your final grade for this part.

3. Ensure that your code can be successfully built and that modules can be
   loaded into the kernel. Failure to build or load will result in a mark of 0.

4. Coding style is important.

   - The repository contains a `.clang-format` file specifying formatting rules.
     You can use the `clang-format` tool to automatically format your code.
   - You're free to modify the `.clang-format` file as you wish.
   - Your final submission must adhere to your specification. I'll use
     `clang-format --dry-run --Werror` to check your code, including C source
     code files and header files. Any complaints from `clang-format` will result
     in a penalty of 1 mark per file.

5. Push only essential files to the repository. Do not push binaries, object
   files, or any other generated files from the source code. Ensure proper usage
   of `.gitignore` to exclude inappropriate files from being pushed to the
   repository. Messy repositories will incur a one-time penalty of 2 marks.

The repository contains the following files or directories:

| Filename             | Description                                                |
| -------------------- | ---------------------------------------------------------- |
| `/qn1.md`            | Your response to Question 1 in Markdown format.            |
| `/qn2.md`            | Your response to Question 2 in Markdown format.            |
| `/qn3.md`            | Your response to Question 3 in Markdown format.            |
| `/task2-mmap/*`      | Your code operating the PCI device using `mmap`.           |
| `/task3-factorial/*` | Your device driver implementing the factorial calculation. |

Please, besides, fill in your GitHub **username** in the [Assignment 4 (Part
A)](https://canvas.nus.edu.sg/courses/53045/assignments/111552) on Canvas.

## Part B (15 marks)

Please complete the quiz
[Assignment 4 (Part B)](https://canvas.nus.edu.sg/courses/53045/assignments/111551)
on Canvas.
