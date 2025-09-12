Here are detailed, explainable notes based on the contents of `02_Examples of SE-Linux Notes.txt`:

---

## SELinux (Security-Enhanced Linux) Overview

SELinux is a security architecture for Linux systems that enforces access control policies. It operates in different modes and uses contexts, policies, and booleans to manage permissions for files, processes, and ports.

---

### Changing SELinux Modes

- **Check SELinux status:**  
  - `getenforce` (shows current mode: Enforcing, Permissive, Disabled)
  - `sestatus` (detailed status)
- **Change mode temporarily:**  
  - `setenforce 0` (Permissive)
  - `setenforce 1` (Enforcing)
- **Change mode permanently:**  
  - Edit `/etc/selinux/config` and set `SELINUX=enforcing|permissive|disabled`
  - Reboot required for permanent changes.

---

### SELinux Contexts

- Every file, directory, and process has a security context (user:role:type:level).
- Example:  
  - `/abc` might have `unconfined_u:object_r:etc_runtime_t:s0`
  - `/var/www/html/index.html` might have `unconfined_u:object_r:httpd_sys_content_t:s0`
- Use `ls -lZ` to view SELinux contexts.

---

### SELinux and Services

- Processes like `sshd` and `httpd` run with specific SELinux types (`sshd_t`, `httpd_t`).
- Configuration files and content must have correct context types for services to work.
- Example:  
  - Web content should have `httpd_sys_content_t` for Apache to serve it.

---

### File Operations and SELinux

- **Copy (`cp`) vs Move (`mv`):**
  - `cp` assigns the target directory's context to the new file.
  - `mv` retains the original file's context, which may cause access issues.
- **Fixing context issues:**
  - Use `chcon` to change context manually.
  - Use `restorecon` to reset context based on policy.

---

### SELinux Policy Management

- **Context files:** Located in `/etc/selinux/targeted/contexts/files/`
- **Port labeling:**  
  - List allowed ports: `semanage port -l | grep http`
  - Add new port: `semanage port -a -t http_port_t -p tcp 82`
- **Booleans:**  
  - Control specific SELinux features (e.g., `httpd_enable_homedirs`)
  - View: `getsebool -a`
  - Set: `setsebool -P boolean_name 1`

---

### Troubleshooting SELinux

- **Logs:**  
  - `/var/log/messages`
  - Audit logs: `ausearch -m AVC -ts today|recent`
- **Common issues:**  
  - "403 Forbidden" errors often relate to incorrect SELinux context or permissions.
  - Use `chcon` or `restorecon` to fix file contexts.

---

### Practical Examples

- **Serving custom directories:**  
  - Change Apache's `DocumentRoot` to `/custom`
  - Set context: `chcon -R -t httpd_sys_content_t /custom`
- **User web directories:**  
  - Enable `public_html` for users.
  - Set correct permissions and context.
  - Enable SELinux boolean: `setsebool -P httpd_enable_homedirs 1`

---

### Port Forwarding and Firewall Integration

- Open custom ports in firewalld:  
  - `firewall-cmd --permanent --add-port=82/tcp`
  - `firewall-cmd --reload`
- Forward ports:  
  - `firewall-cmd --permanent --add-forward-port=port=80:proto=tcp:toport=82`
  - Remove with `--remove-forward-port`
- Use `nmap` to verify open ports.

---

### Rich Rules in Firewalld

- Allow specific subnet:
  ```bash
  sudo firewall-cmd --permanent --add-rich-rule='
    rule family="ipv4"
    source address="192.168.1.0/24"
    accept
  '
  ```
- Drop all other connections:
  ```bash
  sudo firewall-cmd --permanent --add-rich-rule='
    rule family="ipv4"
    reject
  '
  ```
- Reload firewalld after changes.

---

## Summary

- SELinux enforces security using contexts, policies, and booleans.
- Correct context is crucial for service operation.
- Use `chcon`, `restorecon`, and booleans to manage access.
- Integrate SELinux with firewalld for robust security.
- Troubleshoot using logs and audit tools.

---

Let me know if you need these notes in a specific format or want more details on any section!