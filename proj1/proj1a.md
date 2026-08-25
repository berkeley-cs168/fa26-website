---
layout: page
title: "Project 1A: Basic Traceroute"
nav_order : 3
parent: "Project 1: Traceroute"
---

# Project 1A: Basic Traceroute

In this project, you will write a traceroute implementation in Python.

Lectures needed for this project: None. The [Traceroute Guide](/proj1/guide) has all the information you need for this project. Lectures 1–3 (introduction lectures) may be helpful, though.

You can work on this project alone, or with one partner. Reminder: [Our course policies page has a collaboration policy](/policies) that you must follow.


## Project Overview

### Breakdown

This project is split into two parts: Project 1A and Project 1B. You need to finish Project 1A before you can start Project 1B.

Project 1A is split into stages to guide you through the project. You don't need to follow the stages exactly in order. You can even ignore the stages and just implement traceroute from scratch if you want.


### Testing and Debugging

The autograder tests are not provided to you locally, but you can submit to the autograder as many times as you'd like. The score you see on Gradescope is your score for the project (there are no hidden tests).

Another useful way to debug is to run your traceroute implementation on a real-life IP, and see if the results make sense. Example:

```bash
sudo python3 traceroute.py cmu.edu
```


## Code Overview

You will be implementing the `traceroute` function in `traceroute.py`.

