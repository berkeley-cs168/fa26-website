---
layout: page
title: "Project 3: Transport"
nav_order: 7
toc_max_heading: 2
---

# Project 3: Transport

In this project, you will implement a subset of the TCP protocol. Your implementation will provide reliability, though it won't include congestion control.

Lectures needed for this project: Lecture 10 (Transport 1: TCP), 11 (Transport 2: TCP II).

You can work on this project alone, or with one partner. You can keep the same partner from an earlire project, or choose a different partner. Reminder: [Our course policies page has a collaboration policy](/policies) that you must follow.


## Setup

### Operating System Installation

This project has been tested to work on Linux and Mac.

If you're on Windows, you need to install Windows Subsystem for Linux (WSL). WSL is a tool which allows you to run a Linux environment directly on Windows. To install WSL,
1. Open PowerShell as Adminstrator, then run `wsl --install`. This should install the Ubuntu distro. When prompted, restart your computer.
2. Open the Ubuntu terminal from the Start Menu. It should prompt you to choose a username and password. Choose whatever you like and remember it.

If the installation was successful, you should see `username@hostname:~$` in your terminal. If you are using VSCode, you can click on the "><" arrows in the bottom left corner and select "Connect to WSL" to open a Linux environment. Other code editors might also have options to connect to WSL.

### Note on File Location (Windows Users)

After downloading the starter code (see below), do not run Linux commands on files located in the Windows filesystem (those under `/mnt/c`). This can be frustratingly slow because you are running Linux commands on the Windows disc. Instead,
1. Create a project directory in your WSL home using `mkdir -p ~/projects`.
2. Copy the extracted starter code into `~/projects` using `cp -r /mnt/c/Users/<username>/Downloads/cs168-{{ site.semester_slug }}-proj3-transport ~/projects/`. You can also drag the folder into the Explorer sidebar tab if you are using VSCode.
3. Navigate to the  folder in your terminal using `cd ~/projects/cs168-{{ site.semester_slug }}-proj3-transport` and work from there.


### Python Installation

This project has been tested to work on Python 3.7. (It probably works on newer versions of Python as well, but use at your own risk.)

If you run `python3 --version` or `python --version` in your terminal and you see output of the form `Python 3.7.*`, you're all set.


### Starter Code

[Download a copy of the starter code here.](/assets/projects/proj3/cs168-fa26-proj3-transport.zip)

In your terminal, use `cd` to navigate to the `cs168-fa26-proj3-transport/ext/cs168p2` directory. All of the Python commands should be run from this directory.

First, run this command to give executable permissions to the Python file:

```bash
chmod +x ../../pox.py
```

To check that your setup works, in your terminal, run:

```bash
python ../../pox.py config=tests/sanity_test.cfg
```

If you see this message, everything should be set up correctly:

```
[test] [00:00:02] All checks passed, test PASSED
```

You should only edit `ext/cs168p2/student_socket.py`. There are comments clearly indicating the places where you should fill in code.

Guidelines:
- Don't modify any other files.
- Don't add any new files.
- Don't add any imports.
- Don't edit any code outside the sections indicated by the comments.
- Don't add any hard-coded global variables. 
- Adding helper methods is fine.
- In general, if we haven't told you to use something, you probably don't need it. Our goal is not to trick you!


## Project Overview

This project is split into 9 stages.
- In Project 3A, you'll need to complete Stages 1–5.
- In Project 3B, you'll need to complete Stages 6–9.

Each stage has its own unit tests, provided to you locally. Your grade will be determined only by these unit tests (no hidden tests).

To run a unit test, navigate to `cs168-fa26-proj3-transport/ext/cs168p2`, and run one of these commands, replacing 5 with the number of the stage you want to test:

```bash
python autograder.py s5 # Runs unit tests for Stage 5.
python autograder.py all # Runs all unit tests.
python autograder.py all 5 # Runs all unit tests up to and including Stage 5.
```

If a test fails, you'll see some output like this:

```
test_s1_t1 (__main__.test_s1_t1.test_s1_t1) ... 
    Running test: ./tests/s1_t1.cfg
    Tracefile: ./trace/s1_t1/2024-08-22T16:09:52.593234
../../pox.py config=./tests/s1_t1.cfg tcpip.pcap --node=r1 --no-tx --filename=./trace/s1_t1/2024-08-22T16:09:52.593234
FAIL
```

To see nicely-formatted output for that specific test, take the last line, just before the "FAIL," drop the `--filename` argument, and run that command in your terminal:

```bash
../../pox.py config=./tests/s1_t1.cfg tcpip.pcap --node=r1 --no-tx
```


## Code Overview

You will be implementing the `StudentUSocket` class in `student_socket.py`, which represents a TCP socket.

`student_socket.py` also contains other useful methods, which we'll describe below.


### Modular Arithmetic

Sequence numbers are 32 bits long, and they can wrap around (e.g. `0xFFFFFFFF + 1 = 0x00000000`).

Whenever you perform arithmetic operations on sequence numbers, use these operators:

