# podman-kali

A practical reference guide for running **Kali Linux** inside a [Podman](https://podman.io/) container on macOS and Linux.

## Purpose

This guide is aimed at security researchers, penetration testers, and developers who want to run Kali Linux in an isolated, rootless container environment using Podman — without the overhead of a full virtual machine.

## What's included

The guide (`kali-podman.md`) covers:

- Pulling the official `kalilinux/kali-rolling` image
- Starting and configuring the container (shared folders, host networking, privileges)
- Managing an existing container (start, stop, restart, remove)
- Monitoring containers (logs, inspect, status)
- Updating Kali Linux and installing tool packages inside the container
- Troubleshooting the most common errors:
  - Container already active in another session
  - Container in "exited" state
  - Image not found locally
  - Container name conflict
  - Permission denied (rootless Podman)
  - Port already in use
  - Podman socket not available
  - Podman Machine not initialized (macOS)
- Useful extras: snapshots, image export/import, cleanup commands

## Requirements

- [Podman](https://podman.io/docs/installation) installed
- On macOS: Podman Machine initialized (`podman machine init && podman machine start`)

## Quick start

```bash
# Pull the image
podman pull kalilinux/kali-rolling

# Start an interactive session
podman run -it --name kali kalilinux/kali-rolling /bin/bash

# Re-enter an existing container
podman exec -it kali /bin/bash
```

## Guides

| File | Description |
|------|-------------|
| [kali-podman.md](kali-podman.md) | Commands to run and manage Kali Linux via Podman, including error troubleshooting |
| [hydra-guide.md](hydra-guide.md) | Hydra password-cracking tool: commands, protocols, brute force patterns, and full test examples |
| [wireshark-guide.md](wireshark-guide.md) | Wireshark & TShark in Kali Linux: packet capture, display filters, traffic analysis, and export |
| [nmap-guide.md](nmap-guide.md) | Nmap: host discovery, port scanning, OS/version detection, NSE scripts, evasion techniques |

## License

This project is released under the [MIT License](https://opensource.org/licenses/MIT).
