# 2. Netcat (`nc`)

This topic introduces **Netcat**, what it does with input and output, and the basic client/listener concepts.

The presentation describes Netcat as:

> "The Swiss army knife of TCP/IP connections."

The command used to run Netcat is:

```bash
nc
```

So:

```text
Netcat = nc
```

---

# 2.1 What Is Netcat?

Netcat is a networking tool used to create TCP/IP connections and exchange data across a network.

The basic idea is:

```text
System
   ↓
Standard Input
   ↓
Netcat
   ↓
Network
```

Netcat can also work in the opposite direction:

```text
Network
   ↓
Netcat
   ↓
Standard Output
   ↓
System
```

So Netcat can:

```text
Take input from the system
        ↓
Send it across the network


Receive data from the network
        ↓
Give it to the system
```

---

# 2.2 Standard Input, Standard Output, and Standard Error

Before understanding Netcat, we need to understand three basic terms.

## Standard Input (`stdin`)

**Standard Input** is the input that a program receives.

For example, when you type something into a terminal, the program can receive what you type through Standard Input.

Think:

```text
Keyboard
   ↓
Standard Input
   ↓
Program
```

For Netcat:

```text
System
   ↓
Standard Input
   ↓
Netcat
```

---

## Standard Output (`stdout`)

**Standard Output** is where a program sends its normal output.

Usually, this is displayed on your terminal screen.

For Netcat:

```text
Netcat
   ↓
Standard Output
   ↓
Screen
```

---

## Standard Error (`stderr`)

**Standard Error** is another output channel used for messages produced by a program itself.

For example, Netcat may display information about what it is doing through Standard Error.

The presentation says that Netcat's own messages go to Standard Error, which is displayed on Standard Output in the example.

For now, the important thing to remember is:

```text
Standard Input
↓
Input going INTO the program


Standard Output
↓
Normal output coming OUT of the program


Standard Error
↓
Messages/errors from the program
```

---

# 2.3 Netcat's Basic Data Flow

The presentation's diagram can be understood as two separate directions.

## Sending Data

```text
System
   ↓
Standard Input
   ↓
Netcat
   ↓
Network
```

This means:

> The system gives input to Netcat, and Netcat sends that input across the network.

---

## Receiving Data

```text
Network
   ↓
Netcat
   ↓
Standard Output
   ↓
System
```

This means:

> Netcat receives data from the network and places it on Standard Output.

---

# 2.4 The Simplest Mental Model

Think of Netcat as a bridge:

```text
             NETWORK
             ↗     ↘
          SEND     RECEIVE
           ↑          ↓
           |          |
       Netcat       Netcat
           ↑          ↓
           |          |
      Standard     Standard
       Input        Output
           ↑          ↓
           |          |
         System      System
```

Or simply:

```text
SEND:

System → Netcat → Network


RECEIVE:

Network → Netcat → System
```

This is the main idea behind the first Netcat slide.

---

# 2.5 Netcat Input

The presentation says:

> Netcat takes whatever comes in on Standard Input and sends it across the network.

There are three ways the input can reach Netcat.

---

## Method 1: Keyboard

Standard Input can come from the keyboard.

For example, if you type:

```text
hello
```

the flow is:

```text
Keyboard
   ↓
Standard Input
   ↓
Netcat
   ↓
Network
```

So:

> You type something → Netcat receives it as input → Netcat sends it across the network.

---

# 2.6 Input From a File

The presentation gives:

```bash
nc [options] < [file]
```

The important symbol is:

```text
<
```

`<` is an **input redirection operator**.

For this example, it means:

> Take input from the file and give it to Netcat.

So:

```bash
nc [options] < myfile
```

means:

```text
myfile
   ↓
Standard Input
   ↓
Netcat
   ↓
Network
```

---

## Example

Suppose:

```text
myfile.txt
```

contains:

```text
Hello World
```

Then:

```bash
nc [options] < myfile.txt
```

takes the contents of the file and gives them to Netcat as input.

Think:

```text
myfile.txt
     ↓
"Hello World"
     ↓
Netcat
     ↓
Network
```

---

# 2.7 Input From Another Program

The presentation also gives:

```bash
[program] | nc [options]
```

