# 🔐 Cybersecurity Lab Environment Setup

**Building an isolated virtual lab for penetration testing and ethical
hacking practice**

## 📌 Project Overview

This project documents the setup of a virtual cybersecurity and
penetration-testing laboratory using Oracle VirtualBox and Kali Linux.

The lab provides a controlled environment for practicing network
scanning, reconnaissance, vulnerability assessment, packet analysis, web
security testing, and other security-testing activities. Additional
target virtual machines can be added for authorized testing.

## 🎯 Objectives

-   Install and configure VirtualBox.
-   Import Kali Linux as a virtual machine.
-   Create a private NAT Network for the lab.
-   Configure Kali Linux network connectivity.
-   Assign a consistent IP address to the Kali VM.
-   Verify network connectivity and DNS resolution.
-   Create a clean VM snapshot for recovery.
-   Document the setup and troubleshooting process.
-   Prepare the environment for future cybersecurity projects.

## 🛡️ Purpose and Ethical Use

This lab is intended for cybersecurity learning and authorized security
testing only.

Possible learning activities include:

-   Network reconnaissance
-   Port scanning
-   Vulnerability assessment
-   Packet analysis
-   Web security testing
-   Exploitation practice in a controlled lab
-   Security-tool experimentation

> **Important:** Only test systems you own or have explicit permission
> to assess. Do not use this lab or its tools to attack unauthorized
> systems.

## 🏗️ Lab Architecture

``` text
                 ┌──────────────────────────┐
                 │  Host: Windows           │
                 │  Oracle VirtualBox       │
                 └────────────┬─────────────┘
                              │
                 ┌────────────┴─────────────┐
                 │  NatNetwork              │
                 │  Network: 10.0.0.0/24    │
                 │  Gateway: 10.0.0.1       │
                 └────────────┬─────────────┘
                              │
          ┌───────────────────┼───────────────────┐
   ┌──────┴───────┐    ┌──────┴───────┐    ┌──────┴───────┐
   │ Kali Linux   │    │ Target VM    │    │ Target VM    │
   │ 10.0.0.2     │    │ (future)     │    │ (future)     │
   └──────────────┘    └──────────────┘    └──────────────┘
```

Additional target machines can be connected to the same NAT Network for
future lab exercises.

## ⚙️ Lab Configuration

  Component                 Configuration
  ------------------------- -------------------------------------------
  Host OS                   Windows (confirm exact version)
  Host RAM                  8 GB (as specified for this lab)
  Processor                 Intel Core i7 (as specified for this lab)
  Hypervisor                Oracle VirtualBox 7.2
  Security OS               Kali Linux (confirm installed release)
  Kali RAM                  2048 MB
  Virtual Network           NAT Network
  Network Address           `10.0.0.0/24`
  Kali IP Address           `10.0.0.2/24`
  Default Gateway           `10.0.0.1`
  DNS Server                `8.8.8.8`
  Planned Future VM Range   `10.0.0.3`--`10.0.0.99`

**Configuration note:** The screenshots provided show a Kali Linux
2025.4 VM with 2048 MB RAM and four processors. The Network Settings
screenshot shows **NAT**, not **NAT Network**. Update these values after
configuring and verifying the actual VM so the README matches the final
setup. Ensure `10.0.0.2` is reserved or excluded from DHCP before
assigning it statically to avoid an IP address conflict.

## 🪜 Lab Setup Procedure

### Step 1: Install 7-Zip

Install 7-Zip to extract the Kali Linux virtual-machine archive if it is
distributed as a `.7z` file.

### Step 2: Install VirtualBox

Install Oracle VirtualBox on the Windows host. Hardware virtualization
(Intel VT-x or the equivalent setting) must be enabled in BIOS/UEFI.

### Step 3: Create the NAT Network

In VirtualBox's network settings, create a dedicated NAT Network with
these planned settings:

  Setting        Value
  -------------- ---------------------------------------------------------------
  Network Name   `NatNetwork`
  IPv4 Prefix    `10.0.0.0/24`
  DHCP           Enabled, unless using a carefully managed static-address plan
  IPv6           Disabled, if not required

A NAT Network allows attached VMs to communicate with one another and
generally provides outbound internet access. This differs from the
standard **NAT** mode, which typically isolates each VM from other VMs
by default.

### Step 4: Import Kali Linux

Download the Kali Linux VirtualBox image from the official Kali website
and import it into VirtualBox.

Suggested VM settings:

-   **Network Adapter:** Adapter 1 enabled
-   **Attached to:** NAT Network
-   **Network:** `NatNetwork`
-   **Adapter Type:** Intel PRO/1000 MT Desktop
-   **Memory:** 2048 MB

