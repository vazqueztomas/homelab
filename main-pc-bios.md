# Main PC: BIOS preparation for Proxmox VE

Hardware: Ryzen 7 5700G, 16 GB RAM, 480 GB SSD, RTX 4060 Ti 8 GB, ASUS motherboard (UEFI BIOS 3607).

## Display output

The 5700G has integrated graphics, so the monitor goes on the **motherboard HDMI port**, not the graphics card. This leaves the RTX 4060 Ti free to be passed through to a VM later.

Symptom I hit: video output was intermittent at boot. Likely cause: the board sometimes picked the dedicated GPU as primary output while the cable was on the motherboard.

## BIOS settings (Advanced Mode, F7)

| Setting | Value | Why |
|---------|-------|-----|
| SVM Mode | Enabled | CPU virtualization, required to run VMs |
| IOMMU | Enabled | Needed for GPU passthrough |
| Primary Video Device | IGFX Video | Use the iGPU for the host display |
| Integrated Graphics | Force | Keep the iGPU on even with a dedicated GPU installed |
| Fast Boot | Disabled | Always show the boot screen / reach the BIOS |

Tip: ASUS BIOS has a search function (F9); useful because option locations vary by board.

## Bottlenecks to address

- **RAM:** 16 GB will run out quickly with a multi-node Kubernetes cluster. Upgrade to 32-64 GB DDR4 (the 5700G supports up to 128 GB).
- **Storage:** add a second disk for data and backups.
- **Power:** the machine will be on 24/7; measure idle consumption.

## Installation prerequisites (pending)

- USB drive (8 GB or more) with the Proxmox VE ISO written with balenaEtcher in DD mode
- Ethernet cable (Proxmox does not work well over Wi-Fi, and the installer does not support it)
- Static IP planned for the server on the LAN
