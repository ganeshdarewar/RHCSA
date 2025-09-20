Here are detailed, explainable notes based on the contents of `01_Chage-password-policy-theory-session-notes (1).txt`:

---

## Linux Password Policy and Aging

### Key Files
- `/etc/passwd`: Stores user account information.
- `/etc/group`: Stores group information.
- `/etc/shadow`: Stores encrypted passwords and password aging policy.

---

### Password Hashing Algorithms
- **MD5**
- **SHA-256**
- **SHA-512** (default in RHEL 7/8/9)

---

### Password Policy Types
1. **Default Policy**
   - Controlled by `/etc/default/useradd` and `/etc/login.defs`.
2. **Custom Policy**
   - Set per user using the `chage` command.

---

### `/etc/shadow` Fields (9 total)
1. **Username**
2. **Encrypted password/hash**
3. **Last password change (days since Jan 1, 1970)**
4. **Minimum password age (days)**
5. **Maximum password age (days)**
6. **Warning period before password expires (days)**
7. **Inactive period after password expires (days)**
8. **Account expiration date (days since Jan 1, 1970)**
9. **Reserved (future use)**

---

### `chage` Command Usage

- **List aging info:**  
  `chage -l <username>`
- **Set minimum days:**  
  `chage -m <days> <username>`
- **Set maximum days:**  
  `chage -M <days> <username>`
- **Set warning days:**  
  `chage -W <days> <username>`
- **Set inactive days:**  
  `chage -I <days> <username>`
- **Set account expiry:**  
  `chage -E <YYYY-MM-DD> <username>`
- **Force password change on next login:**  
  `chage -d 0 <username>`

---

### Example Workflow

1. **Create user and set password:**
   ```bash
   useradd sachin
   passwd sachin
   ```
2. **View user info:**
   ```bash
   grep sachin /etc/passwd
   grep sachin /etc/group
   grep sachin /etc/shadow
   ```
3. **Set password aging policy:**
   ```bash
   chage -m 10 -M 90 -I 7 -W 15 sachin
   chage -E 2027-12-31 sachin
   ```
4. **Check policy:**
   ```bash
   chage -l sachin
   ```
5. **Force password change on first login:**
   ```bash
   chage -d 0 sachin
   ```

---

### Password Expiry and Account Locking

- **Password expires:** User must change password after max days.
- **Inactive period:** Account becomes inactive if password not changed after expiry.
- **Account expires:** Account is locked after this date.

---

### Bulk Policy Changes

- Use a script to apply policies to multiple users:
  ```bash
  for i in $(cat /tmp/user-info); do
    chage -d 0 -E 2027-12-31 $i
  done
  ```

---

### Default Policy Files

- `/etc/default/useradd`: Controls default settings for new users.
- `/etc/login.defs`: Controls system-wide password policy (e.g., `PASS_MAX_DAYS`).

---

### Interactive `chage` Usage

- Run `chage <username>` to interactively set aging parameters.

---

### Example Field Meanings

| Field                                                     | Meaning                                                                                          |
| --------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| **Last password change**                                  | Date when password was last changed                                                              |
| **Password expires**                                      | Date when password will expire                                                                   |
| **Password inactive**                                     | Date when account becomes inactive if password not changed                                       |
| **Account expires**                                       | Date when account is locked                                                                      |
| **Minimum number of days between password change**         | Minimum wait before user can change password again                                               |
| **Maximum number of days between password change**         | Maximum allowed time before password must be changed                                             |
| **Number of days of warning before password expires**      | Days before expiry when user is warned                                                           |

---

### Additional Topics Mentioned

- **PAM (Pluggable Authentication Modules):**  
  Configuration files in `/etc/pam.d` and `/etc/security` for authentication and security policies.
- **ulimit command:**  
  Used to set user resource limits.

---

Let me know if you need these notes in a specific format or want more details on any section!