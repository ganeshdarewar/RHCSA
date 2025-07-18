# Network Configuration Theory and Notes — Detailed Summary

---

## 1. Objectives

- **Objective 1:** Understand networking theory before configuration.
- **Objective 2:** Learn how to configure a network between two or more machines.

---

## 2. IP Address Assignment

- Machines can have IPs set **manually** or **automatically** via DHCP.
- Example:
  - Machine-1: `192.168.0.10/24`
  - Machine-2: `192.168.0.20/24`
- **Ping** can test connectivity between IPs.

---

## 3. Hostname Configuration

- Hostnames help identify machines; recommended for clarity.
- On RHEL 7/8/9: Use `hostnamectl` or edit `/etc/hostname`.
- Hostname rules: Use FQDN (e.g., `node1.nokia.com`).
- Hostname mapping can be set in `/etc/hosts` for local resolution.

---

## 4. Network Testing

- **IP to IP:** `ping <IP>` works by default.
- **Hostname to Hostname:** Needs `/etc/hosts` or DNS server.
- Without DNS, add entries to `/etc/hosts` for name resolution.

---

## 5. Network Configuration Methods

- **During OS Installation:** Network can be configured.
- **After Installation:** Use CLI, direct file editing, or GUI.

### IP Assignment Choices

- **DHCP:** Automatic IP from server.
- **Manual:** Static IP set by admin.

---

## 6. Network Commands

- `ping` for IPv4, `ping6` for IPv6.
- `ssh root@<IP or hostname>` for remote login.
- `ifconfig`, `ip address`, `nmcli` for interface management.

---

## 7. Configuring Hostname and IP

- Set hostname:
  ```bash
  hostnamectl set-hostname node10.example.com
  ```
- Set IP using `nmcli`:
  ```bash
  nmcli connection add con-name ens160 ifname ens160 type ethernet autoconnect yes ip4 192.168.0.10/24
  ```
- Edit `/etc/NetworkManager/system-connections/ens160.nmconnection` for manual changes.

---

## 8. Testing Connectivity

- **Ping by IP:** Works if network is configured.
- **Ping by Hostname:** Works if `/etc/hosts` or DNS is set up.
- Example `/etc/hosts` entry:
  ```
  192.168.0.10 node10.example.com node10 machine10
  192.168.0.20 node20.example.com node20 machine20
  ```

---

## 9. SSH Access

- Use `ssh root@<IP>` or `ssh root@<hostname>` for remote login.
- SSH service (`sshd`) must be running on the target machine.

---

## 10. NetworkManager and Interface Management

- Use `nmcli` to add, modify, delete, and activate connections.
- Restart interfaces after changes:
  - `nmcli connection down <name>`
  - `nmcli connection up <name>`
  - Or restart NetworkManager: `systemctl restart NetworkManager`

---

## 11. DHCP vs Static Configuration

- **DHCP:** Set method to `auto` in `.nmconnection` file.
- **Static:** Specify IP, gateway, DNS in `.nmconnection` file or via `nmcli`.

---

## 12. Useful Files and Commands

- `/etc/hostname` — System hostname.
- `/etc/hosts` — Local name resolution.
- `/etc/resolv.conf` — DNS server configuration.
- `/etc/NetworkManager/system-connections/` — Network profiles.
- `ifconfig`, `ip a`, `nmcli`, `hostnamectl` — CLI tools for network management.

---

## 13. Adding/Managing Multiple Interfaces

- Multiple interfaces can be managed via `nmcli` and configuration files.
- Each interface can have its own IP and settings.

---

## 14. Activating/Deactivating Interfaces

- Use `nmcli`, `systemctl restart NetworkManager`, or device-specific commands.
- Old commands (`ifup`, `ifdown`) are deprecated in newer RHEL versions.

---

## 15. Summary

- Network configuration involves setting IP, hostname, gateway, and DNS.
- Use `nmcli` and config files for flexible management.
- Test connectivity with `ping` and remote access with `ssh`.
- Proper hostname and `/etc/hosts` setup enable name-based communication.

---

**End of Notes**




Certainly! Let's break down this `nmcli` command and explain each term in detail:

---

### 🔧 Full Command:

```bash
nmcli connection add con-name ens160 ifname ens160 type ethernet autoconnect yes ip4 192.168.0.10/24
```

---

### 🧩 Explanation of Each Part:

| **Part**              | **Meaning**                                                                                                                                         |
| --------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `nmcli`               | This is the **NetworkManager Command Line Interface**. It's used to manage network connections in RHEL, CentOS, Fedora, etc.                        |
| `connection add`      | You're telling `nmcli` to **add a new connection profile**.                                                                                         |
| `con-name ens160`     | This sets the **name of the new connection profile** as `ens160`. <br>📝 *This is just a label for the connection, and can be anything.*            |
| `ifname ens160`       | This tells `nmcli` to **bind the connection to the interface** named `ens160`. <br>🧠 *This must match your actual NIC name (use `ip a` to check).* |
| `type ethernet`       | Specifies the **type of connection**, which is Ethernet (wired). <br>✅ *Other types include `wifi`, `bridge`, `vlan`, etc.*                         |
| `autoconnect yes`     | Enables the connection to **automatically activate on boot** or when the interface comes up.                                                        |
| `ip4 192.168.0.10/24` | Assigns a **static IPv4 address** to the interface: <br>- `192.168.0.10` = IP Address <br>- `/24` = Subnet mask (255.255.255.0)                     |

---

### 🔍 What This Command Does

* Creates a new **static Ethernet connection** profile named `ens160`.
* Binds it to the **network interface** `ens160`.
* Disables DHCP (since you’re specifying an IP manually).
* The system will try to **autoconnect** to this profile on boot or when the NIC is available.
* Assigns the static IP `192.168.0.10` with subnet `/24` (i.e., range: `192.168.0.1 – 192.168.0.254`).

---

### 🔺 Missing in the Command (Optional but Recommended)

You should also specify:

* **Gateway** (for internet or network routing)
* **DNS** (for domain name resolution)

Add them like this:

```bash
nmcli connection modify ens160 ipv4.gateway 192.168.0.1 ipv4.dns 8.8.8.8 ipv4.method manual
```

Then bring up the connection:

```bash
nmcli connection up ens160
```

---

Let me know if you'd like a version of this command that also includes gateway and DNS in one line.