The symbol:

```text
|
```

is called a **pipe**.

For this example, the pipe means:

> Take the output of the program on the left and give it to the program on the right as input.

So:

```bash
[program] | nc [options]
```

means:

```text
Program
   ↓
Output
   ↓
   |
   ↓
Netcat
   ↓
Standard Input
```

Then Netcat sends that input across the network:

```text
Program
   ↓
Output
   ↓
Netcat
   ↓
Network
```

---

# 2.8 Example: `cat myfile | nc`

Suppose we have:

```text
myfile.txt
```

We can use:

```bash
cat myfile.txt
```

to read the file.

If the file contains:

```text
Hello World
```

then:

```bash
cat myfile.txt
```

produces:

```text
Hello World
```

Now we can pipe that output into Netcat:

```bash
cat myfile.txt | nc
```

The flow is:

```text
myfile.txt
     ↓
    cat
     ↓
  output
     ↓
     |
     ↓
    nc
     ↓
Standard Input of nc
```

So yes:

> **`nc` is taking the output produced by `cat` as its Standard Input.**

---

# 2.9 The Left-to-Right Rule for `|`

When using the pipe:

```bash
command1 | command2
```

the output of the command on the **left** becomes the input of the command on the **right**.

Think:

```text
LEFT
 ↓
produces output
 ↓
|
 ↓
RIGHT
 ↓
receives input
```

For example:

```bash
cat myfile.txt | nc
```

means:

```text
cat → nc
```

But:

```bash
nc | cat
```

means:

```text
nc → cat
```

So the position matters.

### Easy rule:

```text
LEFT COMMAND
↓
produces output

RIGHT COMMAND
↓
receives that output as input
```

---

# 2.10 `<` vs `>` vs `|`

These three symbols are easy to confuse.

## `<`

```bash
nc < myfile
```

Means:

```text
myfile
   ↓
Netcat
```

Think:

```text
< = input INTO the command
```

---

## `>`

```bash
nc > myfile
```

Means:

```text
Netcat
   ↓
myfile
```

Think:

```text
> = output FROM the command
```

---

## `|`

```bash
program | nc
```

Means:

```text
program
   ↓
output
   ↓
nc
   ↓
input
```

Think:

```text
| = output of LEFT command
    becomes input of RIGHT command
```

---

# 2.11 Important Redirection Patterns

Remember these patterns:

```text
COMMAND < INPUT
```

means:

```text
INPUT → COMMAND
```

For example:

```bash
nc < myfile
```

means:

```text
myfile → nc
```

---

```text
COMMAND > OUTPUT
```

means:

```text
COMMAND → OUTPUT
```

For example:

```bash
nc > myfile
```

means:

```text
nc → myfile
```

---

```text
COMMAND1 | COMMAND2
```

means:

```text
COMMAND1 → COMMAND2
```

More specifically:

```text
COMMAND1's Standard Output
             ↓
COMMAND2's Standard Input
```

---

# 2.12 What Does `[options]` Mean?

The presentation uses:

```bash
nc [options] < [file]
```

and:

```bash
nc [options] | [program]
```

The:

```text
[options]
```

part is a **placeholder**.

It does NOT mean that you literally type:

```text
[options]
```

It means:

> Put any Netcat options you need here.

For example:

```bash
nc -nv
```

contains the options:

```text
-n
-v
```

So:

```text
[options]
↓
Any appropriate Netcat flags/options
```

---

# Questions & Clarifications — Input

## Q1. Can `<` and `>` be swapped?

You can use either operator, but **they do different things**.

For example:

```bash
nc < myfile
```

means:

```text
myfile → nc
```

while:

```bash
nc > myfile
```

means:

```text
nc → myfile
```

So changing `<` to `>` changes the direction of the redirection.

Remember:

```text
< = input
> = output
```

---

## Q2. Can I write `myfile < nc`?

Not in the way we're using these operators.

The normal pattern is:

```text
COMMAND < INPUT
```

So:

```bash
nc < myfile
```

means:

```text
Run nc
+
give nc input from myfile
```

The command being executed is on the left.

---

## Q3. Can `<` and `>` be used with programs?

The presentation demonstrates them with files:

```bash
nc < file
```

and:

```bash
nc > file
```

The important concept is that they are **redirection operators**.

They redirect a command's Standard Input or Standard Output.

For this topic, remember the presentation's examples as:

```text
< → input from a file
> → output to a file
```

---

## Q4. Is `|` only for programs?

The presentation uses `|` to connect one program's output to another program's input.

For example:

```bash
program | nc
```

The important rule is:

```text
Program on LEFT
      ↓
produces output
      ↓
Program on RIGHT
      ↓
receives input
```

So when you see:

```bash
program | nc
```

think:

```text
program → nc
```

---

## Q5. Does the source program have to be on the left of `|`?

Yes, for the normal pipe structure:

```bash
program1 | program2
```

the command on the left produces the output and the command on the right receives it as input.

For example:

```bash
cat myfile.txt | nc
```

means:

```text
cat → nc
```

If you reverse it:

```bash
nc | cat
```

then the direction becomes:

```text
nc → cat
```

---

# 2.13 Netcat Output

Now we reverse the direction.

The presentation says:

> When Netcat receives data from the network, it places it on the Standard Output.

So:

```text
Network
   ↓
Netcat
   ↓
Standard Output
```

That Standard Output can go to different places.

---

# 2.14 Output to the Screen

Standard Output can normally appear on the screen.

The flow is:

```text
Network
   ↓
Netcat
   ↓
Standard Output
   ↓
Screen
```

So if Netcat receives:

```text
Hello World
```

you may see:

```text
Hello World
```

on the terminal.

---

# 2.15 Output to a File

The presentation gives:

```bash
nc [options] > [file]
```

The:

```text
>
```

redirects Netcat's Standard Output to the file.

So:

```bash
nc [options] > myfile
```

means:

```text
Network
   ↓
Netcat
   ↓
Standard Output
   ↓
myfile
```

---

# 2.16 Output to Another Program

The presentation also gives:

```bash
nc [options] | [program]
```

Here:

```text
Netcat
   ↓
Standard Output
   ↓
|
   ↓
Another Program
   ↓
Standard Input
```

So the output produced by Netcat becomes the input of the program on the right.

Remember:

```text
nc | program
```

means:

```text
nc → program
```

---

# 2.17 The `-e` Option

The presentation also introduces:

```bash
nc [options] -e [program]
```

The:

```text
-e
```

option tells Netcat to execute a program **after a connection is made**.

The basic idea is:

```text
Connection occurs
       ↓
Netcat
       ↓
Execute specified program
```

The program is represented by:

```text
[program]
```

This is another placeholder.

It means:

> Specify the program you want Netcat to execute.

---

# 2.18 What Does `-e` Do With Network Data?

The presentation explains that `-e [program]` does more than simply execute a program.

It connects the network communication with the program's Standard Input and Standard Output.

The data received by Netcat from the network is passed to the program's:

```text
Standard Input
```

The program's:

```text
Standard Output
```

is then sent back across the network through Netcat.

The basic flow is:

```text
NETWORK
   ↓
Netcat
   ↓
Program's Standard Input
   ↓
Program
   ↓
Program's Standard Output
   ↓
Netcat
   ↓
NETWORK
```

So Netcat acts as the connection between the network and the program.

---

# 2.19 Netcat Usage

The presentation gives the general Netcat format:

```bash
nc [flags] [targetIP] [port(s)]
```

These are placeholders.

They mean:

```text
nc
↓
Netcat

[flags]
↓
Optional Netcat options

[targetIP]
↓
IP address of the target machine

[port(s)]
↓
Port or ports involved in the connection
```

You do not literally type the square brackets.

For example:

```bash
nc -nv 192.168.1.10 4444
```

has:

```text
-nv
↓
flags

192.168.1.10
↓
target IP

4444
↓
port
```

---

# 2.20 Important Netcat Flags

The presentation introduces these flags.

---

## `-l` — Listen Mode

```text
-l
```

means:

> Listen mode.

The presentation says the default mode is **client mode**.

Without `-l`, Netcat normally acts as a client and initiates a connection.

With:

```text
-l
```

Netcat waits for a connection.

Think:

```text
Client
↓
initiates connection

Listener
↓
waits for connection
```

