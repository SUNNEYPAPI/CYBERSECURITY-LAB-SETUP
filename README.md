# CYBERSECURITY-LAB-SETUP
A hands on cybersecurity lab environment built for practical security and learning.

## Project Overview

This project covers setting up a virtual lab for cybersecurity and penetration-testing practice, using VirtualBox as the hypervisor and Kali Linux as the main security-testing machine.

The goal is to have a safe, isolated space where I can practice network scanning, reconnaissance, vulnerability assessment, and other security exercises without any risk to my real network or system.

The lab runs on its own private virtual network, so I can add more virtual machines later and use them as practice targets for future exercises.

## Objectives

- Install and configure VirtualBox
- Install Kali Linux as a virtual machine
- Set up a private network for the lab
- Configure Kali's network settings
- Give the Kali VM a fixed IP address
- Confirm network connectivity and DNS resolution work correctly
- Take a clean snapshot of the VM to fall back on
- Document the whole process
- Set the stage for future cybersecurity projects in this lab

## Purpose of the Lab

This lab gives me an isolated, controlled space to practice security skills, including:
- Network reconnaissance
- Port scanning
- Vulnerability assessment
- Packet analysis
- Web security testing

## Lab Configuration

| Component | Configuration |
|---|---|
| Host OS | Windows 11 |
| Host RAM | 16 GB |
| Processor | 13th Gen Intel(R) Core(TM) i7-1355U |
| Hypervisor | VirtualBox 7.2.16 |
| Security OS | Kali Linux 2026.2 |
| Kali RAM | 8192 MB |
| Virtual Network | NAT Network |
| Network Address | 10.0.0.0/24 |
| Kali IP Address | 10.0.0.2/24 |
| Default Gateway | 10.0.0.1 |
| DNS Server | 8.8.8.8 |
| Future VM Range | 10.0.0.3–10.0.0.99 |

## Lab Setup Steps

### Step 1: Install 7-Zip
Needed to extract the Kali Linux VM package, since it's often distributed as a `.7z` archive.

### Step 2: Install VirtualBox
Installed VirtualBox to act as the hypervisor for the lab.

### Step 3: Create a NAT Network
Set up a dedicated NAT Network inside VirtualBox rather than plain NAT, since a NAT Network lets multiple VMs on it talk to each other while still reaching the internet. This means future attacker and target machines in the lab will be able to communicate with one another.

Configuration used:
- Network Name: natnetwork
- IPv4 Prefix: 10.0.0.0/24
- DHCP: Enabled
- IPv6: Disabled

### Step 4: Import Kali Linux
Downloaded the Kali Linux VM from the official Kali website and imported it into VirtualBox. Set the network adapter to attach to the NAT Network created above, and allocated RAM to the VM.

Also set up a shared folder so files can move easily between the host machine and Kali.

### Step 5: Configure Kali's Network
Checked and set a fixed IPv4 address on Kali instead of relying on DHCP, so the IP stays predictable for documentation and for referencing the machine in future exercises.

Example configuration:
- IP Address: 10.0.0.2
- Subnet Mask: 255.255.255.0
- Gateway: 10.0.0.1
- DNS: 8.8.8.8

### Step 6: Take a Clean Snapshot
Once everything was working, took a VirtualBox snapshot as a clean baseline (my lab). If a future exercise breaks something, I can roll back to this point instead of rebuilding from scratch.

## Verification

| Test | Command | Expected Result |
|---|---|---|
| Check IP address | `ip a` | Correct Kali IP shown |
| Test gateway | `ping 10.0.0.1` | Successful replies |
| Test internet connectivity | `ping 8.8.8.8` | Successful replies |
| Test DNS resolution | `ping google.com` | Successful replies |

## Problems Encountered & Fixes

**Problem: eth0 showed as connected, but had no IPv4 address**

After completing setup, `ip a` showed the `eth0` interface up (`UP`, `LOWER_UP`), but it only had a link-local IPv6 address — no IPv4 address was assigned, so the VM had no real network access.

Checked the connection with:
```
nmcli connection show
```
This confirmed the "Wired connection 1" profile existed but wasn't properly bound/active on `eth0`. Trying to bring it up directly returned:
```
Error: Connection activation failed: IP configuration could not be reserved (no available address, timeout, etc.)
```

**Fix that worked:**
```
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```
After running these, the connection activated successfully and `ip a` showed a proper `inet` address on `eth0` (10.0.0.2). Verified with:
```
ping 10.0.0.1
ping 8.8.8.8
```
Both returned successful replies, confirming the gateway and internet connectivity were working.

**Follow-up:** Since this issue could recur (e.g. after a VM restart), a VirtualBox snapshot named "network config" was taken immediately once the network was confirmed stable — this gives a safe rollback point instead of repeating the troubleshooting from scratch. The connection was also set to auto-connect:
```
sudo nmcli connection modify "Wired connection 1" connection.autoconnect yes
```

> **Note:** Network interface and connection names may differ between systems — always confirm your actual connection name with `nmcli connection show` before running any `nmcli` commands.

## What I Learned

- **NAT vs NAT Network** — the difference and why NAT Network is better for a lab where VMs need to talk to each other
- **Static IP configuration** — how and why to give a lab VM a fixed IP
- **Snapshots** — the value of taking a clean snapshot before doing anything risky, so there's always a safe point to roll back to
- **Documentation** — writing down commands, configs, problems, and fixes is a core habit in real security work, not just an afterthought

## Security & Ethical Use

This lab is strictly for educational purposes and authorized practice only.

## Tools & Resources

- 7-Zip: https://7-zip.org/download.html
- VirtualBox: https://virtualbox.org/wiki/Downloads
- Kali Linux: https://kali.org/get-kali

## Author

**Aimiyu Sunday Azeez**
Cybersecurity Intern B083

## Project Information

**Project:** Cybersecurity & Pentesting Lab Setup
**Repository:** GitHub
