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

```
## Lab Environment

| Component | Role | Network |
|---|---|---|
| Kali Linux | Analysis / testing machine | Host-Only + NAT |
| Windows 11 | Target / endpoint | Host-Only |
| VirtualBox | Virtualization platform | Host-Only Network |

### Network

```text
Host-Only Network
192.168.56.0/24

        ┌──────────────────────┐
        │   VirtualBox         │
        │   Host-Only Network  │
        └──────────┬───────────┘
                   │
          ┌────────┴────────┐
          │                 │
       Kali Linux        Windows 11
     192.168.56.103    192.168.56.104
```
#### 1. VirtualBox Network Configuration
- The Kali VM uses a Host-Only Adapter for communication with the Windows VM.
- The Host-Only network provides an isolated virtual network where the VMs can communicate with each other and the host without directly becoming part of the physical LAN.

#### 2. IP Configuration
##### Kali Linux
- The network interfaces were checked using:
```text
ip addr
```
- Kali uses two interfaces:
```text
eth0 → 192.168.56.103/24
eth1 → 10.0.3.15/24 ```
-The Host-Only interface (eth0) is used for communication with Windows.
