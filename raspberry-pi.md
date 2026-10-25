# Raspberry Pi 3 Model B+ setup

Auxiliary node of the homelab. It keeps running RetroPie, so the OS was not reinstalled; everything below was done on top of the existing system.

- **Hardware:** Raspberry Pi 3 Model B+ (1 GB RAM), 16 GB microSD
- **OS:** Raspbian 10 (buster), 32-bit, RetroPie 4.x (end of life, no security updates)
- **LAN IP:** 192.168.1.41 (Ethernet)

## 1. Backup before touching anything

The microSD is the only copy of the RetroPie setup, so I backed up the important parts first. A full `dd` image of the card over SSH was too slow on a Pi 3B+ (SSH encryption + gzip on a small CPU), so I cancelled it and copied only what matters:

```bash
scp -r pi@192.168.1.41:/opt/retropie/configs ./backup-configs
scp -r pi@192.168.1.41:/home/pi/RetroPie/roms ./backup-roms
scp -r pi@192.168.1.41:/home/pi/RetroPie/BIOS ./backup-bios
```

Backups are kept **outside** the repo (or ignored via `.gitignore`): ROMs/BIOS are copyrighted and configs may hold secrets.

## 2. Fix the apt repositories (buster is archived)

The default Raspbian mirror no longer serves buster (404). Pointing apt to the legacy mirror fixes it:

```bash
sudo sed -i 's|raspbian.raspberrypi.org|legacy.raspbian.org|' /etc/apt/sources.list
sudo apt update
```

This only makes `apt install` work again. The base system is still old, so no `apt full-upgrade`, and the Pi must not be exposed to the internet.

## 3. SSH key authentication

On the Mac:

```bash
ssh-keygen -t ed25519 -C "homelab"
ssh-copy-id pi@192.168.1.41
```

Verified key login in a second terminal. Password login is still enabled; it will be disabled after everything is tested, keeping a second session open as a safety net.

## 4. Tailscale

Private mesh network: reach the Pi from anywhere without opening router ports.

```bash
curl -fsSL https://pkgs.tailscale.com/stable/raspbian/buster.noarmor.gpg | sudo tee /usr/share/keyrings/tailscale-archive-keyring.gpg >/dev/null
curl -fsSL https://pkgs.tailscale.com/stable/raspbian/buster.tailscale-keyring.list | sudo tee /etc/apt/sources.list.d/tailscale.list
sudo apt update
sudo apt install tailscale
sudo tailscale up
tailscale ip -4
```

Tailscale does not officially test on buster, but the repository still works.

## 5. node_exporter (metrics)

```bash
sudo apt install prometheus-node-exporter
systemctl status prometheus-node-exporter
```

Metrics are served at `http://<pi-ip>:9100/metrics`. Useful ones: `node_thermal_zone_temp`, `node_memory_MemAvailable_bytes`, `node_filesystem_avail_bytes`, `node_load1`. Prometheus + Grafana will scrape this once the main server is up.

## What NOT to run on this Pi (for now)

- **Docker:** unsupported on buster, installation tends to break.
- **Pi-hole:** if the Pi is rebooted for gaming, DNS for the whole house goes down.
- **Kubernetes:** 1 GB RAM and 32-bit are not enough.

## Next steps

- Firewall with `ufw` (SSH, Samba, metrics only from Tailscale)
- Disable SSH password login
- Reinstall with Raspberry Pi OS Lite 64-bit once a microSD reader is available, and re-apply this setup with an Ansible playbook
