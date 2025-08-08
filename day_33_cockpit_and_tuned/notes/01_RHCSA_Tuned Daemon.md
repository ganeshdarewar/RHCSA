# Tuned Daemon — Detailed Explanation & Notes

---

## 1. **What is Tuned?**

- **Tuned** is a dynamic system tuning daemon in RHEL/CentOS/other Linux distributions.
- It helps optimize system performance for specific workloads (e.g., virtual machines, desktops, servers) by applying pre-defined or custom tuning profiles.
- It can adjust CPU, disk, network, and power management settings automatically or manually.

---

## 2. **Installation and Service Management**

- **Install Tuned:**
  ```bash
  yum install tuned -y
  ```
- **Start and Enable Tuned Service:**
  ```bash
  systemctl start tuned.service
  systemctl enable tuned.service
  ```
- **Check if `tuned-adm` is available:**
  ```bash
  which tuned-adm
  ```

---

## 3. **Tuned Profiles**

- **Profiles are sets of tuning parameters** for different use cases.
- **List available profiles:**
  ```bash
  tuned-adm list
  ```
  Example output:
  ```
  - accelerator-performance     - Throughput performance based tuning
  - balanced                    - General non-specialized tuned profile
  - desktop                     - Optimize for desktop use-case
  - hpc-compute                 - Optimize for HPC compute workloads
  - latency-performance         - Optimize for deterministic performance
  - network-latency             - Optimize for low latency network performance
  - network-throughput          - Optimize for streaming network throughput
  - optimize-serial-console     - Optimize for serial console use
  - powersave                   - Optimize for low power consumption
  - throughput-performance      - Excellent performance for common server workloads
  - virtual-guest               - Optimize for running inside a virtual guest
  - virtual-host                - Optimize for running KVM guests
  ```
- **Current active profile** is shown at the end of the list.

---

## 4. **Checking and Setting Profiles**

- **Check current active profile:**
  ```bash
  tuned-adm active
  ```
- **Get recommended profile for your system:**
  ```bash
  tuned-adm recommend
  ```
- **Set a profile (e.g., for a virtual machine):**
  ```bash
  tuned-adm profile virtual-guest
  ```
- **Verify the change:**
  ```bash
  tuned-adm active
  ```
  Output should show: `Current active profile: virtual-guest`

---

## 5. **Profile Locations**

- **Profile configuration files:**  
  - `/usr/lib/tuned/` — Default profiles provided by the system.
  - `/etc/tuned/` — Custom or active profile settings.
- **Check available profiles:**
  ```bash
  ls /usr/lib/tuned
  ```
- **Check tuned configuration directory:**
  ```bash
  ls /etc/tuned
  ```

---

## 6. **Manual (Static) vs Dynamic Tuning**

- **Manual/Static Tuning:**  
  - Set a profile once; system uses those settings until changed.
- **Dynamic Tuning:**  
  - Tuned can automatically adjust settings in real time based on system activity.

---

## 7. **Enabling Dynamic Tuning**

- **Edit the main configuration file:**
  ```bash
  vim /etc/tuned/tuned-main.conf
  ```
- **Set the following parameters:**
  ```
  dynamic_tuning = 1
  update_interval = 15
  ```
  - `dynamic_tuning = 1` enables dynamic tuning.
  - `update_interval = 15` sets how often (in seconds) tuned checks and updates settings.
- **Restart the tuned service to apply changes:**
  ```bash
  systemctl restart tuned.service
  ```

---

## 8. **Summary of Common Commands**

| Command                                 | Description                                      |
|------------------------------------------|--------------------------------------------------|
| `yum install tuned -y`                   | Install tuned package                            |
| `systemctl start tuned.service`          | Start tuned daemon                               |
| `systemctl enable tuned.service`         | Enable tuned to start at boot                    |
| `tuned-adm list`                         | List all available tuning profiles               |
| `tuned-adm active`                       | Show the currently active profile                |
| `tuned-adm recommend`                    | Show the recommended profile for your system     |
| `tuned-adm profile <profile-name>`       | Set the active profile                           |
| `vim /etc/tuned/tuned-main.conf`         | Edit main tuned configuration file               |
| `systemctl restart tuned.service`        | Restart tuned to apply configuration changes     |

---

## 9. **Best Practices**

- Use the **recommended profile** for your environment unless you have specific needs.
- For virtual machines, `virtual-guest` is usually recommended.
- Enable **dynamic tuning** for workloads that change frequently.
- Always restart the tuned service after changing configuration files.

---

**End of Tuned Daemon Detailed Notes**