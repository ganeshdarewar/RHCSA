Here are detailed, explainable notes based on the contents of `01_firewalld notes.txt`:

---

## Firewall Management in Linux

Linux systems use firewalls to manage network security. There are three main methods:

1. **TCP Wrapper**
   - Uses `/etc/hosts.allow` and `/etc/hosts.deny` to control access to network services.
2. **IPTables**
   - Default firewall in RHEL 6 and earlier.
   - Managed using the `iptables` command.
3. **Firewalld**
   - Default firewall in RHEL 7, 8, and 9.
   - Managed via:
     - `firewall-cmd` (CLI)
     - `firewall-config` (GUI)
     - Cockpit (web-based management tool)

---

## Objectives

- **Objective 1:** Manage network security using firewalld (Layer 1: Network Firewall)
- **Objective 2:** Understand Linux file/folder security (Layer 2: DAC - Discretionary Access Control)
- **Objective 3:** Learn the role of SELinux (Layer 3: MAC - Mandatory Access Control)

---

## Firewalld Basics

- **Zones:** Define trust levels for network connections (e.g., public, home, work).
  - List zones: `firewall-cmd --get-zones`
  - Active zone: `firewall-cmd --get-active-zones`
- **Services:** Predefined rules for common services (e.g., ssh, http).
  - List services: `firewall-cmd --list-services --zone=public`
- **Ports:** Custom port rules.
  - List ports: `firewall-cmd --list-ports --zone=public`
- **All settings:** `firewall-cmd --list-all`

---

## Service and Port Management

- Services are defined in XML files under `/usr/lib/firewalld/services/` (e.g., `http.xml`, `https.xml`).
- Example: `http.xml` opens TCP port 80 for HTTP traffic.

---

## Firewalld Commands

- Check if firewalld is installed: `rpm -qa firewalld`
- Check if running: `systemctl is-active firewalld`
- Enable/disable firewalld: `systemctl enable|disable firewalld`
- Start/stop firewalld: `systemctl start|stop firewalld`
- Check state: `firewall-cmd --state`

---

## Example: Allowing HTTP/HTTPS

1. Install and start Apache (`httpd`).
2. Create a test web page in `/var/www/html/index.html`.
3. Add HTTP service to firewalld:
   - `firewall-cmd --permanent --add-service=http`
   - `firewall-cmd --reload`
4. Verify access from another machine using `curl`.

5. For HTTPS:
   - Install `mod_ssl`.
   - Add HTTPS service:
     - `firewall-cmd --permanent --add-service=https`
     - `firewall-cmd --reload`

---

## Opening Custom Ports

- Change Apache to listen on port 82 (edit `httpd.conf`).
- Allow port 82 in firewalld:
  - `firewall-cmd --permanent --add-port=82/tcp`
  - `firewall-cmd --reload`
- Update SELinux to allow Apache on port 82:
  - `semanage port -a -t http_port_t -p tcp 82`

---

## SELinux Overview

- SELinux provides fine-grained access control for processes and files.
- Uses security policies to determine access.
- Enforcing mode: SELinux actively blocks unauthorized access.
- Ports for services must be allowed in SELinux policy (use `semanage port`).

---

## Management Tools

- **firewall-cmd:** Command-line tool for firewalld.
- **firewall-config:** GUI tool for firewalld.
- **Cockpit:** Web-based management interface (access via `https://<server-ip>:9090`).

---

## Useful Resources

- Official documentation and tutorials:
  - https://firewalld.org/
  - https://linuxhandbook.com/firewalld-cmd/
  - https://www.linuxteck.com/basic-useful-firewall-cmd-commands-in-linux/
  - https://infotechys.com/using-the-firewall-cmd-command-in-linux/
  - https://www.tecmint.com/configure-firewalld-rhel-rocky-almalinux/

---

## Summary

- Firewalld is the modern firewall solution for RHEL 7+.
- Zones and services simplify firewall management.
- SELinux adds another layer of security.
- Use `firewall-cmd`, `firewall-config`, and Cockpit for management.
- Always reload firewalld after making permanent changes.
- SELinux must be configured for custom ports/services.

---

Let me know if you need this in a specific format (Markdown, PDF, etc.) or want more details on any section!