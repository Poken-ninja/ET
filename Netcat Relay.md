
# 1. Moving Files Using Netcat

Netcat can be used to transfer the contents of a file from one machine to another.

This works by using the same input/output concepts we learned earlier:

< = take input from a file
> = send output to a file


The basic idea is:

```text
Sending Machine                  Receiving Machine

    File
      |
      | <
      ↓
     nc
      |
      |------ Network ------→
                              nc
                               |
                               | >
                               ↓
                              File
```

So the file contents travel:

```text
File
 ↓
Netcat
 ↓
Network
 ↓
Netcat
 ↓
Destination File
```

---

## 1.1 Example: Machine 2 → Machine 1

The presentation gives an example where **Machine 1 receives the file from Machine 2**.

### Machine 1 — Receiving Machine

Machine 1 has the IP address:

```text
192.168.20.9
```

It runs:

```bash
nc -lvp 1234 > /tmp/netcatfile
```

This machine is waiting for a connection and saving the received data into:

```text
/tmp/netcatfile
```

### Breaking Down the Command

```bash
nc -lvp 1234 > /tmp/netcatfile
```

| Part              | Meaning                               |
| ----------------- | ------------------------------------- |
| `nc`              | Start Netcat                          |
| `-l`              | Listen for an incoming connection     |
| `-v`              | Verbose output                        |
| `-p`              | Specify the port                      |
| `1234`            | Port being used                       |
| `>`               | Redirect Netcat's output              |
| `/tmp/netcatfile` | File where the received data is saved |

The important part is:

```bash
> /tmp/netcatfile
```

This means:

```text
Netcat's output
      ↓
/tmp/netcatfile
```

So Machine 1 is essentially saying:

> Listen on port `1234` and save the data received from Netcat into `/tmp/netcatfile`.

---

## 1.2 Machine 2 — Sending Machine

Machine 2 runs:

```bash
nc 192.168.20.9 1234 < /tmp/myfile
```

This machine is sending the contents of:

```text
/tmp/myfile
```

to Machine 1.

### Breaking Down the Command

```bash
nc 192.168.20.9 1234 < /tmp/myfile
```

| Part           | Meaning                                    |
| -------------- | ------------------------------------------ |
| `nc`           | Start Netcat                               |
| `192.168.20.9` | IP address of Machine 1                    |
| `1234`         | Port on Machine 1                          |
| `<`            | Redirect file contents into Netcat's input |
| `/tmp/myfile`  | File being sent                            |

The important part is:

```bash
< /tmp/myfile
```

This means:

```text
/tmp/myfile
     ↓
Netcat's Standard Input
```

So Machine 2 is essentially saying:

> Connect to `192.168.20.9` on port `1234` and send the contents of `/tmp/myfile`.

---

## 1.3 Complete Data Flow

Putting both commands together:

```text
                 NETWORK
                    │
                    │
Machine 2           │           Machine 1
─────────           │           ─────────
                    │
/tmp/myfile         │
     │              │
     │ <            │
     ↓              │
    nc ─────────────┼────────→ nc
                                   │
                                   │ >
                                   ↓
                            /tmp/netcatfile
```

The actual flow is:

```text
Machine 2
   ↓
/tmp/myfile
   ↓
< input redirection
   ↓
Netcat
   ↓
Network
   ↓
Netcat
   ↓
> output redirection
   ↓
Machine 1
   ↓
/tmp/netcatfile
```

Therefore:

```text
/tmp/myfile
      ↓
Machine 2
      ↓
    Netcat
      ↓
   Network
      ↓
    Netcat
      ↓
Machine 1
      ↓
/tmp/netcatfile
```

---

## 1.4 Why Does `<` Go on the Sending Side?

This is an important thing to understand.

The sending machine needs to **provide the file as input to Netcat**.

So:

```bash
nc 192.168.20.9 1234 < /tmp/myfile
```

uses:

```text
< 
↓
File → Netcat
```

Therefore:

```text
/tmp/myfile
      ↓
    Netcat
      ↓
   Network
```

The sender uses `<` because the file is becoming **Netcat's input**.

---

## 1.5 Why Does `>` Go on the Receiving Side?

The receiving machine gets data from the network through Netcat.

That received data becomes Netcat's output.

So:

```bash
nc -lvp 1234 > /tmp/netcatfile
```

uses:

```text
>
↓
Netcat → File
```

Therefore:

```text
Network
   ↓
 Netcat
   ↓
/tmp/netcatfile
```

The receiver uses `>` because the received data is being saved as **Netcat's output**.

---

## 1.6 The Easy Rule

For this file-transfer example:

