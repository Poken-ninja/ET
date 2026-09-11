# 1. Named Pipe

A **named pipe** is a special type of file that allows separate processes to communicate with each other.

The presentation explains that named pipes:

- Have a name and exist in the file system like files.
- Normally show a size of `0` bytes.
- Can be accessed by two separate processes.
- Can have one process writing data and another process reading it.
- Still exist after their data has been read, so they can be used again.

The presentation says a named pipe can be created using either:

```bash
mkfifo
```

or:

```bash
mknod
```

---

# 1.1 What Is a Process?

A **process** is basically a program that is currently running.

For example:

```text
Program
   ↓
Running program
   ↓
Process
```

So when the presentation says:

> Two separate processes can access the pipe

it means that two running programs can use the same named pipe to communicate.

For example:

```text
Process A
    |
    | writes data
    ↓
Named Pipe
    |
    | reads data
    ↓
Process B
```

One process writes data into the pipe.

The other process reads that data from the pipe.

---

# 1.2 Named Pipe vs Normal File

A named pipe appears in the file system like a normal file, but it does **not** behave like a normal file.

A normal file is generally used to store data:

```text
Normal File
    ↓
Data is stored in the file
```

A named pipe is used to pass data between processes:

```text
Process A
    ↓
Named Pipe
    ↓
Process B
```

The presentation specifically points out that a named pipe appears to have:

```text
0 bytes
```

This does **not** mean the pipe cannot carry data.

It means the pipe itself is not being used like a normal file that permanently stores the data.

---

# 1.3 Creating a Named Pipe with `mknod`

The presentation uses this command:

```bash
mknod /tmp/mypipe p
```

Let's break it down.

## `mknod`

```text
mknod
```

is the command being used to create the special file.

---

## `/tmp/mypipe`

This tells the command where to create the pipe and what to call it.

```text
/tmp
 ↓
Directory

mypipe
 ↓
Name of the pipe
```

So:

```text
/tmp/mypipe
```

is the full path of the named pipe.

---

## `p`

The final:

```text
p
```

tells `mknod` that the special file should be created as a **pipe**.

So:

```bash
mknod /tmp/mypipe p
```

means:

> Create a named pipe called `mypipe` inside `/tmp`.

---

# 1.4 `mkfifo`

The presentation also says that a named pipe can be created using:

```bash
mkfifo
```

So there are two commands mentioned for creating named pipes:

```text
mkfifo
mknod
```

The example shown in the presentation uses:

```bash
mknod /tmp/mypipe p
```

For this topic, the important thing to remember is:

```text
mknod + p
↓
Create a pipe
```

and:

```text
mkfifo
↓
Create a named pipe
```

---

# 1.5 What Does `/tmp` Mean?

The example uses:

```text
/tmp/mypipe
```

`/tmp` is a directory used for temporary files.

The presentation says `/tmp` is usually used for these examples because it has **world-wide writing privileges**.

The important point from the slide is:

> You need permission to write to the directory where you are creating the pipe.

You do **not** automatically need root privileges just to create a named pipe.

---

# 1.6 Do I Need Root Privileges?

The presentation says:

> You do not need root privilege to create a named pipe as long as you can write the pipe to a directory.

In simpler words:

```text
Can I write to the directory?
        |
       YES
        ↓
You can create the pipe there.
```

For example, the presentation commonly uses:

```text
/tmp
```

because it is generally writable for users.

So:

```text
Root privilege
      ≠
Always required for named pipes
```

What matters is whether you have permission to create the pipe in that directory.

---

# 1.7 Checking the Named Pipe With `ls`

After creating the pipe, the presentation uses:

```bash
ls -l /tmp
```

Let's understand this command.

## `ls`

```bash
ls
```

means:

> List the contents of a directory.

---

## `-l`

```text
-l
```

means:

> Use the long listing format.

This gives more detailed information about each item.

So:

```bash
ls -l /tmp
```

means:

> Show the contents of `/tmp` with detailed information.

---

# 1.8 What Does the `p` Mean in `ls -l`?

This is one of the most important things to remember from the slide.

The output can look similar to:

```text
prw-r--r-- 1 kali kali 0 ... mypipe
```

Look at the **very first character**:

```text
p
```

That `p` tells us that the item is a **named pipe**.

So:

```text
p........
^
|
Named pipe
```

The presentation specifically says:

> The named pipe is displayed starting with `p` when you long list a directory.

Therefore:

```bash
ls -l /tmp
```

can help you recognize that:

```text
mypipe
```

is a named pipe.

---

# 1.9 What Does the `0` Mean?

You may also see:

```text
prw-r--r-- 1 kali kali 0 ... mypipe
                         ^
                         |
                       0 bytes
```

The `0` represents the displayed file size.

For the named pipe, the presentation says it appears as:

```text
0 bytes
```

This is one of the differences between a named pipe and a normal file.

So:

```text
p
↓
It's a pipe

0
↓
Displayed size is 0 bytes
```

---

# 1.10 Writing Data Into the Named Pipe

The presentation then demonstrates:

```bash
echo hello world > /tmp/mypipe &
```

Let's understand this one piece at a time.

## `echo hello world`

```bash
echo hello world
```

produces:

```text
hello world
```

Normally, that output would appear on the terminal.

---

## `>`

The:

```text
>
```

is an **output redirection operator**.

It means:

> Send the command's output to the thing on the right.

So:

```bash
echo hello world > /tmp/mypipe
```

means:

```text
echo
  ↓
hello world
  ↓
/tmp/mypipe
```

So instead of displaying:

```text
hello world
```

on the terminal, the output is redirected into the named pipe.

---

# 1.11 What Does `&` Mean Here?

The command in the presentation ends with:

```text
&
```

So the complete command is:

```bash
echo hello world > /tmp/mypipe &
```

For this example, `&` allows the command to run in the **background**.

So instead of waiting for the command to finish before continuing, the shell allows it to run in the background.

Think:

```text
echo hello world
       ↓
/tmp/mypipe
       ↓
runs in background
```

The important thing for this slide is simply:

```text
&
↓
Run the command in the background
```

---

# 1.12 Why Is the Pipe Used Here?

The command:

```bash
echo hello world > /tmp/mypipe &
```

is trying to write:

```text
hello world
```

into:

```text
/tmp/mypipe
```

So the named pipe is being used as the communication point.

The flow is:

```text
echo
  ↓
"hello world"
  ↓
/tmp/mypipe
```

Another process can then read the data from the pipe.

---

# 1.13 Reading From the Named Pipe

The presentation then uses:

```bash
cat /tmp/mypipe
```

`cat` can read the data coming from the named pipe.

So:

```text
/tmp/mypipe
     ↓
    cat
     ↓
"hello world"
```

The terminal then displays:

```text
hello world
```

The complete flow is:

```text
echo hello world
        ↓
   /tmp/mypipe
        ↓
cat /tmp/mypipe
        ↓
   hello world
```

---

# 1.14 What Is `cat` Doing Here?

For this example, think of:

```bash
cat /tmp/mypipe
```

as:

> Read the data coming from `/tmp/mypipe`.

So:

```text
mypipe
   ↓
cat
   ↓
terminal output
```

This is why the presentation uses `cat` after writing `hello world` into the pipe.

---

# 1.15 What Happens After the Pipe Is Read?

This is the final major idea of the slide.

The presentation says:

> Once the pipe has been read, it's empty, though it still will be visible as an empty file ready to be used again.

This means we need to distinguish between:

```text
The pipe itself
```

and:

```text
The data inside the pipe
```

After:

```bash
cat /tmp/mypipe
```

reads:

```text
hello world
```

the data has been consumed.

So:

```text
Before reading:

/tmp/mypipe
     ↓
data waiting


After reading:

/tmp/mypipe
     ↓
still exists
     ↓
empty
```

The **pipe remains**, but the data that passed through it is no longer there.

---

# 1.16 The Most Important Difference

Remember this:

```text
Normal File

File
 ↓
Stores data
```

A named pipe:

```text
Named Pipe
     ↓
Passes data between processes
```

So the named pipe is better thought of as a **communication channel** rather than a normal storage file.

---

# 1.17 Complete Example From the Slide

## Step 1 — Create the pipe

```bash
mknod /tmp/mypipe p
```

Result:

```text
/tmp/mypipe
```

exists as a named pipe.

---

## Step 2 — Check the pipe

```bash
ls -l /tmp
```

You may see:

```text
prw-r--r-- 1 kali kali 0 ... mypipe
^                     ^
|                     |
pipe                   0 bytes
```

---

## Step 3 — Write data into it

```bash
echo hello world > /tmp/mypipe &
```

Data being written:

```text
hello world
```

Flow:

```text
echo
 ↓
hello world
 ↓
/tmp/mypipe
```

---

## Step 4 — Read the pipe

```bash
cat /tmp/mypipe
```

The data comes out:

```text
hello world
```

Flow:

```text
/tmp/mypipe
     ↓
    cat
     ↓
hello world
```

---

## Step 5 — After reading

```text
/tmp/mypipe
     ↓
still exists
     ↓
empty
     ↓
ready to be used again
```

---

# Questions & Clarifications

## Q1. What is `mknod`?

**Answer:**

`mknod` is the command used in the presentation to create a special file.

For the named-pipe example:

```bash
mknod /tmp/mypipe p
```

The `p` tells `mknod` that the special file should be a pipe.

So:

```text
mknod
 ↓
create special file

p
 ↓
make it a pipe
```

---

## Q2. What is `mkfifo`?

**Answer:**

`mkfifo` is another command mentioned by the presentation for creating a named pipe.

The presentation lists:

```text
mkfifo
mknod
```

as ways to create a named pipe.

---

## Q3. What does the `p` at the end of `mknod /tmp/mypipe p` mean?

**Answer:**

The:

```text
p
```

tells `mknod` to create the special file as a **pipe**.

So:

```bash
mknod /tmp/mypipe p
```

means:

```text
Create /tmp/mypipe
        ↓
as a pipe
```

---

## Q4. What does `ls -l` mean?

**Answer:**

```bash
ls
```

lists directory contents.

```text
-l
```

asks for the **long listing format**, which provides more information.

Therefore:

```bash
ls -l /tmp
```

means:

> List the contents of `/tmp` with detailed information.

---

## Q5. What does the `p` at the beginning of `prw-r--r--` mean?

**Answer:**

The first character:

```text
p
```

indicates that the item is a **named pipe**.

For example:

```text
prw-r--r--
^
|
Named pipe
```

This is one of the easiest ways to recognize a named pipe when using:

```bash
ls -l
```

---

## Q6. Why does the named pipe show `0` bytes?

**Answer:**

The presentation says named pipes appear to have:

```text
0 bytes
```

They are not being used like normal files that permanently store their contents.

The pipe is used to pass data between processes.

So:

```text
0 bytes
≠
The pipe cannot carry data
```

It means the pipe itself does not behave like normal stored file contents.

---

## Q7. If the pipe is 0 bytes, how can `"hello world"` go through it?

**Answer:**

The pipe is a **communication channel**, not normal storage.

The example:

```bash
echo hello world > /tmp/mypipe &
```

writes:

```text
hello world
```

into the pipe.

Then:

```bash
cat /tmp/mypipe
```

reads that data.

The flow is:

```text
echo
 ↓
hello world
 ↓
/tmp/mypipe
 ↓
cat
 ↓
hello world
```

---

## Q8. What does `>` mean in this example?

**Answer:**

```text
>
```

is output redirection.

It sends the output of the command on the left to the destination on the right.

So:

```bash
echo hello world > /tmp/mypipe
```

means:

```text
echo's output
      ↓
/tmp/mypipe
```

The important pattern is:

```text
COMMAND > DESTINATION
```

---

## Q9. What does `&` mean in `echo hello world > /tmp/mypipe &`?

**Answer:**

In this example:

```text
&
```

causes the command to run in the **background**.

So:

```bash
echo hello world > /tmp/mypipe &
```

means the shell allows that command to run in the background.

For this slide, that's all you need to remember about `&`.

---

## Q10. What is `cat /tmp/mypipe` doing?

**Answer:**

`cat` is being used to read the data coming from the named pipe.

```bash
cat /tmp/mypipe
```

means:

```text
Read from /tmp/mypipe
       ↓
Display the data
```

So if the pipe receives:

```text
hello world
```

`cat` displays:

```text
hello world
```

---

## Q11. Does `cat` delete the pipe?

**Answer:**

**No.**

Reading the pipe consumes the data, but the pipe itself remains.

```text
Before:

/tmp/mypipe
↓
exists + data


After cat reads it:

/tmp/mypipe
↓
still exists + empty
```

The presentation specifically says the pipe remains visible and is ready to be used again.

---

## Q12. Does the data stay inside the pipe permanently?

**Answer:**

No.

The presentation explains that once the pipe has been read, it is empty.

So:

```text
Write data
    ↓
Read data
    ↓
Data is consumed
```

But:

```text
Pipe
↓
Still exists
```

---

## Q13. Can two programs use the same named pipe?

**Answer:**

Yes.

That is one of the main purposes of a named pipe.

The presentation explains that two separate processes can access the pipe by name.

Conceptually:

```text
Process A
   ↓
writes

/tmp/mypipe

Process B
   ↓
reads
```

---

# Final Mental Model

Don't think of a named pipe as a normal file.

Think of it as a **named communication channel**:

```text
Process A
    |
    | writes
    ↓
/tmp/mypipe
    |
    | reads
    ↓
Process B
```

The pipe:

```text
Has a name
Exists in the filesystem
Shows up with `p`
Usually displays 0 bytes
Can pass data between processes
Remains after the data is read
```

The most important commands from this topic are:

```bash
mknod /tmp/mypipe p
```

```text
Create a named pipe
```

```bash
ls -l /tmp
```

```text
List /tmp in long format
```

```bash
echo hello world > /tmp/mypipe &
```

```text
Write "hello world" into the pipe
```

```bash
cat /tmp/mypipe
```

```text
Read the data from the pipe
```

# One-Minute Revision

```text
NAMED PIPE
↓
Communication channel between processes

mknod /tmp/mypipe p
↓
Create named pipe

mkfifo
↓
Another command for creating a named pipe

ls -l
↓
Long listing

p at beginning
↓
Named pipe

0 bytes
↓
Pipe is not a normal stored file

>
↓
Redirect output to the destination on the right

&
↓
Run command in background

cat /tmp/mypipe
↓
Read data from the pipe

After data is read
↓
Data is consumed
↓
Pipe still exists
```

## Core idea

```text
WRITE:

Process
   ↓
Named Pipe


READ:

Named Pipe
   ↓
Process
```

> **A named pipe is a special file used as a communication channel between processes. The data can be written into the pipe, read from the pipe, and once it is read, the data is consumed while the pipe itself remains available for reuse.**