So:

```text
-l = listen
```

---

# 2.21 `-L` — Listen Again

```text
-L
```

The presentation specifies this for the **Windows version**.

It causes Netcat to listen again after a client disconnects.

Conceptually:

```text
Listen
   ↓
Client connects
   ↓
Client disconnects
   ↓
Listen again
```

So:

```text
-L = listen again
```

---

# 2.22 `-u` — UDP Mode

```text
-u
```

means:

> Use UDP mode.

The presentation says:

```text
Default = TCP

-u = UDP
```

The other side must also use UDP.

So:

```text
TCP
↓
Default


UDP
↓
-u
```

---

# 2.23 `-p` — Local Port

```text
-p
```

specifies the **local port**.

The presentation explains that its meaning depends on the mode.

### In listen mode

The port is the port Netcat listens on.

For example:

```bash
nc -lvp 4444
```

means:

```text
Listen
   ↓
Port 4444
```

### In client mode

The presentation says the local port is the **source port for packets sent**.

So:

```text
Client mode
↓
-p
↓
Source/local port
```

This is important because the port you connect **to** is not necessarily the same as your local source port.

---

# 2.24 `-d` — Background Mode

```text
-d
```

means:

> Run Netcat in the background, detached from the console.

So:

```text
-d
↓
Netcat runs in background
```

---

# 2.25 `-e` — Execute a Program

```text
-e
```

means:

> Specify a program to be executed after a connection occurs.

For example, the presentation later uses:

```text
/bin/bash
```

or:

```text
cmd.exe
```

The important thing for this topic is:

```text
-e
↓
execute specified program after connection
```

---

# 2.26 `-n` — Do Not Resolve Names

```text
-n
```

means:

> Do not resolve the name of the machine on the other side.

This is about **name resolution**.

It does NOT mean:

```text
Hide the attacker
Hide the IP address
Hide the connection
Become anonymous
Become untraceable
```

It simply means:

```text
-n
↓
Don't resolve machine names
```

---

# 2.27 `-z` — Zero I/O Mode

```text
-z
```

means:

> Zero I/O mode.

I/O means:

```text
Input / Output
```

The presentation explains:

### TCP

Netcat completes the TCP **three-way handshake**, but does not transfer normal data.

Very simply:

```text
Client                  Server

  SYN       ─────────→

  SYN-ACK   ←─────────

  ACK       ─────────→

      Connection established
```

With `-z`, there is no normal data transfer after this.

### UDP

The presentation says Netcat emits a UDP packet without a payload.

A packet can be thought of as:

```text
Header + Payload
```

With this mode, the UDP packet has no normal payload.

For this topic, remember:

```text
-z = zero I/O / no normal data transfer
```

---

# 2.28 `-wN` — Timeout

```text
-wN
```

sets a timeout.

Here:

```text
N = number of seconds
```

For example:

```bash
-w5
```

means:

```text
5 seconds
```

The presentation explains that Netcat waits for the specified time when making a connection.

If the connection does not happen within that time:

```text
Timeout
   ↓
Netcat stops
```

If a connection does happen, Netcat can send or retrieve data.

The presentation also says that when no data is transmitted for a total of `N` seconds, Netcat stops running.

So:

```text
-wN
↓
Timeout of N seconds
```

---

# 2.29 Client vs Listener

The next slide makes an important distinction.

## Client

The presentation says:

> Clients initiate connections.

The general format is:

```bash
nc -nv [targetIP] [remote_port(s)]
```

The client is the side that **starts the connection**.

Think:

```text
Client
   ↓
"I want to connect to that machine."
```

---

## Listener

The presentation says:

> Listeners wait for connections.

The general format is:

```bash
nc -lvp [local_port]
```

The listener is the side that **waits for another machine to connect**.

Think:

```text
Listener
   ↓
"I will wait for someone to connect to me."
```

---

# 2.30 Client and Listener Diagram

The basic relationship is:

```text
CLIENT                         LISTENER
   |                               |
   | -------- connection --------> |
   |                               |
```

The client starts the connection.

The listener waits for it.

Remember:

```text
CLIENT
↓
Initiates


LISTENER
↓
Waits
```

