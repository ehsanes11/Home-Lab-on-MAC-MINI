# Ubuntu Server Installation on Mac Mini

This guide documents the step-by-step process of installing Ubuntu Server on Apple Mac Mini hardware.

## 1. Prerequisites
* **Hardware:** Mac Mini
* **Storage:** USB Flash Drive (8GB+)
* **OS Image:** Ubuntu 26.04 LTS (or your preferred version)
* **Tools:** BalenaEtcher or Rufus (to create the bootable drive)

## 2. Preparing the Bootable USB
1. Download the [Ubuntu Server ISO](https://ubuntu.com/download/server).
2. Flash the ISO to the USB drive using BalenaEtcher.

## 3. Installation Steps
1. Insert the USB into the Mac Mini.
2. Power on the Mac Mini and immediately press and hold the **Option (Alt) ⌥** key.
3. Select the **EFI Boot** (USB icon) from the boot menu.
4. Follow the Ubuntu installer prompts:
   * Select Language and Keyboard layout.
   * Connect to the network (use Ethernet for a more stable installation).
   * **Storage:** Select "Use an entire disk" (Warning: This wipes macOS).
5. Create your user account (e.g., ``) and set a strong password.
6. **Important:** Enable "Install OpenSSH Server" during the process.
7. Enabale Ubuntu  pro(optional )

## 4. Post-Installation (WiFi Driver Fix)
Since Mac Minis often use Broadcom chips, you might need to install the driver manually after the first boot:
```bash
sudo apt update
sudo apt install bcmwl-kernel-source
