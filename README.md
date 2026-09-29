# Basic Network Lab — Kali ↔ Windows

A small VirtualBox networking lab built to understand how
communication works from Layer 2 to Layer 3.

## Objective

- Understand VirtualBox Host-Only networking
- Configure Kali and Windows on the same subnet
- Understand ARP resolution
- Analyze ICMP traffic
- Troubleshoot connectivity using network evidence
- Observe Ethernet, IPv4, and ICMP using tcpdump

## Lab Topology

```text
                    Internet
                       │
                   NAT / eth1
                       │
                      Kali
                 192.168.56.103
                       │
                Host-Only Network
                192.168.56.0/24
                       │
                    Windows
                 192.168.56.104
