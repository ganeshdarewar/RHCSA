# Detailed Notes: Networking Concept Theory Session

---

## 1. Introduction to Networking

- **Networking** is the process that allows two or more machines (computers, servers, devices) to communicate and share resources.
- Before configuring a network, it is essential to understand:
  - **Network devices** (hardware and interfaces)
  - **IP Address concepts** (how devices are identified and communicate)

---

## 2. Basic Structure of a Computer System

- A machine consists of:
  - **Hardware (H/W):** Physical components (motherboard, NIC, etc.)
  - **Operating System (OS):** Manages hardware and provides services for applications.
  - **Applications:** Software that runs on the OS and uses network resources.

**Communication Flow:**
```
Hardware <== OS <== Applications
```
- Multiple machines (physical, virtual, or cloud-based) can be connected for communication.

---

## 3. Types of Machines

- **Physical Machines:** Desktop, laptop, rack-mounted server, tower machine.
- **Virtual Machines:** Software-based computers running on physical hardware.
- **Cloud-based Machines:** Hosted on platforms like AWS, Azure, Google Cloud.

---

## 4. Essential Networking Components

1. **Motherboard:** Hosts the NIC and other hardware.
2. **NIC (Network Interface Card):** Physical or virtual, provides network connectivity.
3. **RJ45 Connector:** Standard connector for Ethernet cables.
4. **Ethernet Cable:** Connects devices; direct connection supports up to two computers.
5. **Hub/Switch:** Devices to connect multiple machines in a network (switches are preferred in modern setups).

---

## 5. Communication Between Machines

- Machines require networking to communicate.
- Each machine runs an OS and applications that use the network to interact with other machines.

---

## 6. How Machines Communicate

- **MAC Address:** Unique hardware address assigned to each NIC.
- **IP Address:** Logical address assigned to each device for network communication.
  - **IPv4:** 32-bit address, written as four decimal blocks (e.g., 192.168.1.1).
  - **IPv6:** 128-bit address, written as eight hexadecimal blocks (e.g., 2001:0db8:85a3:0000:0000:8a2e:0370:7334).

---

## 7. IP Address Classes

- **Class A:** 0–126 (e.g., 10.0.0.1)
- **Class B:** 128–191 (e.g., 172.16.0.1)
- **Class C:** 192–223 (e.g., 192.168.0.1)
- **Class D:** 224–239 (Multicast)
- **Class E:** 240–255 (Experimental)
- **Loopback Address:** 127.0.0.1 (local testing)

---

## 8. Private vs Public IP Addresses

- **Private IP Ranges (used in LANs, not routable on the internet):**
  - Class A: 10.0.0.0 – 10.255.255.255
  - Class B: 172.16.0.0 – 172.31.255.255
  - Class C: 192.168.0.0 – 192.168.255.255
- **Public IP Ranges:** All other addresses, routable on the internet, require purchase.

---

## 9. Subnet Mask

- Defines which part of an IP address is the network and which is the host.
- **Default Subnet Masks:**
  - Class A: 255.0.0.0 (/8)
  - Class B: 255.255.0.0 (/16)
  - Class C: 255.255.255.0 (/24)
- **CIDR Notation:** e.g., 192.168.0.1/24

---

## 10. Valid IP Address Format

- **IPv4:** Four blocks, each 0–255 (e.g., 192.168.1.1)
- **IPv6:** Eight blocks, hexadecimal, each 16 bits (e.g., 2001:db8::1)
- Invalid examples: 192.168.1.5000, 8000.9000.6000.20000

---

## 11. Network Addressing

- **Network Address:** First address in a subnet (e.g., 192.168.0.0)
- **Broadcast Address:** Last address in a subnet (e.g., 192.168.0.255)
- **Host IDs:** Addresses between network and broadcast, assigned to devices.

---

## 12. Number of Hosts per Network

- **Class A:** 16,777,214 hosts
- **Class B:** 65,534 hosts
- **Class C:** 254 hosts

---

## 13. Testing Network Connectivity

- Use the `ping` command to check if two machines can communicate.
- Requirements for successful ping:
  1. Same IP class
  2. Same network ID
- **Router:** Needed for communication between different networks/classes.

---

## 14. Networking Devices

- **NIC/Ethernet/LAN Card:** Provides MAC and IP addresses.
- **Ethernet Cable, RJ45, Cross Cable:** For physical connections.
- **Hub:** Outdated, not recommended.
- **Switch:** Preferred for modern networks.
- **Router:** Connects different networks.
- **Firewall:** Controls incoming/outgoing traffic (hardware or software).

---

## 15. IP Assignment Methods

- **Manual Configuration:** Set by system administrator.
- **Automatic (DHCP):** Dynamic Host Configuration Protocol assigns IPs from a pool.
  - **DORA Process:** Discover, Offer, Request, Acknowledge.
  - **MAC Reservation:** Assigns fixed IP to a specific MAC address.

---

## 16. Gateway and DNS

- **Gateway/Router:** Device that routes traffic between networks.
- **DNS (Domain Name System):** Resolves hostnames to IP addresses.
  - **/etc/hosts:** Local name resolution.
  - **/etc/resolv.conf:** DNS server configuration.
- DNS is optional for IP-to-IP communication but required for name-based communication.

---

## 17. Basic Networking Commands

- **Linux:**
  - `ifconfig`, `ip address`, `ip a`, `ip addr show` — Show network interfaces and IPs.
- **Windows:**
  - `ipconfig` — Show network interfaces and IPs.

---

## 18. Summary

- Networking enables communication between machines.
- Understanding hardware, IP addressing, subnetting, and network devices is crucial.
- Proper configuration and testing ensure reliable connectivity.
- Routers and firewalls manage and secure network traffic.
- DHCP and DNS simplify IP management and name resolution.

---

**End of Detailed Notes**