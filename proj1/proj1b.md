---
layout: page
title: "Project 1B: Traceroute Error Handling"
nav_order : 4
parent: "Project 1: Traceroute"
---

# Project 1B: Traceroute Error Handling

Project 1B picks up where you left off in Project 1A. You'll continue working on the same `traceroute.py` file.

In Project 1A, we assumed that the network works perfectly, but in real life, weird things can happen:
- The network could have bugs that cause you to receive duplicate packets, or unrelated packets.
- The network could also drop packets, or routers and hosts might refuse to send reply packets.

In Project 1B, you'll enhance your existing traceroute implementation to handle various errors that might occur in the network.

You can work on this project alone, or with one partner. Reminder: [Our course policies page has a collaboration policy](/policies) that you must follow.


## Project Overview

### Breakdown

In Project 1B, you can handle the errors in any order you want. We've roughly ordered them by difficulty, from easiest to hardest.

We suggest reading through all of the errors before you start, and thinking carefully about your design to keep your code clean. If you think about every test individually, without seeing the bigger picture, you might fix one test and accidentally break a test you were previously passing.


### Score

Don't stress about getting a perfect score on this project. Most tests can be passed if you carefully follow the spec, but a few tests are intentionally quite difficult to pass.

For reference, here is the distribution of grades from Spring 2024 (it was out of 105 points instead of 100 in that semester):

<img src="/assets/projects/proj1/sp24-traceroute-distribution.png" width="1200px" alt="Minimum is 0/105, maximum is 105/105, mean is 93.33/105, median is 100/105, standard deviation is 17.75">


### Tips for Clean Code

This project doesn't require a lot of code to complete. The staff solution is 100-200 lines long.

However, a clean, concise implementation requires thinking carefully about your design before you start coding. If your code is gross, we won't look at it, and we'll tell you to clean it up first.

- Don't guess-and-check. If you just try stuff and see if it works, you'll probably waste a bunch of time. Make sure that you deeply understand what every line of code is doing.
- Clean up your code as you go. Often, if you refactor your code to make it more readable, whatever bug you're stuck on will pop out at you.
- Don't write highly-nested code. Often, in network programming, we have to check several conditions for an error before processing a packet. This is gross and hard to debug:
    ```python
    def morning_routine():
        if condition1:
            if condition2:
                if condition3:
                    if condition4:
                        take_shower()

        put_on_clothes()
    ```
- Instead, something like this is much better. On a related note, helper functions will be very helpful for cleaning up your code. My solution uses 2 helper functions, and other solutions use even more.
    ```python
    def morning_routine():
        take_shower_helper()
        put_on_clothes()

    def take_shower_helper():
        if not condition1:
            return

        if not condition2:
            return

        if not condition3:
            return

        if not condition4:
            return

        take_shower()
    ```


### Testing and Debugging

For most of Project 1B, the main way to check your implementation will be to [submit your code to Gradescope]({{ site.data.projects.projects[1].gradescope}}).

Unfortunately, in network programming, there are many situations that can be extremely difficult or reproduce locally. As a result, in real-world network programming, you will often be in a situation where the only path to figuring a bug out, is to take a step back, read your code carefully, trace what it does in your head, and think through how that code could produce the problems you're encountering.

I want to stress, I know this is hard and even painful at times, but if you take the time to do this, it will save you time.

Remember to keep the coding tips in mind. If your code is unreadable, you'll have a harder time finding any bugs.


## Avoiding Unnecessary Timeouts

### Receiving Packets: Avoiding Timeouts

Recall our code for receiving packets:

```python
if recvsock.recv_select():  # Check if there's a packet to process.
    buf, address = recvsock.recvfrom()  # Receive the packet.
```

As you modify your code, you'll need to be careful about unnecessary timeouts with `recv_select`, because they will slow down your code.