A VirtualBox shared folder can optionally be configured to transfer
files between the Windows host and Kali Linux. Install the appropriate
Guest Additions and configure folder permissions if required.

### Step 5: Configure the Kali Linux Network

Planned IPv4 settings:

-   **IP Address:** `10.0.0.2`
-   **Subnet Mask:** `255.255.255.0`
-   **Gateway:** `10.0.0.1`
-   **DNS:** `8.8.8.8`

Use a static address only after confirming that it belongs to the NAT
Network's subnet and does not conflict with its DHCP pool or another VM.
Alternatively, create a DHCP reservation if supported by the chosen
setup.

To inspect current network interfaces and connection profiles:

``` bash
ip addr
ip route
nmcli connection show
```

### Step 6: Create a Clean VM Snapshot

After the initial setup and successful verification, create a snapshot
named:

`Clean Kali - Network Setup`

This snapshot provides a known-good recovery point before later
experiments.

## 🔎 Lab Verification

Run these commands in Kali Linux:

  -----------------------------------------------------------------------------
  Test                    Command                       Expected Result
  ----------------------- ----------------------------- -----------------------
  Check IP address        `ip addr`                     The expected Kali IP is
                                                        displayed

  Check routing           `ip route`                    The default route uses
                                                        the intended gateway

  Test gateway            `ping -c 4 10.0.0.1`          Replies, if the gateway
                                                        responds to ICMP

  Test internet           `ping -c 4 8.8.8.8`           Replies, if ICMP is
  connectivity                                          permitted

  Test DNS resolution     `nslookup networkwalks.com`   The domain resolves

  Verify Nmap             `nmap --version`              The installed Nmap
                                                        version is displayed
  -----------------------------------------------------------------------------

Example values, **if the planned configuration has been applied**:

``` text
IP address: 10.0.0.2/24
Gateway:    10.0.0.1
DNS:        8.8.8.8
```

Actual results may differ. A failed ping alone does not always mean
internet access is unavailable, because ICMP traffic can be blocked.

## 🐞 Problems Encountered and Solutions

### Problem 1: Internet connectivity after static IP configuration

Manually configuring IPv4 settings can interrupt connectivity if the IP
address, gateway, DNS, or NetworkManager profile is incorrect.

First inspect the connection profile:

``` bash
nmcli connection show
ip addr
ip route
```

Then check that the static IP, prefix, gateway, and DNS match the actual
VirtualBox NAT Network. Restart the correct NetworkManager connection
only after confirming its name.

**Troubleshooting note:** The command below was suggested during
troubleshooting, but it is not a general-purpose fix for internet
connectivity:

``` bash
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
```

The connection name may differ, and this setting does not replace
correct IP, gateway, and DNS configuration.

### Problem 2: VirtualBox VT-x / virtualization error

If the VM cannot start because hardware virtualization is disabled:

1.  Restart the computer.
2.  Open BIOS/UEFI settings.
3.  Enable Intel VT-x or the equivalent hardware-virtualization option.
4.  Save the changes and restart.
5.  Start the Kali VM again.

The exact menu name depends on the computer manufacturer.

## 💡 What I Learned

-   **NAT vs NAT Network:** Standard NAT generally provides outbound
    access to an individual VM. NAT Network allows VMs attached to the
    same virtual network to communicate with each other and access
    outside networks.
-   **Virtual machine networking:** Network adapter modes affect
    connectivity between the host, guest VMs, and external networks.
-   **Static IP configuration:** Correct IP address, subnet mask,
    gateway, and DNS settings are important for predictable lab
    networking.
-   **VM snapshots:** A clean snapshot provides a recovery point before
    risky or experimental work.
-   **Documentation:** Recording configuration, commands, screenshots,
    issues, and solutions is an important cybersecurity practice.

## 🔗 Tools and Resources

-   [7-Zip](https://www.7-zip.org/)
-   [Oracle VirtualBox](https://www.virtualbox.org/)
-   [Kali Linux](https://www.kali.org/get-kali/)

## 👤 Author

**Bhingradiya Het**

## 📌 Project Information

  Field        Details
  ------------ --------------------------------------
  Program      Cybersecurity at Networkwalks
  Week         01
  Project      Cybersecurity & Pentesting Lab Setup
  Repository   GitHub

------------------------------------------------------------------------

**Security reminder:** Use this lab only for learning and authorized
testing. Before sharing the repository, confirm that all configuration
details and example results match the environment you actually built.