Some useful helper functions for sending and receiving packets are implemented in `util.py` (you can read this file, but don't modify it). We've also described them below.


### Your Task: Traceroute

Your goal is to implement the `traceroute` function so that it reveals all the routers between your computer and the specified destination.

Traceroute takes in three arguments:
- `ip` (string) is the specified destination.
- `sendsock` is an object you can use to send packets (more details below).
- `recvsock` is an object you can use to receive packets (more details below).

Traceroute returns a list of lists, with the following properties:
- The `i`th sublist contains the IP addresses of all the routers you found that are distance `i+1` away.
- For example, the 0th sublist contains all the routers that are distance 1 away.
- The routers inside each sublist can be in any order.
- If no routers were found for a certain distance, the `i`th sublist can be empty.
- If `ip` is discovered, the final sublist should just be `[ip]`.

Traceroute should also call `util.print_result` (more details below) on each sublist to display the routers at each distance. We won't grade the printed output, but it's useful for debugging (also, it looks cool).


### Example of Calling Traceroute

Example: Suppose the network topology looks like this.

<img src="/assets/projects/proj1/multi-path.svg" width="900px" alt="Two Path Topology: Your Computer to 1.1.1.1 to 2.2.2.2 to 3.3.3.3 to 6.6.6.6 to 7.7.7.7 (Destination), and Your Computer to 1.1.1.1 to 4.4.4.4 to 5.5.5.5 to 6.6.6.6 to 7.7.7.7 (Destination)">

Assuming no errors occur, and our probes hit every router, then calling `traceroute` with `ip=7.7.7.7` should return something like this:

```python
[["1.1.1.1"],
 ["2.2.2.2", "4.4.4.4"],
 ["3.3.3.3", "5.5.5.5"],
 ["6.6.6.6"],
 ["7.7.7.7"]]
```

Also, `traceroute` should print output that looks something like this:

```
traceroute to 7.7.7.7
 1: 1.1.1.1
 2: 2.2.2.2
    4.4.4.4
 3: 3.3.3.3
    5.5.5.5
 4: 6.6.6.6
 5: 7.7.7.7
```


### Sending Packets

`sendsock` is an object you can use to send outgoing packets. It has the following useful methods:

- `sendsock.set_ttl(ttl)` sets the TTL to that number for all subsequent outgoing packets.
    - `ttl` (integer) is the TTL to set.
- `sendsock.sendto(msg.encode(), (ip, port))` sends an outgoing packet.
    - `msg` (string) is the UDP payload of the packet. We call `encode` to convert the string to raw bytes.
    - `ip` (string) is the destination IP address.
    - `port` (integer) is the destination port.

Example usage:

```python
# Send a packet "Hello" to 4.4.4.4, port 33434, with TTL 12.
sendsock.set_ttl(12)
sendsock.sendto("Hello".encode(), ("4.4.4.4", 33434))

# Send a packet "Potato" to 5.5.5.5, port 33464, with TTL 20.
sendsock.set_ttl(20)
sendsock.sendto("Potato".encode(), ("5.5.5.5", 33464))
```


### Receiving Packets

`recvsock` is an object you can use to receive incoming packets. It has the following useful methods:

- `recvsock.recv_select()` checks if there are any incoming packets available to be received.
   - If there is at least one packet to be received, it returns True.
   - Otherwise, the function waits until a packet is available, or a timeout expires.
   - If the timeout expires, it returns False.
- `buf, address = recvsock.recvfrom()` receives a single incoming packet.
   - It returns `buf`, the raw bytes of the packet (e.g. IPv4 header, followed by a UDP or ICMP header, followed by payload). To print out the bytes, you can call `.hex()` on the raw bytes.
   - It also returns `address`, a tuple containing the IP address and port that sent the packet.
   - If there is no packet to be received, the function throws an exception. Therefore, you should always call `recvsock.recv_select()` before calling `recvsock.recvfrom()`.

Example usage:

```python
if recvsock.recv_select():  # Check if there's a packet to process.
    buf, address = recvsock.recvfrom()  # Receive the packet.

    # Print out the packet for debugging.
    print(f"Packet bytes: {buf.hex()}")
    print(f"Packet is from IP: {address[0]}")
    print(f"Packet is from port: {address[1]}")
```


### Print Result

`print_result(routers: list[str], ttl: int)` can be used to print nicely-formatted output.

It takes in a TTL and a list of IP addresses (all the routers found by probing with that TTL).

Your implementation should call this function once for every TTL you probe.

Example usage:

```python
util.print_result(["128.2.255.210", "128.2.42.10"], 7)
```

This will cause the TTL, the IP addresses of both machines, and their corresponding names, to be printed out nicely, like this:

```
 7: POD-D-DCNS-CORE2.GW.CMU.NET (128.2.255.210)
    CMU-VIP.ANDREW.CMU.EDU (128.2.42.10)
```


## Stage 1: Run Traceroute Manually

Now that we know how to send and receive packets, let's try running traceroute manually. There's nothing to submit for this stage, but we think it'll help for later stages.

For this stage, you can temporarily comment out these lines of starter code:

```python
# for ttl in range(1, TRACEROUTE_MAX_TTL+1):
#     util.print_result([], ttl)
# return []
```

1. In `traceroute`, write code that sends a packet to `ip` with a TTL of 1 and prints out the reply packet (as raw hex bytes). The payload can be any short message you want, e.g. "Potato." The starter code has destination port numbers you can use.
2. Then, try running that code:
    ```bash
    sudo python3 traceroute.py cmu.edu
    ```
3. Copy-paste the bytes of the reply packet into [an online packet decoder like this one](https://hpd.gasmi.net/). You might have to ask the decoder to parse the packet as an IPv4 packet (since we've already stripped away the Layer 2 header).
4. Use the packet decoder and your knowledge of headers to read this packet. Some things to investigate:
    - Who sent the reply packet? Was it cmu.edu, or some intermediate router?
    - Did you discover an intermediate router? If so, what is its IP address? How did you learn that IP address from the headers?
    - What is the intermediate router trying to tell you? Is it trying to report an error? If so, how do you know what it's trying to say?
    - What are all the different headers in this packet, and why are they here?
5. Back in `traceroute`, try changing the TTL to 2, 3, 4, or 5, and re-running the code to send out the packet with your new TTL:
    ```bash
    sudo python3 traceroute.py cmu.edu
    ```
    You might have to try a few different TTLs here, since some routers along your path to cmu.edu might not reply to you.
6. Copy-paste the bytes of the new reply packet into the packet decoder, and investigate the packet.
    - Did you discover a new intermediate router?
    - What is the packet trying to tell you?
7. Back in `traceroute`, try changing the TTL to 30. This should be enough hops to reach cmu.edu. Re-run the code to send out the packet with your new TTL:
    ```bash
    sudo python3 traceroute.py cmu.edu
    ```
8. Copy-paste the bytes of the new reply packet into the packet decoder, and investigate the packet.
    - Who sent the reply packet? Was it cmu.edu, or some intermediate router?
    - What is the packet trying to tell you? How do you know?


### Testing and Debugging

Running `traceroute.py` manually on your computer can be finicky, because you're actually contacting real-life routers, which might do weird things.

Some real-life routers you probe might give you weird output, or no output at all. It's okay to ignore these routers. All of the routers in the autograder will behave exactly as we describe in the spec.

Here's one common example of weird real-life router behavior that we've noticed. When you try to decode a packet, you might sometimes get an error saying "the packet is invalid/malformed," like this:

<img src="/assets/projects/proj1/little-endian-length-1.png" width="900px" alt="Packet decoder with little-endian length field.">

In the IPv4 header of this packet, the two bytes corresponding to the Total Length field are 0x24 and 0x00. (Make sure you understand how we found these two bytes!)

This project (our autograder, your code, and the online packet decoder) expects that these bytes are sent in big-endian, which means you'd read the total length to be 0x2400 = 9216:

<img src="/assets/projects/proj1/little-endian-length-2.png" width="900px" alt="Packet decoder with little-endian length field.">

The packet is definitely not 9216 fields, though.

If you read the bytes as little-endian instead, you'd read the total length to be 0x0024 = 36. The IPv4 payload is indeed 36 bytes in this packet.

In summary, it seems like some real-life routers might fill in the IPv4 Total Length field differently:
- Some use little-endian, and some use big-endian.
- Some calculate length of payload and header, and some calculate length of payload only.

The screenshots above show a router that calculated the length of payload only, and sent the length in little-endian. This confused the packet decoder (and confuse your code as well).

The routers on the autograder will always use big-endian, and calculate length of the payload and the header, so you don't need to worry about weird routers like this one when writing your code.


## Stage 2: Parsing Packets

When you ran traceroute manually, each time you received a packet, you probably had to interpret the bytes of the various headers. Your traceroute implementation will need to do this parsing in code!


### Your Task

To help you parse headers in code, we've provided three helper classes, corresponding to the three relevant protocols in the packets you'll receive: `IPv4`, `ICMP`, and `UDP`.

These classes are missing constructors. Your job is to fill in the constructors (the `__init__` functions).

The constructors take in `buffer`, which are the raw bytes of of the packet header. For example, the constructor in `ICMP` takes in the bytes of the ICMP packet header.

Your constructor code should initialize all of the instance variables by parsing the bytes of the given packet.


### Hints

`buffer` is a raw byte array, but you'll need to extract individual bits. This line of code converts `buffer` to a bitstring, `b`, that you can perform string operations on:

```python
b = ''.join(format(byte, '08b') for byte in [*buffer])
```

This line of code converts a bitstring into an integer:

```python
bitstring = '10010'          # A string of 1s and 0s.
number = int(bitstring, 2)   # 2, because it's a base-2 number.
print(number)                # Prints 18.
```


### Testing and Debugging

From Stage 1, you have code that sends a packet and receives a reply packet.

To check your parsing code, you can call the constructor on the raw bytes you receive (slicing if needed to extract specific headers), and print the output. The classes already have `__str__` methods implemented for you to help the output look nice.

Then, you can compare the printed output with the online packet decoder to see if you're parsing packets correctly.

When your parser looks good, [you can submit to Gradescope to check your implementation]({{ site.data.projects.projects[0].gradescope}}).

A correct implementation should pass these tests:
- A1. IPv4 Header (Basic)
- A2. IPv4 Header (Random)
- A3. ICMP Header
- A4. UDP Header


## Stage 3: Basic Traceroute

Time to implement traceroute!


### Your Task

Fill in `traceroute` to discover all routers between you and the destination `ip`.

Some reminders:
- The payload can be any short message you want, e.g. "Potato."
- For each TTL, you should send `PROBE_ATTEMPT_COUNT` packets with that TTL. 
- An IP should only be listed once per sublist (no duplicates in a sublist).
- When you see a response from the destination (`ip`), you should stop and return the routers you discovered along the way.
- The constructors you just wrote will be helpful for parsing packets.


### Testing and Debugging

You can run your implementation to see if your output looks similar to the output of a real-world traceroute:

```python
traceroute cmu.edu                  # A real traceroute.
sudo python3 traceroute.py cmu.edu  # Your traceroute.
```

Your output probably won't be exactly the same, because packets could travel along different paths each time you run traceroute.

When your traceroute output looks good, you can [submit to Gradescope to check your implementation]({{ site.data.projects.projects[0].gradescope}}).

A correct implementation should pass these tests (in addition to the other tests so far):
- A5. Test Random Traceroute
- A6. Test Multipath


## Submission and Grading

Congratulations! You've implemented a basic traceroute implementation.

To submit Project 1A, submit only the `traceroute.py` file to the Project 1A autograder on [Gradescope]({{ site.data.projects.projects[0].gradescope}}).

As long as you followed the academic integrity policies, the score you see on Gradescope is your final score for Project 1A (no hidden tests).