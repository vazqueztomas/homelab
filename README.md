# Homelab

A personal homelab built to learn DevOps / Platform Engineering / SRE skills hands-on, and to see how far it can scale. Everything is documented here: what I built, why, and what broke along the way.

## Hardware

| Node | Specs | Role | Status |
|------|-------|------|--------|
| Main PC | Ryzen 7 5700G, 16 GB RAM, 480 GB SSD, RTX 4060 Ti 8 GB, XPG Pylon 750W | Future Proxmox VE host (VMs, Kubernetes, GPU workloads) | BIOS prepared, waiting for USB drive + Ethernet cable |
| Raspberry Pi 3 Model B+ | 1 GB RAM, 16 GB microSD, Raspbian 10 (buster) 32-bit, RetroPie | Auxiliary node: remote access + monitoring (still runs RetroPie) | Online |

## Roadmap

- [x] Prepare BIOS on the main PC (SVM, IOMMU, iGPU as primary display)
- [x] Raspberry Pi: SSH key authentication
- [x] Raspberry Pi: Tailscale (remote access without opening router ports)
- [x] Raspberry Pi: node_exporter (metrics)
- [ ] Raspberry Pi: firewall (ufw), disable SSH password login
- [ ] Main PC: install Proxmox VE
- [ ] Reinstall the Pi with Raspberry Pi OS Lite 64-bit (needs a microSD reader)
- [ ] Infrastructure as code: Terraform (Proxmox provider) + Ansible
- [ ] Kubernetes (k3s) with GitOps (ArgoCD)
- [ ] Observability: Prometheus, Grafana, Loki, alerting
- [ ] GPU passthrough: run local LLM / ML workloads (Ollama) on the RTX 4060 Ti
- [ ] Scale out with extra mini PCs for a multi-node, highly available cluster

## Documentation

- [Main PC BIOS preparation](docs/main-pc-bios.md)
- [Raspberry Pi setup](docs/raspberry-pi.md)
- [Lessons learned](docs/lessons-learned.md)

## Repository conventions

- Only code, scripts and documentation live here.
- No secrets, no keys, no backups, no ROMs/BIOS files (see `.gitignore`).