---

# 2.31 Standard Input and Output on Both Sides

After a connection exists, both sides can have Standard Input and Standard Output.

Conceptually:

```text
        CLIENT                         LISTENER
           |                               |
        Std In                          Std In
           ↓                               ↓
        Netcat  ←──── NETWORK ────→     Netcat
           ↓                               ↓
        Std Out                         Std Out
```

So Netcat can move data between:

```text
System ↔ Netcat ↔ Network ↔ Netcat ↔ System
```

---

# Questions & Clarifications — Netcat

## Q1. What exactly is Netcat?

**Answer:**

Netcat is a networking tool used to create TCP/IP connections and exchange data across a network.

The command is:

```bash
nc
```

The presentation calls it:

> "The Swiss army knife of TCP/IP connections."

The simplest mental model is:

```text
System → Netcat → Network

Network → Netcat → System
```

---

## Q2. What is Standard Input?

**Answer:**

Standard Input is the input a program receives.

For example:

```text
Keyboard
   ↓
Standard Input
   ↓
Program
```

For Netcat:

```text
System
   ↓
Standard Input
   ↓
Netcat
```

---

## Q3. What is Standard Output?

**Answer:**

Standard Output is where a program sends its normal output.

Usually, this is displayed on the terminal.

For Netcat:

```text
Netcat
   ↓
Standard Output
   ↓
Screen
```

---

## Q4. What is Standard Error?

**Answer:**

Standard Error is another output channel used for messages produced by a program itself.

For Netcat, its own messages are sent through Standard Error according to the presentation.

For now:

```text
stdin
↓
input

stdout
↓
normal output

stderr
↓
program messages/errors
```

---

## Q5. In `cat myfile | nc`, is `nc` taking `cat`'s output as input?

**Answer:**

Yes.

```bash
cat myfile | nc
```

means:

```text
myfile
   ↓
cat
   ↓
cat's Standard Output
   ↓
|
   ↓
nc's Standard Input
```

So:

```text
cat → nc
```

---

## Q6. Does the program producing the output have to be on the left of `|`?

**Answer:**

Yes, for the normal pipe structure.

```bash
program1 | program2
```

means:

```text
program1's output
       ↓
program2's input
```

So:

```bash
cat myfile | nc
```

means:

```text
cat → nc
```

while:

```bash
nc | cat
```

means:

```text
nc → cat
```

---

## Q7. Why can't I just use `<` instead of `|` for another program?

**Answer:**

They represent different mechanisms.

For example:

```bash
nc < myfile
```

uses input redirection:

```text
File → nc
```

while:

```bash
program | nc
```

uses a pipe:

```text
Program's output → nc's input
```

So:

```text
< 
↓
Input redirection


|
↓
Output of one command → input of another command
```

---

## Q8. What does `[options]` mean?

**Answer:**

It is a placeholder.

For example:

```bash
nc [options] < myfile
```

does NOT mean you literally type:

```text
[options]
```

It means:

> Put whatever Netcat options you need there.

For example:

```bash
nc -nv 192.168.1.10 4444
```

uses:

```text
-n
-v
```

as its options.

---

## Q9. What is the difference between a client and a listener?

**Answer:**

The difference is who starts the connection.

```text
CLIENT
↓
Initiates the connection
```

```text
LISTENER
↓
Waits for the connection
```

The presentation's commands are:

```bash
nc -nv [targetIP] [remote_port]
```

for a client, and:

```bash
nc -lvp [local_port]
```

for a listener.

---

## Q10. Does the client and listener use the same port?

**Answer:**

The client needs to connect to the **port where the listener is listening**.

For example:

```text
Listener:
Port 4444
```

The client connects to:

```text
Target_IP:4444
```

However, the client's own **source/local port** does not necessarily have to be `4444`.

Conceptually:

```text
Client                         Listener
192.168.1.10:50000  ───────→  192.168.1.20:4444
       ↑                              ↑
  source port                  destination/
                               listening port
```

So:

```text
Client source port
≠
Listener port
```

necessarily.

---

## Q11. Why does the presentation keep using port `4444`?

**Answer:**

`4444` is simply the example port used in the presentation's demonstrations.

It is not a special Netcat-only port.

