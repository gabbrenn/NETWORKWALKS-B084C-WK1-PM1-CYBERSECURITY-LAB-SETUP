# Week 1 Project: Cybersecurity Lab Setup

**Program:** Cybersecurity — Networkwalks  
**Batch:** B084  
**Project:** Virtual Lab Setup for Cybersecurity and Ethical Hacking Practice  
**Tools:** Oracle VirtualBox, Kali Linux, Windows

## 1. Project Overview 📌

The goal of this project was to prepare a virtual lab for cybersecurity and ethical hacking practice. I used Oracle VirtualBox to run Kali Linux as a virtual machine on my Windows laptop. This setup allows me to practice security tools in a separate environment without replacing my main operating system.

## 2. Objectives 🎯

- Install and configure Oracle VirtualBox.
- Import and configure a Kali Linux virtual machine.
- Create a VirtualBox NAT Network.
- Configure a static IPv4 address in Kali Linux.
- Troubleshoot networking and verify internet connectivity.
- Document the challenges, solutions, and lessons learned.

## 3. Purpose of the Lab 🛡️
The lab provides an isolated and controlled environment for cybersecurity learning and authorized security testing.

It can be used for activities such as:

- Network reconnaissance
- Port scanning
- Vulnerability assessment
- Packet analysis
- Web security testing
- Exploitation practice
- Security-tool experimentation

## 4. Lab Environment ⚙️

| Component | Configuration |
|---|---|
| Host operating system | Windows |
| Virtualization software | Oracle VirtualBox |
| Guest operating system | Kali Linux |
| Laptop RAM | 12 GB |
| Virtual network mode | NAT Network |
| NAT Network IPv4 prefix | `10.0.0.0/24` |
| Kali IPv4 address | `10.0.0.2/24` |
| Default gateway | `10.0.0.1` |
| DNS server configured | `8.8.8.8` |
| Kali network interface | `eth0` |

> **Note:** These IP settings apply to the VirtualBox NAT Network used in this lab. They are not necessarily the addresses for a physical internship network. A NAT Network allows the VM to access external networks through the host, but it does not automatically make the VM directly accessible to devices on the physical Wi-Fi/LAN.

## 5. Work Completed 🪜

### 5.1 Installed and configured VirtualBox

I installed Oracle VirtualBox on Windows to create and manage virtual machines.

### 5.2 Imported Kali Linux

I imported a Kali Linux virtual machine into VirtualBox and checked its virtual hardware and network adapter settings.

### 5.3 Created a NAT Network

I configured a VirtualBox NAT Network with the IPv4 prefix:

```text
10.0.0.0/24
```

I attached the Kali VM's network adapter to this NAT Network. DHCP remained enabled in VirtualBox, while Kali was configured to use a static address. These settings can coexist, provided the static address does not conflict with another address in use.

### 5.4 Configured static IPv4 settings in Kali

In Kali's network connection editor, I selected **IPv4 → Manual** and entered:

| Setting | Value |
|---|---|
| Address | `10.0.0.2` |
| Netmask | `255.255.255.0` (`/24`) |
| Gateway | `10.0.0.1` |
| DNS | `8.8.8.8` |

## 6. Challenges and Troubleshooting 🐞

### Challenge 1: Network settings were saved, but Kali had no IPv4 address

Initially, `ip address show eth0` displayed the network interface as up, but only an IPv6 link-local address was shown. The expected IPv4 address, `10.0.0.2`, was missing.

The routing table also did not contain the connected route for `10.0.0.0/24`.

**Commands used to investigate:**

```bash
ip address show eth0
ip route
```

**Solution:**

I used NetworkManager (`nmcli`) to reactivate the saved wired connection:

```bash
sudo nmcli connection up "Wired connection 1"
```

After the connection was activated, Kali received the configured static IPv4 address and the expected routes appeared.

**What I learned:** Saving a network profile and activating it are different actions. NetworkManager may need to reactivate a connection before saved settings are applied to the network interface.

I also tried this command during troubleshooting:

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
```

This changes the IPv4 duplicate-address-detection timeout. It was not the command that assigned the static IP, and it may not be necessary for a normal static IPv4 setup.

### Challenge 2: A command failed because of a typing mistake

I tried to bring the connection down but accidentally included a trailing `~` in the connection name:

```bash
sudo nmcli connection down "Wired connection 1"~
```

NetworkManager reported that the connection name was not active. I corrected the command and activated the connection using the exact profile name.

**What I learned:** Small command-line typing mistakes can cause errors. I should carefully check connection names and command syntax when troubleshooting.

## 7. Connectivity Verification 🔎

After activating the connection, I ran these tests.

### Check the IPv4 address

```bash
ip -4 address show eth0
```

The output showed:

```text
inet 10.0.0.2/24 brd 10.0.0.255 scope global noprefixroute eth0
```

This confirmed that the static IPv4 address was applied.

### Check the routing table

```bash
ip route
```

The relevant output was:

```text
default via 10.0.0.1 dev eth0 proto static metric 100
10.0.0.0/24 dev eth0 proto kernel scope link src 10.0.0.2 metric 100
```

This confirmed the default gateway and the connected route for the local virtual network.

### Test the gateway

```bash
ping -c 4 10.0.0.1
```

**Result:** 4 packets transmitted, 4 received, 0% packet loss.

### Test external IPv4 connectivity

```bash
ping -c 4 8.8.8.8
```

**Result:** 4 packets transmitted, 4 received, 0% packet loss.

This confirmed that Kali could reach an external IPv4 address.

### Test DNS resolution

```bash
getent hosts google.com
```

The command returned an IPv6 address for `google.com`, confirming that the hostname resolved. Combined with the successful IPv4 ping, this showed that external IPv4 connectivity and name resolution were working at the time of testing.

## 8. What I Learned 💡

Through this project, I gained practical experience with:

- Installing and configuring virtualization software.
- Running Kali Linux in a virtual machine on Windows.
- Understanding VirtualBox NAT Network and how it differs from direct access to a physical LAN.
- Configuring a static IPv4 address, subnet mask, gateway, and DNS server.
- Understanding that VirtualBox DHCP can remain enabled even when Kali uses a static IP, provided there is no address conflict.
- Using `ip address` and `ip route` to inspect network configuration.
- Using `nmcli` to manage and reactivate NetworkManager connections.
- Testing gateway reachability, external IP connectivity, and DNS resolution.
- Troubleshooting methodically and verifying changes instead of assuming they worked.

## 9. Conclusion

I successfully set up a Kali Linux virtual lab using Oracle VirtualBox, configured a static IPv4 address on a VirtualBox NAT Network, resolved an issue where the saved network settings were not applied, and verified connectivity to the NAT gateway and an external IPv4 address.

This project gave me a practical foundation for future cybersecurity and ethical hacking labs. All security testing in future exercises will be limited to systems I own or have explicit permission to test.

## 10. Acknowledgment

Thank you to **Networkwalks** and **Waqas Karim (CCIE)** for the learning opportunity and guidance as part of the Cybersecurity program, Batch B084.

## 👤 Author
**Vladimir Brenn Gabiro**

Cyber Security Analyst B084C

LinkedIn: www.linkedin.com/in/vladimir-brenn-gabiro

## 📌 Project Information
Program Name: Cybersecurity at Networkwalks | Week: 01 | Project: Cybersecurity & Pentesting Lab Setup | Repository: GitHub
