# Linux System Administration and Bash Scripting Guide

## Table of Contents

1. [Essential Linux Commands Reference](#essential-linux-commands-reference)
    - [File and Directory Management](#file-and-directory-management)
    - [Process Management](#process-management)
    - [System Monitoring](#system-monitoring)
    - [User Management](#user-management)
    - [Network Configuration](#network-configuration)
2. [Advanced Bash Scripting Tutorial](#advanced-bash-scripting-tutorial)
    - [Script Structure and Best Practices](#script-structure-and-best-practices)
    - [Variables, Arrays, and Data Types](#variables-arrays-and-data-types)
    - [Control Structures](#control-structures)
    - [Functions and Modules](#functions-and-modules)
    - [Error Handling and Debugging](#error-handling-and-debugging)
    - [Regular Expressions](#regular-expressions)
    - [Working with Files and Processes](#working-with-files-and-processes)
3. [Real-World Scenarios for Senior Linux Engineer Interviews](#real-world-scenarios-for-senior-linux-engineer-interviews)
    - [System Troubleshooting Cases](#system-troubleshooting-cases)
    - [Performance Optimization Challenges](#performance-optimization-challenges)
    - [Security Hardening Examples](#security-hardening-examples)
    - [High Availability Configurations](#high-availability-configurations)
    - [Disaster Recovery Planning](#disaster-recovery-planning)
    - [Infrastructure Automation](#infrastructure-automation)
    - [CI/CD Pipeline Implementation](#cicd-pipeline-implementation)

---

## Essential Linux Commands Reference

### File and Directory Management

- **ls**: List directory contents
  - `ls -l /path/to/dir`  
    _Lists files with details._
  - **Pitfall**: Hidden files are not shown by default (`ls -a`).
  - **Best Practice**: Use `ls -lh` for human-readable sizes.
  - **Man page**: `man ls`

- **cp**: Copy files and directories
  - `cp source.txt /dest/dir/`
  - `cp -r dir1 dir2` (recursive)
  - **Pitfall**: Overwrites without warning unless `-i` is used.
  - **Best Practice**: Use `-a` for archiving (preserves attributes).
  - **Man page**: `man cp`

- **mv**: Move/rename files
  - `mv oldname.txt newname.txt`
  - **Pitfall**: Overwrites destination without prompt unless `-i`.
  - **Man page**: `man mv`

- **rm**: Remove files/directories
  - `rm file.txt`
  - `rm -rf /path/to/dir/`
  - **Pitfall**: Irrecoverable deletion. Use with caution.
  - **Best Practice**: Use `-i` for interactive mode.
  - **Man page**: `man rm`

- **chmod**: Change file permissions
  - `chmod 755 script.sh`
  - **Best Practice**: Use symbolic mode (`chmod u+x file`).
  - **Security**: Avoid `777` permissions in production.
  - **Man page**: `man chmod`

- **chown**: Change file owner/group
  - `chown user:group file.txt`
  - **Security**: Restrict ownership to least privilege.
  - **Man page**: `man chown`

### Process Management

- **ps**: Display process status
  - `ps aux | grep nginx`
  - **Best Practice**: Use `ps -ef` for full-format listing.
  - **Man page**: `man ps`

- **top**: Real-time process monitoring
  - `top`
  - **Tip**: Use `htop` for enhanced UI (if installed).
  - **Man page**: `man top`

- **kill**: Terminate processes
  - `kill -9 <PID>`
  - **Best Practice**: Try `kill -15` (SIGTERM) before `-9` (SIGKILL).
  - **Man page**: `man kill`

- **nice/renice**: Set process priority
  - `nice -n 10 command`
  - `renice -n 5 -p <PID>`
  - **Man page**: `man nice`, `man renice`

### System Monitoring

- **df**: Disk space usage
  - `df -h`
  - **Best Practice**: Monitor `/` and `/var` for space issues.
  - **Man page**: `man df`

- **du**: Directory/file space usage
  - `du -sh /var/log/*`
  - **Man page**: `man du`

- **free**: Memory usage
  - `free -m`
  - **Man page**: `man free`

- **iostat**: CPU and I/O statistics
  - `iostat -xz 1`
  - **Man page**: `man iostat`

- **netstat**: Network connections
  - `netstat -tulnp`
  - **Best Practice**: Use `ss` for modern systems.
  - **Man page**: `man netstat`, `man ss`

### User Management

- **useradd**: Add new user
  - `sudo useradd -m -s /bin/bash alice`
  - **Man page**: `man useradd`

- **usermod**: Modify user
  - `sudo usermod -aG sudo alice`
  - **Man page**: `man usermod`

- **passwd**: Change user password
  - `sudo passwd alice`
  - **Security**: Enforce strong password policies.
  - **Man page**: `man passwd`

### Network Configuration

- **ip**: Network interface management
  - `ip addr show`
  - `ip route`
  - **Man page**: `man ip`

- **ifconfig**: Legacy network config
  - `ifconfig -a`
  - **Note**: Deprecated on modern systems; use `ip`.
  - **Man page**: `man ifconfig`

- **ping**: Test network connectivity
  - `ping 8.8.8.8`
  - **Man page**: `man ping`

- **traceroute**: Trace network path
  - `traceroute google.com`
  - **Man page**: `man traceroute`

---

## Advanced Bash Scripting Tutorial

### Script Structure and Best Practices

- Start scripts with a shebang: `#!/bin/bash`
- Use `set -euo pipefail` for robust error handling.
- Comment code and use meaningful variable names.
- Example:

```bash
#!/bin/bash
set -euo pipefail
# Backup script example
SRC="/data"
DEST="/backup"
rsync -av "$SRC" "$DEST"
```

### Variables, Arrays, and Data Types

- Variables: `VAR=value` (no spaces)
- Arrays: `arr=(one two three)`
- Access: `${arr[0]}`
- Example:

```bash
name="Alice"
echo "Hello, $name!"
nums=(1 2 3)
echo "First: ${nums[0]}"
```

### Control Structures

- **if**:
  ```bash
  if [ -f /etc/passwd ]; then
    echo "File exists"
  fi
  ```
- **case**:
  ```bash
  case $1 in
    start) echo "Starting..." ;;
    stop)  echo "Stopping..." ;;
    *)     echo "Usage: $0 {start|stop}" ;;
  esac
  ```
- **loops**:
  - For:
    ```bash
    for i in {1..5}; do echo $i; done
    ```
  - While:
    ```bash
    while read line; do echo $line; done < file.txt
    ```

### Functions and Modules

```bash
function greet() {
  echo "Hello, $1!"
}
greet "World"
```
- Modularize scripts for reuse.

### Error Handling and Debugging

- `set -e` (exit on error), `set -u` (undefined var), `set -x` (debug)
- Trap errors:
  ```bash
  trap 'echo "Error on line $LINENO"' ERR
  ```
- Check exit codes: `if ! command; then ... fi`

### Regular Expressions

- Use `[[ "$var" =~ regex ]]` for matching.
- Example:
  ```bash
  email="user@example.com"
  if [[ $email =~ ^[a-z0-9._%+-]+@[a-z0-9.-]+\.[a-z]{2,}$ ]]; then
    echo "Valid email"
  fi
  ```

### Working with Files and Processes

- Read file line by line:
  ```bash
  while IFS= read -r line; do
    echo "$line"
  done < file.txt
  ```
- Redirect output: `command > out.txt 2>&1`
- Background jobs: `command &`

---

## Real-World Scenarios for Senior Linux Engineer Interviews

### System Troubleshooting Cases
- **Scenario**: High CPU usage
  - Use `top`, `ps aux --sort=-%cpu | head`
  - Check logs: `/var/log/syslog`, `/var/log/messages`
  - **Best Practice**: Automate log rotation and alerting.

### Performance Optimization Challenges
- **Scenario**: Disk I/O bottleneck
  - Use `iostat`, `iotop`, `df -h`
  - Tune filesystem mount options, use LVM/RAID.
  - **Best Practice**: Monitor with Prometheus/Grafana.

### Security Hardening Examples
- **Scenario**: Secure SSH access
  - Disable root login: `PermitRootLogin no` in `/etc/ssh/sshd_config`
  - Use key-based auth, fail2ban, firewall (`ufw`/`firewalld`).
  - **Best Practice**: Regularly patch and audit.

### High Availability Configurations
- **Scenario**: Web server HA
  - Use load balancers (HAProxy, Nginx), keepalived, clustering (Pacemaker).
  - Shared storage: NFS, GlusterFS.
  - **Best Practice**: Test failover regularly.

### Disaster Recovery Planning
- **Scenario**: Automated backups
  - Use `rsync`, `tar`, cron jobs, offsite/cloud storage.
  - Document and test restore procedures.

### Infrastructure Automation
- **Scenario**: Provision servers with Ansible
  - Write playbooks for user creation, package install, config management.
  - **Best Practice**: Use version control (Git) for infrastructure code.

### CI/CD Pipeline Implementation
- **Scenario**: Deploy app with Jenkins
  - Automate build, test, deploy steps with Bash scripts and Jenkinsfiles.
  - Integrate with Docker/Kubernetes for containerized workflows.

---

## References
- [The Linux Command Line](https://linuxcommand.org/)
- [Bash Guide for Beginners](https://tldp.org/LDP/Bash-Beginners-Guide/html/)
- [Advanced Bash-Scripting Guide](https://tldp.org/LDP/abs/html/)
- [Linux man pages](https://man7.org/linux/man-pages/)
- [Official Bash Reference](https://www.gnu.org/software/bash/manual/bash.html)

---

**Target Audience:** Experienced Linux administrators preparing for senior roles.

**Security Note:** Always follow the principle of least privilege, keep systems patched, and audit regularly.

**Common Pitfalls:**
- Running destructive commands as root without backups
- Ignoring exit codes in scripts
- Weak file permissions
- Not monitoring system health

**Best Practices:**
- Automate repetitive tasks
- Use version control for scripts/configs
- Document procedures and recovery steps
- Regularly review and test security controls
