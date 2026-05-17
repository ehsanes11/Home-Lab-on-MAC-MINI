# Home-Lab-on-MAC-MINI

Documentation and configuration files for my home lab setup, focusing on Proxmox virtualization, Ubuntu Server management, and Docker containers. This repository serves as a central hub for my technical projects and certification studies.

## 🛠 Hardware Stack

* **Server:** Apple Mac Mini (Late 2014)
* **Storage:** Local SSD + 2TB SSD

## 🌐 Software & Services

The following services are currently deployed and managed within this lab:

* **Hypervisor (Bare Metal):** Proxmox VE
* **Guest OS / Containers:** Ubuntu Server (VM) / Docker & Docker Compose
* **Network Management:**  VPN 
* **DNS & Security:** Cloudflare integration for domain management

## 📚 Certification & Study Goals

This lab is a hands-on environment for mastering the following objectives:

- [x] **Hypervisor Setup:** Installing and configuring Proxmox VE on Mac Mini hardware.
- [ ] **CompTIA Infrastructure:** Studying for A+, Network+, and Security+ exams.
- [ ] **Linux Administration:** Deploying services and managing Docker containers.




after install the proxmox


---## 💾 Proxmox VE Storage Allocation Guide

When installing Proxmox VE on the Mac Mini hardware, use the following advanced storage options to optimize the 2TB SSD efficiently. This ensures the host system remains stable while leaving maximum space for virtual machines and containers.

### 🔹 Recommended Advanced Disk Options

* **Filesystem:** `ext4` or `XFS` (Standard for single-drive setups).
* **hdsize:** `2000` (Leave blank or set to total capacity to utilize the entire 2TB drive).
* **swapsize:** `8` (Allocates 8GB for virtual memory, which is ideal for the Mac Mini's RAM capacity).
* **maxroot:** `50` (Allocates 50GB for the root OS partition. This is more than enough for Proxmox system files and update logs, preventing it from wasting space).
* **minfree:** `16` (Keeps 16GB of space completely free outside the LVM. This is required for safety and smooth LVM snapshot operations).
* **maxvz:** Leave at default (The installer will automatically calculate and allocate all the remaining space to `local-lvm`, which is where your Ubuntu VM and Docker containers will live).

---

### 🛠️ Post-Installation Access
1. **Web GUI:** Once the installation finishes, reboot the Mac Mini and access the dashboard from your MacBook's browser at `https://<YOUR_MAC_MINI_IP>:8006`.

## 🌐 Networking & IP Configuration Guide

During the Proxmox installation, you will be prompted to configure the management network interface. Here is how to set up the IPs correctly:

### 🔹 1. Management Interface (Network Device)
* Select the primary Ethernet port of the Mac Mini (usually starts with `enp...` or `eth0`). 
* *Note:* Avoid using Wi-Fi during the initial host installation as Proxmox bridges require a stable wired connection.

### 🔹 2. Hostname (FQDN)
* Set a local fully qualified domain name, for example: `pve.homelab.local` or `node1.yourdomain.com`.
you can buy a domain with 2 $ 
### 🔹 3. IP Address (CIDR Notation)
* This is the static IP you will assign to your Proxmox Host. It must belong to your local router's subnet but outside its DHCP pool.
* **Format:** `192.168.1.X/24` (Replace `X` with your chosen number, e.g., `192.168.1.50`). The `/24` represents the Subnet Mask (`255.255.255.0`).

### 🔹 4. Gateway
* This is the local IP address of your home router.
* **Format:** Typically `192.168.1.1` or `192.168.0.1`.

### 🔹 5. DNS Server
* The Domain Name System server used to resolve internet addresses.
* **Recommendation:** You can use your router's IP (`192.168.1.1`) or fast public DNS servers like Cloudflare (`1.1.1.1`) or Google (`8.8.8.8`).

--3. **Verify IP Address:** If you are unsure of the assigned IP after installation, log into the Mac Mini console and run:
   ```bash
   ip a

Note on SSL Warning: Since Proxmox uses a self-signed certificate by default, your browser will display a "Your connection is not private" or "Potential Security Risk" warning on your first login. This is perfectly normal and expected for a local home lab environment. To bypass this, click on Advanced and select Proceed to <your_ip> (unsafe) to load the login page.
then you will see the error   You do not have a valid subscription for this server. Please visit www.proxmox.com to get a list of available options.

Error: command 'apt-get update' failed: exit code 100.

This is a classic Proxmox situation. By default, Proxmox comes pre-configured with the Enterprise Repository, which requires a paid subscription. Since you are likely running a community or Home Lab setup, apt-get is hitting a wall trying to access that locked repository, resulting in that exit code 100.

You need to tell Proxmox to stop trying to use the subscription-only repo.
In the Proxmox web UI, look at the left-hand sidebar (Server View).

Click directly on your node named .

In the middle menu column that appears, scroll down and click on Repositories.

Look at the list on the right. Select the line that mentions pve-enterprise.

Click the Disable button at the top of that section.

Step 2: Add the No-Subscription Repository
Now, let's add the free community repository so you can actually get updates.

While still in that same Repositories view, click the Add button.

A warning about subscriptions might pop up; you can safely click OK / ignore it.

In the dropdown menu for Repository, select No-Subscription.

Click Add.


### Fix Proxmox VE Subscription Repository Error

**Issue:** When checking for system updates, the task fails with the following error:
`Error: command 'apt-get update' failed: exit code 100`

**Cause:** By default, Proxmox VE is configured with the Enterprise Repository, which requires a paid subscription. Without a valid license, `apt-get` cannot access this repository.

**Resolution:** To resolve this, switch from the enterprise repository to the community-supported, free repository:

1. **Navigate to Repositories:**
   * In the Proxmox Web UI, select your node .
   * Go to **System** -> **Repositories**.

2. **Add the Free Repository:**
   * Click on the **Add** button at the top of the *APT Repositories* section.
   * Disregard the subscription warning popup.
   * In the *Repository* dropdown menu, select **No-Subscription**.
   * Click **Add**.

3. **Disable the Enterprise Repositories (Optional):**
   * Select the commercial Ceph or PVE enterprise components (e.g., `ceph-squid ... enterprise`).
   * Click the **Disable** button at the top to prevent future access errors.

4. **Verify the Fix:**
   * Go to the **Updates** tab under your node.
   * Click **Refresh**.
   * The package database will now update successfully, returning a **`TASK OK`** status.
then click on the upgrade to install


connect to clouflare...

### Troubleshooting: Missing LXC Templates in Proxmox VE

**Issue:** During the LXC container creation wizard, the *Template* field is empty, throwing a validation error: `Input error. This field is required.`

**Cause:** The hypervisor's local storage does not yet contain any pre-downloaded Linux distribution rootfs templates.

**Resolution:** Download a minimal Debian template before proceeding with the container setup:

1. **Download the Template:**
   * Navigate to **Main Menu** -> **ahmagh** -> **local** storage.
   * Select **CT Templates** from the inner menu, then click the **Templates** button at the top.
   * Search for `debian-12-standard` (or the latest stable release), select it, and click **Download**.
   * Wait for the task log to output **`TASK OK`**.

2. **Resume Container Creation:**
   * Relaunch the **Create CT** wizard.
   * Under the **Template** tab, select `local` as the storage provider, and the newly downloaded Debian rootfs template will now be available for selection.
📅 **Last Updated:** May 2026



