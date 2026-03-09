# Smurf Attack - Network Security Lab

![Status](https://img.shields.io/badge/Status-Completed-brightgreen)
![Category](https://img.shields.io/badge/Category-DoS%20%2F%20ICMP%20Amplification-red)
![Tool](https://img.shields.io/badge/Tool-hping3-blue)
![Platform](https://img.shields.io/badge/Platform-Kali%20Linux-purple)
![Lab](https://img.shields.io/badge/Lab-Network%20Security-orange)

**By Anas TAGUI**

A hands-on lab demonstrating a Smurf Attack (ICMP Amplification / Denial of Service) in a controlled virtual environment.

---

## Overview

| Field | Details |
|---|---|
| Category | Denial of Service (DoS) / ICMP Amplification |
| Attacker | Kali Linux |
| Victim | Windows Server with IIS |
| Amplifiers | 4x Knoppix 4.0 Linux VMs |
| Tool | hping3 |
| Protocol | ICMP |

---

## What is a Smurf Attack?

A Smurf Attack exploits ICMP combined with IP broadcast addressing. The attacker sends ICMP ECHO REQUEST packets to a network's broadcast address while spoofing the victim's IP as the source. Every host on the network that responds to broadcast pings replies directly to the victim, creating a traffic amplification effect.

---

## Lab Environment

- All VMs connected via bridged adapter on subnet `192.168.0.0/24`
- **Kali Linux** (`192.168.0.51`) - Attacker
- **Windows Server IIS** (`192.168.0.50`) - Victim
- **4x Knoppix 4.0** (`192.168.0.101-104`) - Amplifiers

---

## Attack Command

```bash
hping3 --a 192.168.0.50 --i u10 -1 --d 65000 192.168.0.255
```

| Parameter | Description |
|---|---|
| `--a 192.168.0.50` | Spoofs source IP as the victim's address |
| `--i u10` | Sends one packet every 10 microseconds |
| `-1` | ICMP mode |
| `--d 65000` | Packet size of 65,000 bytes (max ICMP) |
| `192.168.0.255` | Broadcast address of the subnet |

---

## Results

### With 10 Mbps bandwidth cap
- IIS web service completely unreachable
- Network interface fully saturated
- CPU usage spiked from ICMP flood processing
- Victim effectively offline

### With unlimited bandwidth
- IIS web service remained accessible
- Network interface no longer bottlenecked
- CPU elevated but server functional
- Attack had minimal impact

**Key takeaway:** the attack's effectiveness is directly tied to the victim's available bandwidth.

---

## Countermeasures

**1. Disable broadcast responses on hosts**
```bash
echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_broadcasts
```

**2. Block directed broadcasts at the router (Cisco)**
```bash
interface FastEthernet0/0
no ip directed-broadcast
```

**3. Ingress/Egress filtering (BCP38) against IP spoofing**
```bash
# Block packets with source IP not belonging to your subnet
# Apply at network edge via firewall ACL or router policy
```

Any one of these countermeasures breaks the attack. All three together make it essentially impossible to execute.

---

## Files

- `LAB_4.pdf` - Full lab report with screenshots and analysis

---

## Disclaimer

This lab was performed in a fully isolated virtual environment for educational purposes only. Do not replicate outside of a controlled lab setting.
