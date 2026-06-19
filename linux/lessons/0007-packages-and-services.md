# Lesson 7: Package Management & Services

**Lesson 7** · ~25 min · Hands-on

> [!NOTE] Mission tie-in
> Every server needs software installed, services running, users managed, and logs rotated. These are the daily tasks of anyone managing Linux servers. This lesson covers the practical sysadmin skills you'll use constantly in DevOps.

## What You'll Learn

- Install, update, and remove packages with `apt`
- Manage services with `systemctl`
- View and rotate logs
- Create users and groups

## Package Management (Debian/Ubuntu)

> [!TIP] These commands assume a Debian-based system (Ubuntu, Debian). If you're on RHEL/CentOS/Fedora, replace `apt` with `dnf` or `yum`.

### Updating Package Lists

```bash
sudo apt update          # Refresh the package index (run this first)
```

### Installing Packages

```bash
sudo apt install nginx          # Install a package
sudo apt install curl htop git  # Install multiple at once
```

### Removing Packages

```bash
sudo apt remove nginx           # Remove package (keeps config files)
sudo apt purge nginx            # Remove package + config files
sudo apt autoremove             # Remove unused dependencies
```

### Searching and Info

```bash
apt search "web server"         # Search for packages
apt show nginx                  # Show package details
dpkg -l                         # List all installed packages
dpkg -l | grep nginx            # Check if a specific package is installed
```

> [!TIP] Always run `sudo apt update` before `sudo apt install` — otherwise you install from a stale index.

## Services with systemd

Most Linux servers use `systemd` to manage services. The `systemctl` command controls them:

```bash
sudo systemctl status nginx     # Is it running?
sudo systemctl start nginx      # Start it
sudo systemctl stop nginx       # Stop it
sudo systemctl restart nginx    # Restart it
sudo systemctl reload nginx     # Reload config without dropping connections
sudo systemctl enable nginx     # Start on boot
sudo systemctl disable nginx    # Don't start on boot
```

Common states:
- `active (running)` — working fine
- `inactive (dead)` — stopped
- `failed` — crashed or misconfigured
- `enabled` — starts automatically at boot

```bash
# See all running services
systemctl list-units --type=service --state=running

# See failed services
systemctl --failed
```

## Viewing Logs with journalctl

```bash
journalctl -u nginx             # Logs for a specific service
journalctl -u nginx --since "1 hour ago"   # Last hour
journalctl -u nginx -f          # Follow logs in real-time
journalctl -u nginx -n 50       # Last 50 lines
```

## Log Files

Traditional log files live in `/var/log`:

```bash
ls /var/log
# syslog, auth.log, kern.log, nginx/, postgresql/, ...

# Watch auth log for SSH attempts
sudo tail -f /var/log/auth.log

# Count failed SSH attempts
grep "Failed password" /var/log/auth.log | wc -l
```

> [!WARNING] Log files can grow huge on busy servers. Use `logrotate` to manage them: `sudo nano /etc/logrotate.d/nginx` to configure rotation rules.

## Users and Groups

```bash
sudo useradd -m alice           # Create user with home directory
sudo passwd alice               # Set password
sudo usermod -aG sudo alice     # Add to sudo group (admin privileges)
sudo userdel -r alice           # Delete user + home directory

# Who is logged in?
who
w
```

### File Permissions with Users

```bash
# Change ownership
sudo chown alice:alice file.txt

# Give a group access
sudo addgroup webteam
sudo usermod -aG webteam alice
sudo chown :webteam /var/www/html
sudo chmod 775 /var/www/html     # Owner and group can write
```

## sudo

`sudo` lets authorized users run commands as root:

```bash
sudo whoami                  # → "root" (if you have sudo access)
sudo -l                      # List what commands you can run as sudo
sudo -u alice whoami         # Run as another user
```

> [!TIP] If a command says "permission denied," 90% of the time you just need to add `sudo` at the front. But be careful — `sudo` gives you full power.

## Hands-On Exercise

Run this if you have a Linux environment with sudo:

```bash
# 1. Update package list
sudo apt update

# 2. Install nginx
sudo apt install -y nginx

# 3. Check it's running
sudo systemctl status nginx
curl -I http://localhost          # Should get a response

# 4. See nginx logs
sudo journalctl -u nginx -n 10

# 5. Stop nginx
sudo systemctl stop nginx

# 6. Create a test user
sudo useradd -m testuser
sudo passwd testuser          # Set a password

# 7. Check auth log (might not exist on all distros)
sudo tail /var/log/auth.log 2>/dev/null || echo "auth.log not available"

# 8. Cleanup
sudo systemctl disable nginx
sudo apt remove -y nginx
sudo userdel -r testuser
```

## Self-Quiz

<details>
<summary><strong>1.</strong> What command installs a package on Ubuntu?</summary>

`sudo apt install <package>` — but always run `sudo apt update` first to refresh the package index.
</details>

<details>
<summary><strong>2.</strong> How do you make a service start automatically at boot?</summary>

`sudo systemctl enable <service>` — and `sudo systemctl disable <service>` to stop it from starting at boot.
</details>

<details>
<summary><strong>3.</strong> How do you see logs for a specific service?</summary>

`journalctl -u <service>` — add `-f` to follow live, `-n 50` for last 50 lines, or `--since "1 hour ago"` to filter by time.
</details>

<details>
<summary><strong>4.</strong> What does <code>sudo usermod -aG sudo alice</code> do?</summary>

Adds the user `alice` to the `sudo` group (`-aG` = append to group). After this, alice can run commands with `sudo`.
</details>

## Next Steps

- Practice installing/uninstalling a package (nginx is good)
- Try `systemctl` on any service you run
- Check your logs — see what's happening on your system
- You've now covered the essential Linux skills for day-to-day server work — practice by managing a real or VM server
- Review the [Linux Command Cheatsheet](../reference/0001-command-cheatsheet.md) and [Glossary](../GLOSSARY.md) to solidify

> [!NOTE] Ask followup questions!
> Package won't install? Service won't start? Ask your teacher.

---

*Reference: [Linux Command Cheatsheet](../reference/0001-command-cheatsheet.md)*
