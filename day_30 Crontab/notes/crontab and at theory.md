# Crontab and at Command — Detailed Explanation Notes

---

## 1. **Types of Command Execution in Linux**

- **Manual Execution:**  
  Commands/scripts are run directly by the administrator.
- **Automated Execution:**  
  Uses scheduling utilities like `at` and `crontab` for automatic execution.

---

## 2. **Difference Between `at` and `crontab`**

- **`at` Command:**
  - Schedules a command or script to run **once** at a specific future time.
  - Not repeated; single execution.
  - Example:
    ```bash
    at 10:05 AM
    > rsync -av /sqldata /backup
    (Press Ctrl+D to save and exit)
    ```
- **`crontab` Command:**
  - Schedules commands/scripts to run **repeatedly** at specified intervals (minute, hour, day, month, weekday).
  - Used for recurring tasks like backups, monitoring, etc.
  - Example:
    ```bash
    05 10 * * * rsync -av /sqldata /backup
    ```

---

## 3. **Crontab Overview**

- **Purpose:**  
  Automate repetitive tasks (backups, health checks, monitoring).
- **Who can use:**  
  Both root and normal users.
- **Access Control:**  
  - `/etc/cron.allow`: Only listed users can use crontab.
  - `/etc/cron.deny`: Listed users are denied crontab access.
  - If both files are blank, all users are allowed.

---

## 4. **Crontab Service and Files**

- **Packages:** `crontabs`, `cronie`
- **Daemon/Service:** `crond`
- **Main Config File:** `/etc/crontab`
- **Log File:** `/var/log/cron`

---

## 5. **Crontab Commands**

- `crontab -l` — List current user's cron jobs.
- `crontab -e` — Edit current user's cron jobs.
- `crontab -r` — Remove all cron jobs for current user.
- `crontab -e -u username` — Edit cron jobs for another user (root only).
- `crontab -l -u username` — List cron jobs for another user.
- `crontab -r -u username` — Remove cron jobs for another user.

---

## 6. **Crontab File Structure**

- **Two Parts:**
  1. **Time Table:** 5 fields specifying when the command runs.
  2. **Command:** The command or script to execute.

### **Time Table Fields:**
| Field         | Allowed Values | Meaning         |
|---------------|---------------|----------------|
| Minute        | 0–59          | Minute         |
| Hour          | 0–23          | Hour           |
| Day of Month  | 1–31          | Day of Month   |
| Month         | 1–12          | Month          |
| Day of Week   | 0–7           | Day of Week (0/7=Sunday, 1=Monday, ..., 6=Saturday) |

**Example:**
```
30 09 * * * command
```
Runs at 09:30 every day.

---

## 7. **Crontab Time Table Syntax Examples**

- `* * * * *` — Every minute.
- `30 09 * * *` — At 09:30 every day.
- `30 21 * * *` — At 21:30 every day.
- `30 09 * * 1` — At 09:30 every Monday.
- `30 09 * * 1,3,5` — At 09:30 on Monday, Wednesday, Friday.
- `30 09 * * 0,6` — At 09:30 on Sunday and Saturday.
- `30 09 * * 1-5` — At 09:30 Monday to Friday.
- `*/5 * * * *` — Every 5 minutes.
- `* */2 * * *` — Every 2 hours.

### **Special Symbols:**
- `*` — All possible values (every minute, hour, etc.)
- `,` — List (AND operator, e.g., 1,3,5)
- `-` — Range (e.g., 1-5)
- `/` — Step value (repeat, e.g., */2 for every 2 units)

---

## 8. **Practical Usage**

- **Automated Backups:**
  ```
  05 10 * * * rsync -av /sqldata /backup
  ```
  Runs backup at 10:05 AM daily.

- **Automated Health Checks:**
  - Schedule scripts to run at regular intervals for monitoring.

---

## 9. **`at` Command Usage**

- Used for one-time execution at a specific time.
- Example:
  ```bash
  at 10:05 AM
  rsync -av /sqldata /backup
  (Press Ctrl+D to save and exit)
  ```
- List scheduled `at` jobs:
  ```bash
  atq
  ```
- Remove a scheduled job:
  ```bash
  atrm <jobnumber>
  ```
- View job content:
  ```bash
  at -c <jobnumber>
  ```

---

## 10. **Summary Table**

| Feature      | `at` Command                | `crontab` Command           |
|--------------|----------------------------|-----------------------------|
| Execution    | One-time                   | Repeated/Scheduled          |
| Syntax       | `at <time>`                | `minute hour day month weekday command` |
| List Jobs    | `atq` or `at -l`           | `crontab -l`                |
| Remove Job   | `atrm <jobnumber>`         | `crontab -r`                |
| Edit Jobs    | N/A                        | `crontab -e`                |

---

**End of Detailed Crontab and at Command Notes**