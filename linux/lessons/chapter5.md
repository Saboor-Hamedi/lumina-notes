# Chapter 5: Networking Essentials

**Chapter 5** · ~30 min · Hands-on

> [!NOTE] Mission tie-in
> Servers exist to serve over networks. Debugging connectivity, downloading files, checking ports — these are daily tasks for anyone managing infrastructure. This lesson gives you the networking toolkit you'll use constantly.

## What You'll Learn

- Download files with `curl` and `wget`
- Test connectivity with `ping` and `ssh`
- Inspect network connections with `ss`
- Check DNS resolution with `nslookup` and `dig`

## Before You Start

These commands reach out to the internet. Make sure you have a working internet connection.

## Downloading Files

### curl — Swiss Army Knife of HTTP

```bash
# Download a URL to stdout
curl https://example.com

# Save to a file
curl -o example.html https://example.com

# Follow redirects
curl -L https://bit.ly/some-link

# Show response headers
curl -I https://example.com

# Download with original filename
curl -O https://example.com/file.zip

# Silent mode (no progress bar)
curl -s https://api.github.com/repos/user/repo

# Combine: silent + follow redirects
curl -sL https://example.com
```

> [!TIP] `curl -s` is your friend in scripts. Without it, curl prints a progress bar that clutters your output.

### wget — Simpler Downloader

```bash
# Download a file
wget https://example.com/file.zip

# Download to a specific name
wget -O output.zip https://example.com/file.zip

# Resume interrupted download
wget -c https://example.com/large-file.zip

# Download entire website (dangerous!)
wget -r https://example.com/
```

## Network Connectivity

### ping — Is It Reachable?

```bash
ping google.com               # Ping until Ctrl+C
ping -c 4 google.com          # Send exactly 4 pings
ping -c 4 -W 2 google.com     # Timeout after 2 seconds
```

`ping` tells you:
- Is the host reachable? (no packet loss = yes)
- How fast is the connection? (lower ms = faster)

```
64 bytes from 142.250.80.46: icmp_seq=1 ttl=118 time=12.3 ms
```

> [!TIP] If `ping` fails, try `ping -c 1 8.8.8.8` (Google DNS). If IP works but hostname doesn't, it's a DNS issue. If neither works, you have no connectivity.

### traceroute — Path to Destination

```bash
traceroute google.com
# Shows every hop between you and the destination
```

### ss — Socket Statistics (Modern netstat)

```bash
ss -tuln                    # Show all listening ports
# -t = TCP, -u = UDP, -l = listening, -n = numeric

ss -tup                     # Show active connections with processes
ss -tuln | grep :80         # Is port 80 listening?
ss -tuln | grep :443        # Is port 443 listening?
```

> [!TIP] `ss -tuln` is your first command when checking if a web server is running. You should see `:80` (HTTP) or `:443` (HTTPS) if a web server is up.

### Checking a Port is Open

```bash
# Using curl
curl -v telnet://example.com:80

# Using /dev/tcp (bash built-in)
echo >/dev/tcp/example.com/80 && echo "open" || echo "closed"
```

## DNS Resolution

```bash
# Check what IP a domain resolves to
nslookup google.com

# More detailed DNS info (if installed)
dig google.com
dig +short google.com        # Just the IP

# Check specific DNS server
nslookup google.com 8.8.8.8  # Query Google's DNS
```

## SSH — Remote Access

```bash
# Connect to a server
ssh user@192.168.1.100

# Connect on a non-default port
ssh -p 2222 user@example.com

# Run a single command and exit
ssh user@example.com "uptime"

# Copy files over SSH
scp file.txt user@example.com:/remote/path/
scp -r docs/ user@example.com:/remote/path/

# Copy from remote to local
scp user@example.com:/remote/file.txt .
```

> [!TIP] If SSH is slow to connect, it's often a DNS issue on the server side. Add `UseDNS no` to `/etc/ssh/sshd_config` or connect with `-o UseDNS=no`.

## Hands-On Exercise

```bash
# 1. Check your internet connection
ping -c 4 8.8.8.8

# 2. Check DNS resolution
nslookup google.com

# 3. Download something
curl -sI https://example.com
curl -o test.html https://example.com

# 4. Check what ports are listening on your system
ss -tuln

# 5. Check your public IP
curl -s https://ifconfig.me

# 6. Trace a route
traceroute google.com

# Cleanup
rm test.html
```

## Self-Quiz

<details>
<summary><strong>1.</strong> How do you check if a web server is running on a remote machine?</summary>

`curl -I http://remote-ip` — if it returns HTTP headers (especially a `200 OK`), the web server is running. Or use `ssh remote "ss -tuln | grep :80"`.
</details>

<details>
<summary><strong>2.</strong> What's the difference between <code>ping google.com</code> failing and <code>ping 8.8.8.8</code> failing?</summary>

If `ping google.com` fails but `ping 8.8.8.8` works, you have a DNS problem. If both fail, you have no network connectivity.
</details>

<details>
<summary><strong>3.</strong> How do you copy a file from your local machine to a remote server?</summary>

`scp localfile.txt user@server:/remote/path/` — copies over SSH. Add `-r` for directories.
</details>

<details>
<summary><strong>4.</strong> What command shows all ports currently listening on your system?</summary>

`ss -tuln` — shows listening TCP (`-t`) and UDP (`-u`) ports with numeric addresses (`-n`). Look for lines in LISTEN state.
</details>

## Next Steps

- Try `curl` against a public API (e.g., `curl -s https://api.github.com/users/octocat`)
- Check what ports are open on your own machine
- Practice `scp` by copying a file between two directories (simulating remote)
- Move to [Chapter 6: Shell Scripting](chapter6.md)

> [!NOTE] Ask followup questions!
> Can't connect somewhere? Getting weird DNS results? Ask your teacher.

---

*Reference: [Linux Command Cheatsheet](reference/0001-command-cheatsheet.md)*
