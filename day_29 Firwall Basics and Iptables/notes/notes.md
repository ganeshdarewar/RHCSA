# IP-Tables Examples — Detailed Explanation & Notes

---

## 1. **Lab Setup and Preparation**

- **VM-1:** RHEL 9 Server Node (e.g., `node10.example.com`, IP: `192.168.1.108`)
- **VM-2:** RHEL 9 Client Node (e.g., `node20.example.com`, IP: `192.168.1.105`)
- **Network:** Both VMs must be networked (via DHCP or static IP) and able to ping each other.

---

## 2. **YUM Repository Configuration (for Package Installation)**

- Mount the RHEL ISO and create a local YUM repo:
  ```bash
  mount /dev/sr0 /mnt
  vim /etc/yum.repos.d/abc.repo
  ```
  Example repo file:
  ```
  [path-1]
  name=abc
  baseurl=file:///mnt/BaseOS
  enabled=1
  gpgcheck=0

  [path-2]
  name=xyz
  baseurl=file:///mnt/AppStream
  enabled=1
  gpgcheck=0
  ```
- Clean and verify repos:
  ```bash
  yum clean all
  yum repolist all
  ```

---

## 3. **Switching from firewalld to iptables**

- **Disable and mask firewalld:**
  ```bash
  systemctl stop firewalld
  systemctl disable firewalld
  systemctl mask firewalld
  ```
- **Install and enable iptables:**
  ```bash
  yum install iptables* -y
  systemctl start iptables
  systemctl enable iptables
  ```
- **Check iptables status and rules:**
  ```bash
  iptables -L
  cat /etc/sysconfig/iptables
  ```

---

## 4. **Basic iptables Commands**

- **List rules:**  
  `iptables -L`, `iptables -L INPUT`, `iptables -L OUTPUT`, `iptables -L FORWARD`
- **Flush rules:**  
  `iptables -F` or `iptables -F INPUT`
- **Save rules:**  
  `service iptables save`
- **Delete rule by number:**  
  `iptables -L INPUT --line-number` then `iptables -D INPUT <num>`

---

## 5. **Blocking SSH Access Using iptables**

- **Block SSH from a specific IP:**
  ```bash
  iptables -I INPUT -p tcp --dport 22 -s 192.168.1.105 -j REJECT
  service iptables save
  ```
- **Block SSH from a subnet:**
  ```bash
  iptables -I INPUT -p tcp --dport 22 -s 192.168.1.0/24 -j REJECT
  ```
- **Block SSH from all IPs:**
  ```bash
  iptables -I INPUT -p tcp --dport 22 -j REJECT
  ```
- **Allow only a specific IP/subnet and reject others:**
  ```bash
  iptables -F
  iptables -I INPUT -p tcp --dport 22 -s 192.168.0.0/24 -j ACCEPT
  iptables -I INPUT -p tcp --dport 22 -s 192.168.1.0/24 -j REJECT
  ```

---

## 6. **Blocking ICMP (Ping) Requests**

- **Block ping from a specific IP:**
  ```bash
  iptables -I INPUT -p icmp -s 192.168.1.105 -j REJECT
  ```
- **Block ping from all except a specific IP:**
  ```bash
  iptables -I INPUT -p icmp ! -s 192.168.1.105 -j DROP
  ```

---

## 7. **Blocking by MAC Address**

- **Block SSH from a specific MAC:**
  ```bash
  iptables -I INPUT -m mac --mac-source 00:0c:29:3c:ad:74 -p tcp --dport 22 -j REJECT
  ```
- **Block all services from a MAC:**
  ```bash
  iptables -I INPUT -m mac --mac-source 00:0c:29:3c:ad:74 -j REJECT
  ```

---

## 8. **Blocking Multiple Ports**

- **Block SSH, FTP, HTTP, HTTPS from a specific IP:**
  ```bash
  iptables -I INPUT -m multiport -p tcp --dport 22,21,80,443 -s 192.168.1.105 -j REJECT
  ```

---

## 9. **Blocking Outgoing Traffic**

- **Block outgoing SSH to a specific IP:**
  ```bash
  iptables -I OUTPUT -p tcp --dport 22 -d 192.168.1.105 -j DROP
  ```

---

## 10. **HTTP Service Example with iptables**

- **Install and start Apache:**
  ```bash
  yum install httpd* -y
  systemctl start httpd
  systemctl enable httpd
  ```
- **Test locally and remotely using curl or browser.**
- **Block HTTP from a specific IP:**
  ```bash
  iptables -I INPUT -p tcp --dport 80 -s 192.168.1.105 -j REJECT
  ```

---

## 11. **Custom Ports and SELinux**

- **Change SSH port (e.g., to 1122):**
  1. Edit `/etc/ssh/sshd_config` and set `Port 1122`
  2. Add port to SELinux:
     ```bash
     semanage port -a -t ssh_port_t -p tcp 1122
     ```
  3. Restart sshd and test with:
     ```bash
     ssh -p 1122 user@host
     ```
- **Change Apache port (e.g., to 85):**
  1. Edit `/etc/httpd/conf/httpd.conf` and set `Listen 85`
  2. Add port to SELinux:
     ```bash
     semanage port -a -t http_port_t -p tcp 85
     ```
  3. Restart httpd and test with:
     ```bash
     curl http://<ip>:85
     ```

---

## 12. **Port Forwarding**

- **With iptables:**
  ```bash
  iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 1122
  ```
- **With firewalld:**
  ```bash
  firewall-cmd --permanent --add-forward-port=port=80:proto=tcp:toport=85
  firewall-cmd --reload
  ```

---

## 13. **Switching Back to firewalld**

- **Stop and disable iptables:**
  ```bash
  iptables -F
  service iptables save
  systemctl stop iptables
  systemctl disable iptables
  ```
- **Unmask, start, and enable firewalld:**
  ```bash
  systemctl unmask firewalld
  systemctl start firewalld
  systemctl enable firewalld
  ```
- **Add ports/services to firewalld:**
  ```bash
  firewall-cmd --permanent --add-port=1122/tcp
  firewall-cmd --permanent --add-service=http
  firewall-cmd --reload
  ```

---

## 14. **Troubleshooting**

- **If a service fails to start on a custom port:**  
  Check SELinux port context with `semanage port -l | grep <service>`, and add the port if needed.
- **Check active rules:**  
  `iptables -L -v -n --line-number`
- **Check listening ports:**  
  `netstat -tunlp | grep <service>`

---

## 15. **Summary Table**

| Task                        | iptables Example                                                                 |
|-----------------------------|----------------------------------------------------------------------------------|
| Block SSH from IP           | `iptables -I INPUT -p tcp --dport 22 -s <IP> -j REJECT`                         |
| Block Ping from IP          | `iptables -I INPUT -p icmp -s <IP> -j REJECT`                                   |
| Block by MAC                | `iptables -I INPUT -m mac --mac-source <MAC> -j REJECT`                         |
| Block Multiple Ports        | `iptables -I INPUT -m multiport -p tcp --dport 22,21,80,443 -s <IP> -j REJECT`  |
| Block Outgoing SSH          | `iptables -I OUTPUT -p tcp --dport 22 -d <IP> -j DROP`                          |
| Port Forwarding             | `iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 1122`    |
| Allow/Block HTTP            | `iptables -I INPUT -p tcp --dport 80 -s <IP> -j REJECT`                         |

---

**End of Detailed IP-Tables Notes**