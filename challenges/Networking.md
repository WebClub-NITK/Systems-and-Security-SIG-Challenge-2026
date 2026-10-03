# eBPF + Security: High-Performance XDP Firewall

**Tags:** `eBPF` `Linux` `Networking` `XDP` `Security` `Firewall`

## Introduction

eBPF (Extended Berkeley Packet Filter) allows you to run custom, sandboxed programs inside the Linux kernel.

By utilizing XDP (eXpress Data Path), you can process and drop network packets at the lowest possible level—often before the kernel even allocates memory for them.

This makes XDP a powerful tool for building **high-performance, low-latency firewalls**.

## Problem Statement

Build a **programmable, high-performance firewall using eBPF and XDP**.

You will start with a basic stateless packet dropper and independently research how to evolve it into a dynamic, rate-limiting security tool.

The goal is to explore the **kernel-to-user-space boundary** and understand **raw packet processing**.

---

## Level 1: Stateless Firewall

* Research how to attach an XDP program to a network interface and safely read packet headers.
* Write a kernel-space program to intercept incoming traffic and drop packets based on a hardcoded rule.
Example: Block all ICMP requests, block a specific protocol.
* Log the dropped packet details to the trace pipe for basic observability.

---

## Level 2: Dynamic Control Plane

* Hardcoding rules requires recompiling the kernel code, which isn't practical.

* Explore how to share data and state between **kernel space and user space**.

* Build a simple user-space command-line tool using **Go, Python, or C** that allows an administrator to:
  - Add blocked IP addresses dynamically.
  - Remove blocked IP addresses dynamically.
  - View currently blocked IP addresses.
  - Apply changes on the fly **without reloading the XDP program**.

---

## Level 3: Rate Limiting & DDoS Mitigation

* Enhance the firewall to detect and mitigate volumetric attacks such as a **TCP SYN flood**.
*  Figure out how to track packet rates and timestamps per source IP. If an IP exceeds a predefined threshold (e.g., > 100 packets per second), automatically drop subsequent packets from that source.
* The firewall should:
  - Track packet rates per source IP.
  - Track timestamps per source IP.
  - Define a packet-rate threshold.
  - Automatically drop subsequent packets from a source IP when the threshold is exceeded.

### Example
If an IP exceeds **100 packets per second**, automatically drop subsequent packets from that source.

---

## Level 4: Stateful Connection Tracking

* Stateless firewalls are easily bypassed. Research how to track the state of active network connections.

* Monitor outgoing connections and modify your firewall so that it only permits inbound traffic if it belongs to a locally initiated session. Drop all unsolicited inbound traffic. 
* Modify the firewall to:
  - Monitor outgoing connections.
  - Track the state of active network connections.
  - Permit inbound traffic only if it belongs to a **locally initiated session**.
  - Drop all unsolicited inbound traffic.

---

## Resources

* [eBPF](https://ebpf.io/)
* XDP Tutorial (GitHub)
* L4Drop — XDP DDoS Mitigations
* *Learning eBPF* by Liz Rice (Book)

---

## Submission

Create a **private GitHub repository** and add the mentors as collaborators.

---

## Mentor Details
1. Shanjiv - shanjiv.231cs155@nitk.edu.in
2. Nishant - nishant.231ee138@nitk.edu.in