```text
SENDER

File
 ↓
<
 ↓
Netcat
 ↓
Network
```

```text
RECEIVER

Network
 ↓
Netcat
 ↓
>
 ↓
File
```

Or simply:

```text
< = File → Netcat
> = Netcat → File
```

This is the main idea behind the file-transfer example.

---

# 1.7 Can We Transfer the File in the Opposite Direction?

Yes.

The presentation asks how to transfer the file from **Machine 1 to Machine 2**.

The same idea is used, but the roles are reversed.

Previously:

```text
Machine 2 → Machine 1
```

Now:

```text
Machine 1 → Machine 2
```

The machine receiving the file needs to listen and save the incoming data.

The machine sending the file connects to the listener and provides the file as input.

Conceptually:

```text
Machine 1                         Machine 2

/tmp/myfile
     │
     │ <
     ↓
    nc
     │
     │────── Network ──────────→
                                  nc
                                   │
                                   │ >
                                   ↓
                            /tmp/netcatfile
```

The important thing is not the specific machine number.

The important rule is:

```text
Sender:
File → nc → Network

Receiver:
Network → nc → File
```

---

# 1.8 File Transfer Is Just Combining Input and Output

This example is useful because it combines concepts we already learned about Netcat.

### On the sending machine:

```text
File
 ↓
<
 ↓
Netcat
```

### Across the network:

```text
Netcat
 ↓
Network
 ↓
Netcat
```

### On the receiving machine:

```text
Netcat
 ↓
>
File
```

So the complete process is:

```text
File
 ↓
Netcat
 ↓
Network
 ↓
Netcat
 ↓
File
```

---

# Questions & Clarifications

## Q1. Is Netcat actually copying the file?

Not exactly.

Netcat is transferring the **data contained in the file** across the network.

The sending machine gives the file contents to Netcat:

```text
File → Netcat
```

Netcat sends that data through the network:

```text
Netcat → Network
```

The receiving Netcat outputs the received data into a file:

```text
Netcat → File
```

So conceptually:

```text
File contents
     ↓
    Netcat
     ↓
   Network
     ↓
    Netcat
     ↓
New file
```

---

## Q2. Why is the destination file called `/tmp/netcatfile`?

It is simply the filename used in the presentation's example.

It could be given another name.

The important part is:

```bash
> /tmp/netcatfile
```

which tells the shell to save Netcat's output there.

---

## Q3. Why is `/tmp/myfile` on the sending side?

Because that is the file whose contents are being sent.

```bash
< /tmp/myfile
```

means:

```text
Read input from /tmp/myfile
        ↓
Give it to Netcat
```

---

## Q4. Why is `/tmp/netcatfile` on the receiving side?

Because that is where the receiving machine saves the data.

```bash
> /tmp/netcatfile
```

means:

```text
Netcat's output
      ↓
/tmp/netcatfile
```

---

## Q5. Which machine is the client?

In the presentation's example:

```text
Machine 1
↓
Listener

Machine 2
↓
Client
```

Machine 1 runs:

```bash
nc -lvp 1234
```

so it is listening.

Machine 2 runs:

```bash
nc 192.168.20.9 1234
```

so it initiates the connection.

Therefore:

```text
Machine 2 = Client
Machine 1 = Listener
```

---

## Q6. Does the sender always have to be the client?

No.

The important concepts are separate:

```text
Client / Listener
↓
Who initiates the connection


Sender / Receiver
↓
Who sends and receives the data
```

In this particular presentation example:

```text
Machine 2 = Client + Sender
Machine 1 = Listener + Receiver
```

But those are two different concepts.

---

# Quick Revision

### Sending file

```bash
nc [destination_IP] [port] < /tmp/myfile
```

```text
File
 ↓
<
 ↓
Netcat
 ↓
Network
```

### Receiving file

```bash
nc -lvp [port] > /tmp/netcatfile
```

```text
Network
 ↓
Netcat
 ↓
>
 ↓
File
```

### Presentation Example

```bash
# Machine 1 — Receiver
nc -lvp 1234 > /tmp/netcatfile
```

```bash
# Machine 2 — Sender
nc 192.168.20.9 1234 < /tmp/myfile
```

### Mental Model

```text
SENDER                         RECEIVER

/tmp/myfile
     ↓
     <
     ↓
    nc
     │
     │
     │────── NETWORK ──────→
                              nc
                               ↓
                               >
                               ↓
                         /tmp/netcatfile
```

> **The key idea:** Netcat can transfer a file by treating the file as its input on the sending machine and saving Netcat's output to a file on the receiving machine.

```text
< = File → Netcat

> = Netcat → File
```