<table><thead>
  <tr>
    <th>Modular Arithmetic<br>(Use This)</th>
    <th>Operation</th>
    <th>Arithmetic Equivalent<br>(Don't Use This)</th>
  </tr></thead>
<tbody>
  <tr>
    <td><code>A = A |PLUS| 1</code></td>
    <td>Addition</td>
    <td><code>A = A + 1</code></td>
  </tr>
  <tr>
    <td><code>C = A |MINUS| B</code></td>
    <td>Subtraction</td>
    <td><code>C = A – B</code></td>
  </tr>
  <tr>
    <td><code>if (A |EQ| B)</code></td>
    <td>Equal to</td>
    <td><code>if (A == B)</code></td>
  </tr>
  <tr>
    <td><code>if (A |NE| B)</code></td>
    <td>Not equal to</td>
    <td><code>if (A != B)</code></td>
  </tr>
  <tr>
    <td><code>if (A |GT| B)</code></td>
    <td>Greater than</td>
    <td><code>if (A > B)</code></td>
  </tr>
  <tr>
    <td><code>if (A |GE| B)</code></td>
    <td>Greater than or equal to</td>
    <td><code>if (A >= B)</code></td>
  </tr>
  <tr>
    <td><code>if (A |LT| B)</code></td>
    <td>Less than</td>
    <td><code>if (A < B)</code></td>
  </tr>
  <tr>
    <td><code>if (A |LE| B)</code></td>
    <td>Less than or equal to</td>
    <td><code>if (A <= B)</code></td>
  </tr>
</tbody>
</table>


### State

The instance variable `self.state` records the state of the current TCP connection.

`self.state` is always assigned to one of the values in this list:

```python
[CLOSED, LISTEN, SYN_RECEIVED, ESTABLISHED, SYN_SENT, FIN_WAIT_1, FIN_WAIT_2, CLOSING, TIME_WAIT, CLOSE_WAIT, LAST_ACK]
```

We'll describe more details about each state as they come up in the spec.


### Send Sequence Space: TXControlBlock

The instance variable `self.tx_data` is a byte array representing what you're sending. The user will append bytes to the back of this array, and you will remove bytes from the front of this array as you send them out.

The instance variable `self.snd` is a `TXControlBlock` object, which has information about the outgoing bytestream that you are sending.

The `TXControlBlock` object has the following relevant instance variables:

- `self.snd.iss`: Your initial sequence number.
- `self.snd.una`: The oldest unacknowledged sequence number that you sent.
- `self.snd.nxt`: The next sequence number you should send.
- `self.snd.wnd`: The current size of your send window, determined by how much buffer space the other side (recipient) has left.

This diagram shows the bytestream that you are sending and what the instance variables represent:

<img src="/assets/projects/proj3/send-window.svg" width="900px" alt="TCP send window. Before snd.una is sent and acked. Between snd.una and snd.next is sent and unacked. Between snd.nxt and snd.una + snd.wnd is unsent and can be sent. After snd.una + snd.wnd is unsent and cannot be sent.">


### Receive Sequence Space: RXControlBlock

The instance variable `self.rx_data` is a byte array representing what you're receiving. As you receive packets, you should append their payloads (in the correct order) to this byte array so that the user can read them.

The instance variable `self.rcv` is a `RXControlBlock` object, which has information about the incoming bytestream that you are receiving.

The `RXControlBlock` object has the following relevant instance variables:

- `self.rcv.nxt`: The next sequence number you expect to receive.
- `self.rcv.wnd`: The current size of your receive window, determined by how much buffer space you have left.

This diagram shows the bytestream that you are sending and what the instance variables represent:

<img src="/assets/projects/proj3/receive-window.svg" width="900px" alt="TCP receive window. Before rcv.nxt received. Between rcv.nxt and rcv.nxt + rcv.wnd is unreceived, can receive. After rcv.nxt + rcv.wnd is unreceived, cannot receive.">


### Receiving Incoming Segments

The packets you send and receive are TCP/IP packets.

If you receive an IP packet `p`, you can extract the TCP header and payload like this:

```python
seg = p.tcp
```

If you receive a TCP segment `seg`, you can extract these fields from its header:

- `seg.seq`: The sequence number.
- `seg.ack`: The ack number.
- `seg.win`: The advertised window (how much buffer space left on the other side).
- `seg.ACK`: Whether this segment has the ACK flag set (Boolean value).
- `seg.SYN`: Whether this segment has the SYN flag set (Boolean value).
- `seg.FIN`: Whether this segment has the FIN flag set (Boolean value).

If you receive a TCP segment `seg`, you can extract the payload like this:

```python
seg.payload       # The payload.
len(seg.payload)  # Length of the payload.
```


### Sending Outgoing Segments

To create a new packet, you can use the function `self.new_packet(ack=True, data=None, syn=False)`, which takes in the following arguments:

- `ack`: Whether to set the ACK flag. True by default.
- `data`: The TCP payload to send. None by default.
- `syn`: Whether to set the SYN flag. False by default.



## Stage 1: Three-Way Handshake

### Overview

Recall the three-way handshake for starting a TCP connection:

<img src="/assets/projects/proj3/tcp-handshake.svg" width="900px" alt="The TCP three-way handshake. State is CLOSED. After sending SYN, state is SYN_SENT. After sending ACK, state is ESTABLISHED.">

Before the handshake, `self.state` is `CLOSED`.

When you send a SYN packet, you will change `self.state` to `SYN_SENT`.

When you receive a SYN-ACK packet, you will send an ACK packet and change `self.state` to `ESTABLISHED`.


### Stage 1.1: Sending SYN

In `connect`, implement sending a SYN packet.

1. Set your next sequence number to your initial sequence number. (Your initial sequence number has already been set for you.)

2. Use `self.new_packet` to create a SYN packet. Remember, SYN packets don't contain any data.

3. Use `self.tx(p)` to send `p`, the packet you just created. 

4. Set the state to `SYN_SENT`.

5. Update the next sequence number appropriately. Remember, the SYN packet counts as 1 sequence space. Also, remember to use modular arithmetic.


### Stage 1.2: Receiving Packets

Next, in `rx`, implement receiving a packet.

1. If the current state is `SYN_SENT`, call `self.handle_synsent(seg)`.


### Stage 1.3: Processing SYN-ACK, Sending ACK

Finally, in `handle_synsent`, implement processing the SYN-ACK and sending out the ACK. All your code here should be inside the `if acceptable_ack` block.

1. You just received the SYN-ACK packet, so update the receive sequence space to indicate the next byte you expect to receive after that.

2. The SYN-ACK packet is also acking your SYN packet, so update the send sequence space to indicate that the SYN has been acked.

3. Check if the SYN-ACK packet you got is actually acking the SYN you sent. (This if-case is given to you in the starter code.) If so, send an ACK packet by doing steps 4-7:

4. Set your next sequence number to be the ack number for the ACK packet.

5. Set the state to `ESTABLISHED`.

6. Call `self.set_pending_ack()` to express that you want to send an ACK.

7. Call `self.update_window(seg)`. More on this in a later stage.


### Testing and Debugging

To run all Stage 1 tests:

```bash
python autograder.py s1
```

Tests in this stage:

1. You send a SYN. You receive a SYN-ACK, and send an ACK.
   
   ```bash
   ../../pox.py config=./tests/s1_t1.cfg tcpip.pcap --node=r1 --no-tx
   ```

2. Same as test 1, but set the other side's initial sequence number close to a wraparound boundary.

   ```bash
   ../../pox.py config=./tests/s1_t2.cfg tcpip.pcap --node=r1 --no-tx
   ```

To run all tests so far:

```bash
python autograder.py all 1
```


## Stage 2: Receiving In-Order Data

### Overview

In this stage, you'll process packets arriving in-order.

For example, suppose you receive P1, P2, and P3, in the correct order. Then, you just need to copy each of their payloads to `self.rx_data`, and you're all done.

On the other hand, suppose P2 gets lost, and you receive P1 and P3. When you receive P1, you can copy its payload to `self.rx_data`. But when you receive P3, you can't copy its payload yet. For now, you'll drop P3 and send an ack requesting P2.


### Stage 2.1: Raw Receive

In `rx`, implement processing the raw TCP/IP packet that arrives. All your code here should be inside the `if self.acceptable_seg(seg, payload)` block.

1. If the segment is in-order (i.e. it is the next segment you are expecting), call `self.handle_accepted_seg(seg, payload)`.

2. Otherwise, the packet is out-of-order, so call `self.set_pending_ack()` to express that you want to send an ack.

Don't `return` from `rx` in your code, because you need the rest of the function to execute.


### Stage 2.2: Handle Accepted Segment

In `handle_accepted_seg`, implement processing the in-order packet that you handled in Stage 2.1.

1. If the state is one of `[ESTABLISHED, FIN_WAIT_1, FIN_WAIT_2]`, and the length of the payload is non-zero, call `handle_accepted_payload(payload)`.

Don't `return` from `handle_accepted_seg` in your code, because you need the rest of the function to execute.


### Stage 2.3: Handle Accepted Payload

In `handle_accepted_payload`, implement processing the payload that you handled in Stage 2.2.

1. You just received a packet, so update the receive sequence space to indicate the next byte you expect to receive after that.

2. You just received some payload, which is going to use up some of your buffer space. Decrease the receive window size accordingly.

3. You want to pass the payload to the user, so add the payload to the end of `self.rx_data`.

    Hint: `a += b` can be used to add bytes to the end of a byte array.

4. Call `self.set_pending_ack()` to indicate that you want to ack this data.


### Testing and Debugging

To run all Stage 2 tests:

```bash
python autograder.py s2
```

Tests in this stage:

1. You receive 1 packet with payload. Check that you processed the correct number of packets. Check sequence numbers. Check that the received data is correct.
   
   ```bash
   ../../pox.py config=./tests/s2_t1.cfg tcpip.pcap --node=r1 --no-tx
   ```

2. Same as test 1, but you receive 3 packets with payload.

   ```bash
   ../../pox.py config=./tests/s2_t2.cfg tcpip.pcap --node=r1 --no-tx
   ```

3. Same as test 1, but you receive 50 packets with payload.

   ```bash
   ../../pox.py config=./tests/s2_t3.cfg tcpip.pcap --node=r1 --no-tx
   ```

4. Same as test 1, but the packet gets dropped once. You should request that packet with an ack. Then, you receive the packet on the second try.

   ```bash
   ../../pox.py config=./tests/s2_t4.cfg tcpip.pcap --node=r1 --no-tx
   ```
5. Same as test 4, but you receive 3 packets, and the second packet gets dropped. You should request the second packet with an ack. Then, you receive the packet on the second try.

   ```bash
   ../../pox.py config=./tests/s2_t5.cfg tcpip.pcap --node=r1 --no-tx
   ```

6. Same as test 5, but you receive 15 packets, and every other packet (1, 3, 5, 7, etc.) gets dropped. Each packet is dropped at most once, so you receive packets on the second try.

   ```bash
   ../../pox.py config=./tests/s2_t6.cfg tcpip.pcap --node=r1 --no-tx
   ```

To run all tests so far:

```bash
python autograder.py all 2
```


## Stage 3: Receiving Out-of-Order Data

### Overview: Out-of-Order Packets

In Stage 2, if we received an out-of-order segment, we dropped it and sent an ack requesting the next in-order segment.

In this stage, we will improve on this by temporarily holding on to any out-of-order segments until they can be processed in order.

For example, suppose you want to receive P1, P2, and P3, but P2 gets dropped.

You receive P1 and process it.

Then, you receive P3. Instead of dropping P3 (as in the previous stage), you'll store P3 in your *receive queue*, and send an ack requesting P2.

Then, when you receive P2, you can process P2. Then, you can go to the receive queue and process P3 as well.

Notice that we temporarily store out-of-order packets, but we end up processing them in order.


### Overview: Packet Overlap

Sometimes, the other side has a bug, and you receive packets with overlapping payloads. For example, suppose you want to receive the bytestream:

```
P O T A T O
1 2 3 4 5 6
```

You might receive a packet with:

```
P O T A
1 2 3 4
```

and another packet with:

```
T A T O
3 4 5 6
```

If we just appended each payload to the end of the receive queue, the user would receive "POTATATO", which is incorrect. We need to remove the duplicate bytes from the beginning of the second packet.


### Code Overview: Receive Queue

The instance variable `self.rx_queue` is a `RecvQueue` object, which stores out-of-order packets.

The `RecvQueue` will automatically sort packets in sequence number order for you.

The `RecvQueue` object has the following relevant methods:

- `self.rx_queue.push(p)`: Adds packet `p` to the queue.
- `s, p = self.rx_queue.pop()`: Removes and returns the packet in the queue with the lowest sequence number.

  The return value is a tuple with the sequence number of the packet (`s`) and the packet itself (`p`).
- `s, p = self.rx_queue.peek()`: Returns, but does not remove, the packet in the queue with the lowest sequence number.

  The return value is a tuple with the sequence number of the packet (`s`) and the packet itself (`p`).
- `self.rx_queue.empty()`: Returns a boolean indicating whether the queue is empty.


### Stage 3.1: Inserting Into Receive Queue

In `rx`, implement inserting arriving packets into the receive queue. All your code here should be inside the `if self.acceptable_seg(seg, payload)` block.

1. In Stage 2.1, you modified `rx` to process incoming packets. You can now comment out that code.

2. When a packet arrives, simply insert it into `self.rx_queue`.


### Stage 3.2: Processing Receive Queue

Also in `rx`, implement processing any in-order segments from the receive queue. Each time we call `rx`, there's a chance that the next in-order segment is available for processing in the queue.

1. If the next packet (with smallest sequence number) in the queue is out-of-order, set a pending ack and stop checking the queue.

2. Otherwise, the next packet in the queue is in-order, so you should process it.
   
   Pop the packet from the queue.

   Extract its payload (using the hint to deal with overlaps).

   Call `self.handle_accepted_seg` to process the payload.

3. Repeat Steps 1 and 2 until all in-order packets in the queue have been handled.

Don't `return` from `rx` in your code, because you need the rest of the function to execute.


### Testing and Debugging

To run all Stage 3 tests:

```bash
python autograder.py s3
```

Tests in this stage:

1. You receive 1 packet with payload. The packet is dropped the first time, and sent on the second try. Check that the data is correctly received.
   
   ```bash
   ../../pox.py config=./tests/s3_t1.cfg tcpip.pcap --node=r1 --no-tx
   ```

2. You receive 3 packets. Every other packet (1, 3, 5, 7, etc.) gets dropped. Each packet is dropped at most once, so you receive packets on the second try.

   ```bash
   ../../pox.py config=./tests/s3_t2.cfg tcpip.pcap --node=r1 --no-tx
   ```

3. Same as test 2, but you receive 15 packets.

   ```bash
   ../../pox.py config=./tests/s3_t3.cfg tcpip.pcap --node=r1 --no-tx
   ```

4. Same as test 2, but set the other side's initial sequence number close to a wraparound boundary.

   ```bash
   ../../pox.py config=./tests/s3_t4.cfg tcpip.pcap --node=r1 --no-tx
   ```
5. Same as test 3, but set the other side's initial sequence number close to a wraparound boundary.

   ```bash
   ../../pox.py config=./tests/s3_t5.cfg tcpip.pcap --node=r1 --no-tx
   ```

To run all tests so far:

```bash
python autograder.py all 3
```


## Stage 4: Simple Sending of Data

### Overview

In stages 2 and 3, you implemented receiving data. In this stage, you'll implement sending data.

Recall that the user puts the bytes they want to send in `self.tx_data`, and your job is to send these out. You will remove bytes from the front of this array as you send them out.

However, you can't send out everything in `self.tx_data` immediately. The send window (`self.snd.wnd`) tells us how many outgoing bytes can be in flight at any given time. As we process acks, our send window will update, which will allow us to send more packets.


### Stage 4.1: Processing Acks

In `check_ack`, implement code to process incoming acks. All your code here should be inside the `if self.state in (ESTABLISHED, FIN_WAIT_1, FIN_WAIT_2, CLOSE_WAIT, CLOSING):` block.

1. If the ack number of the received segment represents a sent but unacked packet, call `self.handle_accepted_ack` on the segment.

    Hint: Check out [the send sequence space diagram](#send-sequence-space-txcontrolblock) for which sequence numbers have been sent but unacked.

2. If the ack number had already previously been acked before (i.e. this is an old ack), drop the packet.

    Allow the rest of `check_ack` to execute, but don't allow the rest of `handle_accepted_seg` to execute. (Hint: There's a Boolean variable to help you.)

3. If the ack number represents a byte you haven't sent yet, drop the packet.

    Don't allow the rest of `check_ack` to execute, and don't allow the rest of `handle_accepted_seg` to execute.


### Stage 4.2: Processing Accepted Acks

In `handle_accepted_ack`, implement code to process in-order acks. (We won't deal with out-of-order acks for now.)

1. Update the variable that keeps track of sent but unacknowledged sequence numbers.


### Stage 4.3: Creating Segments

In `maybe_send`, implement sending as much of the transmit buffer (`self.tx_data`) as allowable.

When creating packets to send, you need to maintain the following conditions:
- You can't send more data than what `self.tx_data` has.

    If `self.tx_data` becomes empty, there is nothing more to send on this call to `maybe_send`.

- You can't send more data than what your send window allows.

    Hint: Our solution sets `remaining` to the number of bytes you can still send, according to the window.

    Hint: Check out [the send sequence space diagram](#send-sequence-space-txcontrolblock) and think about which range corresponds to the bytes you have not sent yet, but can still send.

- Each payload you send must be less than or equal to `self.mss` bytes in size.

Use `self.new_packet` to create new packets, and use `self.tx` to send out packets.

When you send out some bytes, remember to remove those bytes from the front of the `self.tx_data` array.


### Stage 4.4: Transmitting Segments

In `tx`, implement code to update your send sequence space when you transmit a packet. All your code here should be inside the `if (p.tcp.SYN or p.tcp.FIN or p.tcp.payload) and not retxed:` block.

1. If the packet contains non-empty payload, update the next sequence number to be sent in your send sequence space.


### Testing and Debugging

To run all Stage 4 tests:

```bash
python autograder.py s4
```

Tests in this stage:

1. You send 1 packet. Check that the other side receives all data correctly.
   
   ```bash
   ../../pox.py config=./tests/s4_t1.cfg tcpip.pcap --node=r1 --no-tx
   ```

2. Same as test 1, but you send 3 packets.

   ```bash
   ../../pox.py config=./tests/s4_t2.cfg tcpip.pcap --node=r1 --no-tx
   ```

3. Same as test 1, but you send 50 packets.

   ```bash
   ../../pox.py config=./tests/s4_t3.cfg tcpip.pcap --node=r1 --no-tx
   ```

4. Same as test 2, but set the other side's initial sequence number close to a wraparound boundary.

   ```bash
   ../../pox.py config=./tests/s4_t4.cfg tcpip.pcap --node=r1 --no-tx
   ```
5. Same as test 3, but set the other side's initial sequence number close to a wraparound boundary.

   ```bash
   ../../pox.py config=./tests/s4_t5.cfg tcpip.pcap --node=r1 --no-tx
   ```

To run all tests so far:

```bash
python autograder.py all 4
```


## Stage 5: Honoring Advertised Window

### Overview

Welcome to the shortest stage of this project.

In Stage 4, you obeyed the send window when sending data (e.g. you couldn't send too many packets in-flight).

However, so far, we've set `self.snd.wnd` to a `self.TX_DATA_MAX`, which is not correct.

Each time you receive a packet, the other side advertises how much buffer space it has left. You should use this advertised value to adjust your send window size, so you aren't flooding the other side with packets.

Note: In real life, you'd also implement congestion control and set `self.snd.wnd` to avoid flooding the network, but we'll skip that for this project.


### Stage 5.1: Updating Window Size

In `update_window`, implement setting the window size correctly.

1. Assign the send window to the value advertised in the segment.


### Testing and Debugging

To run all Stage 5 tests:

```bash
python autograder.py s5
```

Tests in this stage:

1. Set the other side's receive window to 1 byte. You have 300 bytes to send. Check that you send 300 packets.
   
   ```bash
   ../../pox.py config=./tests/s5_t1.cfg tcpip.pcap --node=r1 --no-tx
   ```

2. Set the other side's receive window to 199 bytes. You have 1990 bytes to send. Check that you send 10 packets.

   ```bash
   ../../pox.py config=./tests/s5_t2.cfg tcpip.pcap --node=r1 --no-tx
   ```

To run all tests so far:

```bash
python autograder.py all 5
```

If all tests from Stages 1–5 pass at this point, you are done with Project 3A! Don't forget to submit to the Project 3A autograder on Gradescope.

To submit Project 3A, submit only the `student_socket.py` file to the [Project 3A autograder on Gradescope]({{ site.data.projects.projects[3].gradescope}}).


## Stage 6: Passive Close

### Overview

We now turn to connection teardown. In TCP, there are two graceful shutdown procedures:
- Passive close: The other side closes first. (Stage 6.)
- Active close: You close first. (Stage 7.)

Here's a diagram showing what happens in passive close:

<img src="/assets/projects/proj3/passive-close.svg" width="900px" alt="Passive close diagram. In Established state, you and other side can both send payloads. You receive FIN, and send ACK, moving into Close Wait state, where only you can send payloads. You're done and you send FIN, moving into Last Ack, where neither side can send payloads. You receive ACK (for your FIN) and move into Closed.">

- You are in the `ESTABLISHED` state.
- You receive a FIN packet. In response, you send an ACK, and transition to `CLOSE_WAIT`.
- When you're done, you send a FIN, and transition to `LAST_ACK`.
- When you receive an ACK, you transition to `CLOSED`.


### Code Overview: Fin Control

The instance variable `self.fin_ctrl` has two useful methods for connection teardown.

- `self.fin_ctrl.set_pending(next_state=None)`: Sometimes you know you want to send a FIN, but you actually can't do it yet because there's still some data waiting in the transmit buffer. So, when you want to send a FIN, you should call this function.

    If `next_state` is supplied as an argument, you'll move to that state after the FIN is actually sent.

- `self.fin_ctrl.acks_our_fin(ack)`: Takes in `ack`, an ack number from an incoming packet. Returns True if the FIN you sent has been acked.


### Stage 6.1: Receiving FIN

In `handle_accepted_fin`, implement receiving a FIN from the other side.

1. If you are in the `ESTABLISHED` state, do steps 2–4.

2. In the receive sequence space, update the next sequence number you expect to receive.

3. Call `self.set_pending_ack()` to ack the FIN.

4. Update `self.state`.


### Stage 6.2: Sending FIN

In `close`, implement sending a FIN. All your code here should be inside the `elif self.state is CLOSE_WAIT:` block.

1. Set a pending FIN. Remember to provide an argument specifying which state to move to after the FIN is sent.

    Hint: Use a method of `self.fin_ctrl`.


### Stage 6.3: Receiving ACK

In `check_ack`, implement receiving an ACK (acking your FIN) and closing the connection. All your code here should be in the `elif self.state == LAST_ACK:` block.

1. If the ack number in the segment you just received is acking the FIN you sent, then call `self._delete_tcb()` to delete the connection state.

    Hint: Use a method of `self.fin_ctrl`.


### Testing and Debugging

To run all Stage 6 tests:

```bash
python autograder.py s6
```

Tests in this stage:

1. Do a 3-way handshake.

   The 4th packet is a FIN from the other side.

   The 5th packet is an ACK from you.

   The 6th packet is a FIN from you.

   The 7th packet is an ACK from the other side.

   Check that you transition to the correct states during passive close.
   
   ```bash
   ../../pox.py config=./tests/s6_t1.cfg tcpip.pcap --node=r1 --no-tx
   ```

2. Same as test 1, but set the other side's initial sequence number close to a wraparound boundary.

   ```bash
   ../../pox.py config=./tests/s6_t2.cfg tcpip.pcap --node=r1 --no-tx
   ```

To run all tests so far:

```bash
python autograder.py all 6
```


## Stage 7: Active Close

### Overview

In Stage 6, you implemented passive close, where the other side closes first. In this stage, you'll implement active close, where you close first.

Here's a diagram showing what happens in active close:

<img src="/assets/projects/proj3/active-close.svg" width="900px" alt="Passive close diagram. In Established state, you and other side can both send payloads. You're done, and you send FIN, moving into FIN_WAIT_1, where only the other side can send payloads. Path 1: You receive ACK and move into FIN_WAIT_2. You receive FIN and send ACK, moving into TIME_WAIT. Path 2: You receive FIN-ACK and send ACK, moving into TIME_WAIT. Path 3: You receive FIN, and send ACK, moving into CLOSING. YOu receive ACK, moving into TIME_WAIT. In TIME_WAIT, neither side can send payloads. The timer waits, and then we move into CLOSED.">

- You are in the `ESTABLISHED` state.
- When you're done, you send a FIN, and transition to `FIN_WAIT_1`.

At this point, there are 3 possible paths that our state transition can follow. In all 3 paths, our goal is to reach the `TIME_WAIT` state.

1. If you receive just an ACK, the other side has more to say.

    You transition to `FIN_WAIT_2`, where you listen for more data until the other side sends a FIN.

    Eventually, you receive a FIN, and in response, you send an ack and transition to `TIME_WAIT`.

2. If you receive a FIN-ACK, the other side is also done at the same time as you.

    In response, you send an ack and immediately transition to `TIME_WAIT`.

3. If you receive a FIN, the other side is also done, but has not acked your FIN yet.

    You transition to `CLOSING`, where you listen for the other side acking your FIN.

    Eventually, you receive an ack for your FIN, and transition to `TIME_WAIT`.

Once you reach `TIME_WAIT`, you'll start a timer and wait a little bit, and then transition to the `CLOSED` state.


### Code Overview: Starting Timer

To transition to the `TIME_WAIT` state, you should call `self.start_timer_timewait()`.

This method will transition to `TIME_WAIT` for you, and will also start the timer to wait a bit before transitioning to `CLOSED`.


### Stage 7.1: Sending FIN

In `close`, implement sending a FIN. All your code here should be inside the `elif self.state is ESTABLISHED:` block.

1. Set a pending FIN. Remember to provide an argument specifying which state to move to after the FIN is sent.

    Hint: Use a method of `self.fin_ctrl`.


### Stage 7.2: Receiving FIN

In `handle_accepted_fin`, implement all 3 state transitions in the diagram where you receive a FIN.

1. If you're in `FIN_WAIT_1` and you receive a FIN-ACK packet, then:

    Set a pending ack.

    Transition immediately to `TIME_WAIT`. Hint: Use `self.start_timer_timewait()` to transition.

2. Otherwise, if you're in `FIN_WAIT_1` and you receive just a FIN packet (ACK flag not set), then:

    Set a pending ack.

    Transition to `CLOSING`.

3. Otherwise, if you're in `FIN_WAIT_2` and you receive a FIN packet, then:

    In the receive sequence space, update the next sequence number you expect to receive.

    Set a pending ack.

    Transition to `TIME_WAIT` using `self.start_timer_timewait()`.


### Stage 7.3: Receiving ACK

In `check_ack`, implement both state transitions in the diagram where you receive an ack.

1. If you're in `FIN_WAIT_1` (this if-check is already in the starter code), then:

    Use a `self.fin_ctrl` method to check if the ack number in the segment you just received is acking the FIN you sent.

    If so, then transition to `FIN_WAIT_2`.

2. If you're in `CLOSING` (this if-check is already in the starter code), then:

    Use a `self.fin_ctrl` method to check if the ack number in the segment you just received is acking the FIN you sent.

    If so, then transition to `TIME_WAIT` using `self.start_timer_timewait()`.


### Testing and Debugging

To run all Stage 7 tests:

```bash
python autograder.py s7
```

Tests in this stage:

1. You're in `ESTABLISHED` and you send a FIN. You should transition to `FIN_WAIT_1`.

   You receive an ACK. You should transition to `FIN_WAIT_2`.

   Then, you receive a FIN. You should send an ACK and transition to `TIME_WAIT`.

   The timer should go off and you should transition to `CLOSED`.
   
   ```bash
   ../../pox.py config=./tests/s7_t1.cfg tcpip.pcap --node=r1 --no-tx
   ```

2. You try to close the connection while there's still some data in the transmit buffer.

   You should wait until all data is sent before sending the FIN.

   ```bash
   ../../pox.py config=./tests/s7_t2.cfg tcpip.pcap --node=r1 --no-tx
   ```

To run all tests so far:

```bash
python autograder.py all 7
```


## Stage 8: Retransmitting Packets

### Overview

In Stage 4, you implemented simple in-order sending of segments. However, you didn't handle the case where packets you sent are lost. In this stage, you'll handle dropped packets by retransmitting them.

In this project, the first time you send a packet, you will:
- Tag the packet with a timestamp, indicating when the packet was originally transmitted.
- Add the packet to a *retransmit queue*.

When a packet gets acked, you can remove that packet from the retransmit queue.

Every 100 milliseconds, the oldest packet (lowest sequence number) in the retransmit queue is inspected to see if it has expired. If so, that packet is retransmitted.

What does it mean for a packet to expire? In this stage, we'll say a packet needs to be acked within 1 second, or else it's expired.

Note: We only add packets to the queue the first time they are transmitted. We do not add retransmitted packets to the queue.

Note: Only SYNs, FINs, and data packets with payload are retransimtted. We don't need to retransmit ACKs.


### Code Overview: Retransmit Queue

The instance variable `self.retx_queue` is a `RetxQueue` object, which stores packets that have been sent, but not acked yet (and therefore might need to be retransmitted).

The `RetxQueue` expects that you will push packets in increasing sequence number, since you should be pushing packets in the same order that you first transmit them.

The `RetxQueue` object has the following relevant methods:

- `self.retx_queue.push(p)`: Adds packet `p` to the queue.
- `self.retx_queue.pop_upto(seq_no)`: Removes and returns all packets whose sequence number is less than `seq_no`.

  The return value is a list of tuples, where each tuple contains the sequence number of the packet, and the packet itself.
- `self.retx_queue.get_earliest_pkt()`: Returns, but does not remove, the packet in the queue with the lowest initial transmission timestamp (`tx_ts`).

  The return value is a tuple with the sequence number of the packet (`s`) and the packet itself (`p`).
- `self.retx_queue.empty()`: Returns a boolean indicating whether the queue is empty.


### Stage 8.1: First Transmission

In `tx`, implement code to tag packets with a timestamp and add them to the retransmit queue. All your code here should be inside the `if (p.tcp.SYN or p.tcp.FIN or p.tcp.payload) and not retxed:` block.

1. Tag the packet with the current time.

    Hint: `p.tx_ts` and `self.stack.now` will be helpful.

2. Add the packet to the retransmit queue.


### Stage 8.2: Removing Acked Packets

In `handle_accepted_ack`, implement code that removes any acked packets from the retransmit queue.

1. Use the ack number in the received segment (`seg`) to remove all acked packets from the retransmit queue.

    Hint: Use a method of `self.retx_queue`.


### Stage 8.3: Retransmitting Packets

In `check_timer_retx`, implement code that retransmits the earliest packet if it's expired.

1. If the retransmit queue is not empty, get the earliest packet, and check if it's expired.

    Hint: Reassign `time_in_queue` to how long the earliest packet has been in the queue.

2. If the packet is expired, resend the packet. This is already done for you in the starter code.

    Note that in the starter code, we call `self.tx(p, retxed=True)`. The `retxed=True` argument helps to ensure we don't put the packet in the retransmit queue again.


### Testing and Debugging

To run all Stage 8 tests:

```bash
python autograder.py s8
```

Tests in this stage:

1. You send data.

    The other side drops acks that correspond to the beginning of the payload, but does not drop acks that correspond to the end of the payload.

    You should not retransmit any packets.
   
   ```bash
   ../../pox.py config=./tests/s8_t1.cfg tcpip.pcap --node=r1 --no-tx
   ```

2. You send one packet. The packet is dropped once. You should retransmit the packet after 1 second.

   ```bash
   ../../pox.py config=./tests/s8_t2.cfg tcpip.pcap --node=r1 --no-tx
   ```

3. You send 10 data packets. Every other packet is dropped. Each packet is dropped at most once, so your packets are successfully sent on the second try.

    Check that you properly retransmit any dropped packets.

   ```bash
   ../../pox.py config=./tests/s8_t3.cfg tcpip.pcap --node=r1 --no-tx
   ```

To run all tests so far:

```bash
python autograder.py all 8
```


## Stage 9: Updating RTO by Estimating RTT

### Overview

In the previous stage, you retransmitted a packet if it was not acked within 1 second. In this stage, you'll implement a smarter way to estimate when to retransmit a packet.

`self.rto` represents RTO (Retransmission Timeout), the amount of time to wait before retransmitting a packet. In the previous stage, we hard-coded RTO to be 1 second.

Recall that the RTT (Round Trip Time) is the time between sending a packet and receiving an ack for that packet. Ideally, we'd like our RTO to be equal to one RTT, so that we wait one RTT before retransmitting the packet.

In order to estimate the RTT, you can use the `tx_ts` attribute that you set each time you transmitted a packet for the first time.


### Code Overview: RFC 6298

[RFC 6298](https://tools.ietf.org/html/rfc6298) describes an algorithm for estimating the RTT based on how long it took for each packet to be acked.

It uses the following constants (already implemented for you, do not modify them):

```python
self.alpha = 1.0/8
self.beta = 1.0/4
self.K = 4
self.G = 0 # clock granularity
```

It uses 3 variables to maintain a moving average of the RTT and set the RTO accordingly. They are initialized as follows (and you will update them in your code):

```python
self.rto = 1    # retransmission timeout
self.srtt = 0   # smoothed round-trip time (estimated RTT)
self.rttvar = 0 # round-trip time variation (estimated standard deviation)
```

When the first RTT measurement `R` is made, you should set:

```python
srtt = R
rttvar = R / 2
rto = srtt + max(G, K * rttvar)
```

When a subsequent RTT measurement `R` is made, you should set:

```python
rttvar = (1-beta) * rttvar + beta * abs(srtt - R)
srtt = (1-alpha) * srtt + alpha * R
rto = srtt + max(G, K * rttvar)
```


### Stage 9.1: Updating RTO on Acks

In `update_rto`, implement the RFC 6298 algorithm to update the RTO based on the ack you received.

1. Use `acked_pkt` (the ack you received) to compute `R`, an estimate of the RTT based on this particular packet.

2. Update any variables as described in RFC 6298.

3. Clamp `self.rto` so that it is at most `self.MAX_RTO`, and at least `self.MIN_RTO`.


### Stage 9.2: Processing Acks

In `handle_accepted_ack`, implement calling `update_rto` for any ack that you receive.

1. The call to `self.update_rto` is already in the starter code. Assign `acked_pkts` so that it contains the acks that you want `self.update_rto` to process.

    Hint: See your code in Stage 8 directly above this part of the code.


### Stage 9.3: Processing Retransmissions

In `check_timer_retx`, implement doubling the RTO any time you resend the packet. All your code here should be inside the `if time_in_queue > self.rto` block.

1. Double `self.rto`.

2. Clamp `self.rto` so that it never exceeds `self.MAX_RTO`.


### Testing and Debugging

To run all Stage 9 tests:

```bash
python autograder.py s9
```

Tests in this stage:

1. You send 100 packets, one packet every 25 ms.

    The simulator sets the link latency to 200ms.

    By the end of the simulation, `srtt` should be within 10% of the true RTT.

    Check that all your data reaches the other side.
   
   ```bash
   ../../pox.py config=./tests/s9_t1.cfg tcpip.pcap --node=r1 --no-tx
   ```

2. Same as test 1, but set the link latency to 500ms.

    By the end of the simulation, `srtt` should be within 5% of the true RTT.

   ```bash
   ../../pox.py config=./tests/s9_t2.cfg tcpip.pcap --node=r1 --no-tx
   ```

3. Same as test 1, but drop roughly 4% of the packets you send.

    By the end of the simulation, `rto` should be less than 32, `srtt` should be less than 16, and `rttvar` should be more than 1.5.

   ```bash
   ../../pox.py config=./tests/s9_t3.cfg tcpip.pcap --node=r1 --no-tx
   ```

To run all tests so far:

```bash
python autograder.py all 9
```


## Submission and Grading

Congratulations! You've implemented TCP.

If you fully finished the project, the tests from all 9 stages should pass:

```bash
python autograder.py all
```

To submit Project 3B, submit only the `student_socket.py` file to the [Project 3B autograder on Gradescope]({{ site.data.projects.projects[4].gradescope}}).

Your grade is entirely computed from the unit tests, split equally among the 9 stages. Within each stage, all tests are weighted equally.

If you completed the project honestly (no academic misconduct), then the grade you see on Gradescope is the grade you get.