For example, if you use this code to receive two packets, the third call to `recv_select` will always timeout (because there's no third packet to receive):

```python
def read_two_packets_slow():
    while recvsock.recv_select():
        recvsock.recvfrom()
```

By contrast, in this code, both calls to `recv_select` returns True (corresponding to the two packets you receive), and there are no timeouts:

```python
def readtwo_packets_fast():
    if recvsock.recv_select():
        recvsock.recvfrom()
    if recvsock.recv_select():
        recvsock.recvfrom()
```


### Test B1: Avoiding Unnecessary Timeouts

This test runs traceroute on a simple topology, with no packet drops or other errors (similar to Test Random Traceroute from Project 1A).

The test checks that your code does not time out on a call to `recv_select` in this simple case.

Instead, all calls to `recv_select` must return True due to packet reception.


### Tips

Your Project 1A implementation might already be passing this test. However, as you implement more features in Project 1B, your changes might start causing this test to fail. We suggest keeping this test in mind as you work through the project.

Usually, if you're failing this test, it's because you're attempting to drain the entire receive queue (like the whlie loop above) before moving on to the next TTL. This technically works, but will be very slow. 

Instead, you need to move to the next TTL after you've received the three responses to the three probes you've sent.


## Handling Invalid Packets

These tests check that your code ignores invalid packets, without crashing.

Each of the tests in this section follows the same structure:

<img src="/assets/projects/proj1/single-path.svg" width="600px" alt="Topology: Your Computer to 2.2.2.2 to 3.3.3.3 to 4.4.4.4 (Destination)">

First run: 3.3.3.3 generates the invalid packet to the first two probes it receives.
- Input destination: `"4.4.4.4"`
- Expected output: `[["2.2.2.2"], ["3.3.3.3"], ["4.4.4.4"]]`

Second run: 3.3.3.3 generates the invalid packet to all three probes it receives.
- Input destination: `"4.4.4.4"`
- Expected output: `[["2.2.2.2"], [], ["4.4.4.4"]]`


### Test B2: Invalid ICMP Type

*Estimated difficulty: Easy*

If you receive an ICMP response packet whose error type is not "time exceeded" or "destination unreachable," you should ignore the packet.

This could happen if, for example, your computer ran the `ping` utility (which uses a different ICMP error type) before running traceroute, and a badly delayed packet from `ping` arrives during your traceroute. Whatever the reason, these responses should be ignored.


### Test B3: Invalid ICMP Code

*Estimated difficulty: Easy*

If you receive an ICMP response packet where the error type is "time exceeded," but the error code is not "TTL exceeded in transit," you should ignore the packet.


### Test B4: Invalid IP Protocol

*Estimated difficulty: Easy*

If you receive an ICMP response, but the IP protocol header field is something that's not ICMP, you should ignore the packet.

This could happen if, for example, the network corrupts the packet, or there was a bug in your operating system.


### Test B5: Unparseable Response

*Estimated difficulty: Easy*

If you receive an IPv4 packet with garbage as the IPv4 payload, you should ignore the packet.


### Test B6: Truncated Buffer

*Estimated difficulty: Easy*

If you receive an IPv4 packet that's too short (later bytes are missing), your code should detect the problem and ignore the packets without crashing.


### Test B7: Irrelevant UDP Response

*Estimated difficulty: Medium*

If you receive a UDP packet from a router, instead of an ICMP "time exceeded" packet, you should ignore the packet.


### Test B8: IP Options

*Estimated difficulty: Medium*

If you receive an IP packet with a non-empty Options field (length of the header is now >20 bytes), your code should ignore the options, but otherwise process the packet as normal.


## Packet Loss and Missing Packets

These tests check that your code handles packets getting dropped in the network, or hosts/routers refusing to send a reply, or packets getting lost in the network.


### Test B9: Router Loops

*Estimated difficulty: Easy*

If routers are buggy, the path to the destination might include a loop. No matter how high you set the TTL, packets to the destination will always get trapped in this loop until their TTL expires.

This test simulates a topology with a routing loop. Your code should return the routers in the loop over and over again, until you reach the maximum TTL and return (without ever seeing the destination).

<img src="/assets/projects/proj1/routing-loop.svg" width="750px" alt="Topology: Your Computer to 1.1.1.1 to 2.2.2.2 to 3.3.3.3 to 4.4.4.4 to 3.3.3.3, creating a loop between 3.3.3.3. Destination (5.5.5.5) never reachable.">

Input destination: `"5.5.5.5"`

Expected output: `[["2.2.2.2"], ["3.3.3.3"], ["4.4.4.4"], ["3.3.3.3"], ["4.4.4.4"], ... ]`

`["3.3.3.3"]` and `["4.4.4.4"]` should repeat until we hit the maximum TTL of 30.


### Test B10: Missing Host

*Estimated difficulty: Medium*

Sometimes, the destination IP won't answer your traceroute probe. This could be because:
- The destination IP doesn't exist.
- The destination machine is configured to not reply to traceroute probes.
- Along the path to the destination, there's a network firewall that drops your traceroute probe packet.

This test simulates a topology where the destination doesn't exist. Your code should keep sending probes until you reach the maximum TTL and return (without ever seeing the destination).

<img src="/assets/projects/proj1/single-path.svg" width="600px" alt="Topology: Your Computer to 2.2.2.2 to 3.3.3.3 to 4.4.4.4 (Destination)">

Input destination: `"5.5.5.5"` (doesn't exist)

Expected output: `[["2.2.2.2"], ["3.3.3.3"], ["4.4.4.4"], [], [], [], ... ]`

Any traceroute probe that reaches 4.4.4.4 gets dropped, so the return value should have empty lists for all TTLs from 4 to 30.


### Test B11: Silent Routers

*Estimated difficulty: Medium*

Some routers on the Internet won't respond to traceroute probes at all, for various reasons.

This test simulates a router that refuses to respond to traceroute probes.

<img src="/assets/projects/proj1/single-path.svg" width="600px" alt="Topology: Your Computer to 2.2.2.2 to 3.3.3.3 to 4.4.4.4 (Destination)">

Input destination: `"4.4.4.4"`

Expected output: `[["2.2.2.2"], [], ["4.4.4.4"]]`

Router 3.3.3.3 stays silent the entire time, and does not respond to your traceroute probes.


### Test B12: Occasional Drops

*Estimated difficulty: Easy (after finishing the previous test)*

Sometimes, packets on the Internet get dropped.

This test simulates packets getting dropped. From your code's perspective, it's basically the same as if the router refused to respond to ICMP probes.

<img src="/assets/projects/proj1/single-path.svg" width="600px" alt="Topology: Your Computer to 2.2.2.2 to 3.3.3.3 to 4.4.4.4 (Destination)">

First run: The first two probes with TTL 2 (aimed at 3.3.3.3) get dropped before reaching 3.3.3.3.
- Input destination: `"4.4.4.4"`
- Expected output: `[["2.2.2.2"], ["3.3.3.3"], ["4.4.4.4"]]`

Second run: All probes with TTL 2 (aimed at 3.3.3.3) get dropped before reaching 3.3.3.3.
- Input destination: `"4.4.4.4"`
- Expected output: `[["2.2.2.2"], [], ["4.4.4.4"]]`


## Duplicate Packets

These tests check that your code correctly ignores duplicate packets, and responses to duplicate packets.

Each of the tests in this section follows the same structure:

<img src="/assets/projects/proj1/single-path.svg" width="600px" alt="Topology: Your Computer to 2.2.2.2 to 3.3.3.3 to 4.4.4.4 (Destination)">

Input destination: `"4.4.4.4"`

Expected output: `[["2.2.2.2"], ["3.3.3.3"], ["4.4.4.4"]]`

Any duplicate packets, or responses to duplicate packets, should be ignored.


### Test B13: Duplicate Responses

*Estimated difficulty: Hard*

Sometimes, you'll send one packet, but the response will get duplicated. You should ignore the duplicate response.

In this test, when you send a probe with TTL 2 (aimed at 3.3.3.3), the reply from 3.3.3.3 gets duplicated in transit.


### Test B14: Duplicate Probes

*Estimated difficulty: Hard*

Sometimes, you'll send one packet, and the packet you send gets duplicated. This causes the router to receive two copies of your packet, and reply twice. You should ignore the second reply.

In this test, when you send a probe with TTL 2 (aimed at 3.3.3.3), the probe gets duplicated. This causes 3.3.3.3 to generate multiple ICMP responses.


### Test B15: Delayed Duplicates

*Estimated difficulty: Hard*

Sometimes, packets (probes or responses) can be duplicated *and* badly delayed.

As a result, when sending a probe aimed at 3.3.3.3, you may get a duplicate response from a previous probe aimed at 2.2.2.2.

Or, when sending a probe aimed 2.2.2.2, that probe might be duplicated. Later, when you send a probe aimed at 3.3.3.3, you might get a response from the duplicate probe aimed at 2.2.2.2.

In this test, probes and responses may be duplicated and badly-delayed in transit. This may cause the response probe from any particular router to be preceded by duplicate responses from previous routers, or responses to duplicate probes to previous routers.


## Response from Wrong Traceroute

### Test B16: Irrelevant TTL Response

*Estimated difficulty: Hard*

If you receive a valid ICMP time exceeded packet, but the packet was generated in response to a probe to a different destination that your code did not send, you should ignore the packet.

This could happen if, for example, a packet from a previous run of traceroute for a different destination is badly-delayed and picked up by this run of traceroute instead.

<img src="/assets/projects/proj1/single-path.svg" width="600px" alt="Topology: Your Computer to 2.2.2.2 to 3.3.3.3 to 4.4.4.4 (Destination)">

First run: 3.3.3.3 generates the invalid packet to the first two probes it receives.
- Input destination: `"4.4.4.4"`
- Expected output: `[["2.2.2.2"], ["3.3.3.3"], ["4.4.4.4"]]`

Second run: 3.3.3.3 generates the invalid packet to all three probes it receives.
- Input destination: `"4.4.4.4"`
- Expected output: `[["2.2.2.2"], [], ["4.4.4.4"]]`


### Tips

First off, the duplicate packet and wrong traceroute tests are the hardest tests in the project. If you have other tests you aren't passing yet, I would highly recommend focusing on those first.

At a conceptual level, what you need to do here is fairly simple. When working at TTL=x, if you receive an ICMP Time Exceeded packet, you need to know if it was generated in response to a probe you sent at TTL=x.

Figuring out how to do this requires a significant conceptual leap which, if you're stuck, you probably haven't had yet. This is not something where you can just tweak a for loop, or add a try-catch, or move an if statement and stumble onto the solution.

The solution doesn't require huge amounts of code, but it does require a solid understanding of the underlying concepts as well as some additional insights.

Of course, this leads to the question, how do we have this conceptual leap?

Some activities that may help you get unstuck include:

- Re-read the spec carefully. There are details and hints that may point you in the right direction.
- Re-read all comments provided in the starter code carefully. There are hints embedded in there that may help.
- Make sure you fully understand what's included in the ICMP response packets.
- Check out some of the resources linked in the traceroute guide. 
- Check out some of the professional traceroute implementations.

If you work through those suggestions and pair that effort with a significant dose of careful thought, you greatly increase your likelihood of figuring it out. Unfortunately, this process will require you to slow down, do some significant research, and think. There's not really a shortcut here.

**Added Sep 19, 2024**: If you're having trouble with detecting duplicate responses, here are some common bugs that we've noticed.

- Make sure that you don't leave duplicates waiting in the receive queue. Here's an example:

    If you send 3 packets, and they're all duplicated, you get 6 responses back. These 6 responses sit in the receive queue, waiting for you to process them.

    If you only process 3 of the 6 responses before moving on to the next TTL, then at the next TTL, you end up processing the remaining 3 responses.

    This often causes your output to have an extra entry at the duplicated TTL. For example, if we expect:

    `[[2.2.2.2], [3.3.3.3], [4.4.4.4]]`

    This bug may cause you to instead output something like this:

    `[[2.2.2.2], [3.3.3.3], [3.3.3.3], [4.4.4.4]]` 

    `[[2.2.2.2], [3.3.3.3], [], [4.4.4.4]]`

- Remember that timeouts are okay if the network has errors (e.g. duplicated, dropped, or corrupted packets). Test B1 is specifically checking that your code does not time out when the network has no errors (i.e. you receive one response per probe).

    In other words, at a given TTL, if you receive 3 unique responses to your 3 probes, you can move on to the next TTL without receiving any further packets (which would likely time out).

    However, if you receive a duplicate response, you can continue receiving responses to help drain the queue before moving on to the next TTL.

There are many different ways to solve these bugs, depending on how you designed the rest of your code. As mentioned above, there usually isn't a one-line tweak that you can do to fix these bugs, and you may need to do some deep thinking and redesigning to deal with these bugs.


## Submission and Grading

Congratulations! Your traceroute is now robust to all sorts of weird network errors.

To submit Project 1B, submit only the `traceroute.py` file to the [Project 1B autograder on Gradescope]({{ site.data.projects.projects[1].gradescope}}).

As long as you followed the academic integrity policies, the score you see on Gradescope is your final score for Project 1B (no hidden tests).