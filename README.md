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
The Kali VM uses a Host-Only Adapter for communication with the Windows VM.
The Host-Only network provides an isolated virtual network where the VMs can communicate with each other and the host without directly becoming part of the physical LAN.

#### 2. IP Configuration
##### Kali Linux
The network interfaces were checked using:
```text
ip addr
```
Kali uses two interfaces:
```text
eth0 → 192.168.56.103/24
eth1 → 10.0.3.15/24
```
The Host-Only interface (eth0) is used for communication with Windows.

##### Windows
Windows receives an address from the Host-Only network:
```text
192.168.56.104
```
Both machines are therefore located in:
```text
192.168.56.0/24
```

#### 3. Initial Connectivity Test
The first connectivity test was performed from Kali:
```text
ping -c 4 192.168.56.104
```
Initially, the ping failed.
At this point, instead of assuming that the network configuration was broken, the next step was to investigate each layer separately.

#### 4. Checking ARP / Neighbor Discovery
Kali's neighbor table was checked using:
```text
ip neigh
```
After communication with Windows, Kali learned the MAC address associated with:
```text
192.168.56.104
```
This provided evidence that Layer 2 communication was functioning.

The troubleshooting process therefore became:
```text
    IP configuration
            ↓
      ARP resolution
            ↓
    ICMP connectivity
            ↓
        Firewall
```

#### 5. Troubleshooting the Failed Ping
The important observation was that Windows could ping Kali, while Kali could not initially ping Windows.

This suggested that the problem was not simply a broken Host-Only network.

The Windows Firewall was then investigated.

Rather than disabling the firewall completely, a specific inbound ICMP rule was created for the lab:
```text
New-NetFirewallRule `
    -DisplayName "Lab - Allow ICMPv4 Echo" `
    -Protocol ICMPv4 `
    -IcmpType 8 `
    -Direction Inbound `
    -Action Allow
```
This allows ICMPv4 Echo Request packets while keeping the Windows Firewall enabled.

#### 6. Successful Connectivity
After allowing inbound ICMP Echo Requests, Kali was able to successfully ping Windows:
```text
ping -c 4 192.168.56.104
```
The result showed successful ICMP Echo Replies with no packet loss.
This confirmed that the underlying network connectivity was working and that the previous failure was related to firewall filtering.

