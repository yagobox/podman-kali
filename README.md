# podman-kali

A comprehensive reference for running **Kali Linux** inside a [Podman](https://podman.io/) container, with hands-on guides for the most commonly used penetration testing tools.

Targeted at security researchers, penetration testers, and students who want an isolated, rootless Kali Linux environment — without the overhead of a full virtual machine.

> **Legal notice:** All tools and techniques documented here must be used exclusively on systems and networks for which you have explicit written authorization. Unauthorized use is illegal.

---

## Requirements

- [Podman](https://podman.io/docs/installation) installed
- **macOS only:** Podman Machine initialized (see setup below)
- At least 4 GB of free disk space for the Kali image

---

## Podman Setup

### macOS — First-time initialization

```bash
# Initialize and start the Podman virtual machine
podman machine init
podman machine start

# Verify it's running
podman machine list
```

### Pull the official Kali Linux image

```bash
podman pull kalilinux/kali-rolling

# Verify the image was downloaded
podman images
```

---

## Starting Kali Linux

### Basic interactive session

```bash
podman run -it --name kali kalilinux/kali-rolling /bin/bash
```

### With shared folder (access host files from Kali)

```bash
podman run -it --name kali -v ~/kali-shared:/root/shared kalilinux/kali-rolling /bin/bash
```

### With host networking (access LAN directly)

```bash
podman run -it --name kali --network host kalilinux/kali-rolling /bin/bash
```

### Full setup — shared folder + host network + privileged mode

```bash
podman run -it --name kali \
  --network host \
  --privileged \
  -v ~/kali-shared:/root/shared \
  kalilinux/kali-rolling /bin/bash
```

---

## Managing the Container

| Action | Command |
|---|---|
| Re-enter a running container | `podman exec -it kali /bin/bash` |
| Start a stopped container | `podman start kali` |
| Stop the container | `podman stop kali` |
| Restart the container | `podman restart kali` |
| Remove the container | `podman rm kali` |
| Force remove (even if running) | `podman rm -f kali` |
| List all containers | `podman ps -a` |
| View container logs | `podman logs kali` |
| Inspect container config | `podman inspect kali` |

### Update Kali and install tools (inside the container)

```bash
apt update && apt upgrade -y
apt install -y kali-linux-headless
```

---

## Troubleshooting Common Errors

### Container already active in another session
**Error:** `Error: container kali already exists` or `container is already running`

```bash
# Option 1 — Re-attach to the existing session
podman exec -it kali /bin/bash

# Option 2 — Remove and recreate
podman rm -f kali
podman run -it --name kali kalilinux/kali-rolling /bin/bash
```

### Container in "exited" state
**Error:** `Error: container kali is not running`

```bash
podman start kali
podman exec -it kali /bin/bash
```

### Image not found locally
**Error:** `Error: image not known` or `unable to find image`

```bash
podman pull kalilinux/kali-rolling
```

### Container name conflict
**Error:** `Error: container name "kali" is already in use`

```bash
# Remove the old container and recreate
podman rm kali
podman run -it --name kali kalilinux/kali-rolling /bin/bash
```

### Permission denied (rootless Podman)
**Error:** `permission denied` or `cannot create /proc/...`

```bash
# Run with privileged flag
podman run -it --name kali --privileged kalilinux/kali-rolling /bin/bash
```

### Port already in use
**Error:** `Error: address already in use`

```bash
# Find and kill the process occupying the port
lsof -i :8080
kill -9 <PID>

# Or use a different port
podman run -it --name kali -p 9090:80 kalilinux/kali-rolling /bin/bash
```

### Podman socket not available (Linux)
**Error:** `Cannot connect to Podman. Did you start the service?`

```bash
systemctl --user start podman.socket
```

### Podman Machine not initialized (macOS)
**Error:** `Error: no such Podman machine`

```bash
podman machine init
podman machine start
```

---

## Useful Container Commands

```bash
# Save a snapshot of the container as a new image
podman commit kali kali-custom

# Export image to a file
podman save -o kali-custom.tar kali-custom

# Load image from file
podman load -i kali-custom.tar

# Remove all unused images
podman image prune -a

# Remove all stopped containers
podman container prune
```

---

## Tool Guides

Each guide covers installation, core commands, common use cases, and complete lab examples.

### Infrastructure & Network

| Guide | Description |
|---|---|
| [kali-podman.md](kali-podman.md) | Full Podman + Kali Linux command reference and error troubleshooting |
| [nmap-guide.md](nmap-guide.md) | Host discovery, port scanning, OS/version detection, NSE scripts, firewall evasion |
| [netcat-guide.md](netcat-guide.md) | TCP/UDP connections, listeners, file transfer, reverse/bind shells, port scanning |
| [wireshark-guide.md](wireshark-guide.md) | Packet capture, BPF/display filters, traffic analysis, TShark CLI, export |
| [aircrack-guide.md](aircrack-guide.md) | Wi-Fi monitor mode, WPA2 handshake capture, WEP/WPA cracking, PMKID attack |

### Web Application Testing

| Guide | Description |
|---|---|
| [burpsuite-guide.md](burpsuite-guide.md) | Proxy, Repeater, Intruder, Scanner, Decoder, extensions, full testing workflow |
| [nikto-guide.md](nikto-guide.md) | Web server vulnerability scanning, plugins, tuning categories, IDS evasion |
| [gobuster-guide.md](gobuster-guide.md) | Directory/file brute force, DNS subdomain enumeration, vhost and fuzz modes |
| [sqlmap-guide.md](sqlmap-guide.md) | SQL injection detection, database enumeration, data dump, OS access, WAF evasion |

### Exploitation & Post-Exploitation

| Guide | Description |
|---|---|
| [metasploit-guide.md](metasploit-guide.md) | Exploit framework, Meterpreter, auxiliary modules, msfvenom payloads, session management |
| [hydra-guide.md](hydra-guide.md) | Online password cracking: SSH, FTP, HTTP, RDP, SMB, databases, email protocols |

### Password Cracking

| Guide | Description |
|---|---|
| [john-guide.md](john-guide.md) | Hash cracking, wordlist/brute force/single modes, file hash extraction (ZIP, SSH, PDF, Office) |
| [hashcat-guide.md](hashcat-guide.md) | GPU-accelerated cracking, mask attacks, hybrid modes, rules, WPA2/NTLM/bcrypt |

---

## License

This project is released under the [MIT License](https://opensource.org/licenses/MIT).