The important relationship is:

```text
Listener
↓
listens on PORT X

Client
↓
connects to PORT X
```

So if the listener were using another port, the client would need to connect to that port instead.

---

## Q12. How does the client know which port to connect to?

**Answer:**

The client has to know the destination port somehow.

In the presentation's examples, the port is already known because the example tells us what port the listener is using.

For example:

```text
Listener:
4444
```

Therefore:

```text
Client:
connect to 4444
```

Netcat does not magically tell the client which port is being used.

The port could be communicated beforehand or discovered through network/service discovery during an authorized assessment.

The current Netcat slide does not teach the discovery process.

---

## Q13. Does `-n` hide the attacker's IP address?

**Answer:**

No.

The presentation says:

```text
-n
↓
don't resolve the name of the machine on the other side
```

It does **not** mean:

```text
Hide IP
Hide connection
Become anonymous
Become untraceable
```

So:

```text
-n ≠ anonymity
```

It is only about name resolution.

---

## Q14. Does Netcat automatically bypass a firewall?

**Answer:**

No.

Netcat is a networking tool. It does not automatically bypass firewall rules.

Conceptually:

```text
Netcat
   ↓
Operating System
   ↓
Network
   ↓
Firewall / Security Controls
   ↓
Destination
```

Whether the connection works depends on the network and security configuration.

So:

```text
Netcat ≠ firewall bypass
```

---

## Q15. Is Netcat automatically a backdoor?

**Answer:**

No.

Netcat is a networking tool.

It can be used for legitimate purposes such as:

```text
Network testing
Troubleshooting
Learning networking
Cybersecurity labs
```

The presentation later demonstrates how Netcat can be combined with a shell to create a bind shell.

So:

```text
Netcat
≠
Automatically a backdoor
```

The context and configuration matter.

---

# Quick Revision

## Netcat

```bash
nc
```

```text
Netcat = networking tool
```

---

## Standard Input

```text
Input INTO the program
```

```text
Keyboard → stdin → Netcat
```

---

## Standard Output

```text
Normal output FROM the program
```

```text
Netcat → stdout → Screen
```

---

## Standard Error

```text
Program messages/errors
```

```text
Netcat → stderr
```

---

## Input Redirection

```bash
nc < myfile
```

```text
myfile → nc
```

---

## Output Redirection

```bash
nc > myfile
```

```text
nc → myfile
```

---

## Pipe

```bash
program | nc
```

```text
program → nc
```

The left command's Standard Output becomes the right command's Standard Input.

---

# Netcat Flags

```text
-l
↓
Listen mode
```

```text
-L
↓
Listen again after disconnect
(Windows version in presentation)
```

```text
-u
↓
UDP mode
```

```text
-p
↓
Specify local port
```

```text
-d
↓
Run in background
```

```text
-e
↓
Execute a program after connection
```

```text
-n
↓
Don't resolve machine names
```

```text
-z
↓
Zero I/O mode
```

```text
-wN
↓
Timeout of N seconds
```

---

# Client vs Listener

```text
CLIENT
↓
Initiates connection
```

```bash
nc -nv [targetIP] [remote_port]
```

---

```text
LISTENER
↓
Waits for connection
```

```bash
nc -lvp [local_port]
```

---

# Final Mental Model

```text
                         NETWORK
                        ↗       ↘
                       /         \
                    SEND         RECEIVE
                     ↑             ↓
                     |             |
               Standard Input  Standard Output
                     ↑             ↓
                     |             |
                   System        System
                       \         /
                        \       /
                         NETCAT
```

Or simply:

```text
SEND:

System → Standard Input → Netcat → Network


RECEIVE:

Network → Netcat → Standard Output → System
```

The three most important symbols are:

```text
< 
↓
Input redirection

>
↓
Output redirection

|
↓
LEFT command's output → RIGHT command's input
```

The three most important Netcat concepts are:

```text
Client
↓
Initiates


Listener
↓
Waits


Netcat
↓
Moves data between the system and network
```

> **Core idea:** Netcat connects a computer's input/output with a network connection. It can take input from the keyboard, a file, or another program, send that data across the network, receive data from the network, and place that received data on Standard Output.
