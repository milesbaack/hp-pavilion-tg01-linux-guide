# 🐧 Running Linux on HP Pavilion Gaming Desktop TG01 with NVIDIA RTX 3060 🖥️

This guide provides a step-by-step approach to successfully install and run Linux on the HP Pavilion Gaming Desktop TG01 series with NVIDIA RTX 3060 graphics. These systems have specific hardware compatibility issues that require special configuration to work properly with Linux.

## 🔍 Hardware Information

- **Model:** HP Pavilion Gaming Desktop TG01-2xxx
- **GPU:** NVIDIA GeForce RTX 3060 (12GB)
- **Known Issue:** ACPI/APIC compatibility problems causing system instability and crashes

## ✅ Prerequisites

- A USB drive (8GB+) for the installation media
- Backup of important data
- Basic familiarity with Linux command line

## 💿 Installation Guide

### 1. 📦 Prepare Installation Media

1. Download a Linux distribution (Ubuntu recommended for this guide)
2. Create a bootable USB using tools like Rufus (Windows) or Etcher
3. Ensure you have a stable internet connection during installation

### 2. ⚙️ BIOS Configuration

1. Restart your computer and press F10 repeatedly during startup to enter BIOS
2. Navigate to the "Security" tab
3. Disable "Secure Boot"
4. Set boot priority to boot from your USB
5. Save changes and exit

### 3. 🚀 Boot and Install Linux

1. Boot from the USB
2. When you see the GRUB menu, press 'e' to edit boot options
3. Find the line that contains `quiet splash` (may also include `$vt_handoff`)
4. Add `noapic` after these parameters
5. Press F10 to boot with these parameters
6. Proceed with the normal installation

### 4. 🛠️ Post-Installation Configuration

After installing Linux, you'll need to make several critical configuration changes to ensure stability:

#### 4.1 🔧 Make Kernel Parameters Permanent

1. Edit the GRUB configuration:
```bash
sudo nano /etc/default/grub
```

2. Find the line with `GRUB_CMDLINE_LINUX_DEFAULT` and modify it:
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash $vt_handoff noapic irqpoll acpi_osi=Linux"
```

3. Update GRUB:
```bash
sudo update-grub
```

4. Reboot your system:
```bash
sudo reboot
```

#### 4.2 🖥️ Create X.org Configuration for NVIDIA

1. Create a directory for X.org configurations (if it doesn't exist):
```bash
sudo mkdir -p /etc/X11/xorg.conf.d/
```

2. Create a configuration file for NVIDIA:
```bash
sudo nano /etc/X11/xorg.conf.d/20-nvidia.conf
```

3. Add the following content:
```
Section "Device"
    Identifier  "NVIDIA Card"
    Driver      "nvidia"
    Option      "NoLogo" "true"
    Option      "RegistryDwords" "EnableBrightnessControl=1"
    Option      "AccelMethod" "none"
    Option      "IgnoreDisplayDevices" "CRT"
EndSection
```

4. Save and reboot your system

#### 4.3 🎮 Install NVIDIA Drivers

1. Check available NVIDIA drivers:
```bash
sudo ubuntu-drivers list
```

2. Install the recommended driver (usually the highest non-server version):
```bash
sudo apt install nvidia-driver-550  # Use the highest version available
```

3. Reboot your system again:
```bash
sudo reboot
```

4. Verify the driver is working:
```bash
nvidia-smi
```

You should see output showing your RTX 3060 being recognized.

## ⚠️ Troubleshooting

### 💥 If your system still crashes:

1. Try adding just `irqpoll` or `acpi=off` to the kernel parameters instead
2. Check for errors in the system logs:
```bash
journalctl -b -p err..emerg
```
3. Look for NVIDIA-specific issues:
```bash
journalctl -b | grep -i nvidia
```

### 📺 If you experience graphics/display issues:

1. Try a different combination of kernel parameters
2. Consider updating to the latest NVIDIA drivers from the official repository

## 📦 Additional Software

For gaming on your HP Pavilion: 🎮

1. Install Steam (choose one method):

   **Method A: Using the official repository** (recommended for best system integration)
   ```bash
   # Enable 32-bit architecture
   sudo dpkg --add-architecture i386
   sudo apt update

   # Install Steam
   wget https://cdn.cloudflare.steamstatic.com/client/installer/steam.deb
   sudo apt install ./steam.deb
   ```

   **Method B: Using Snap** (easier installation, containerized)
   ```bash
   # Install Steam from Snap store
   sudo snap install steam
   ```

   Note: The Snap version is more isolated from your system and may have slightly different performance characteristics, but is generally easier to install.

2. For better gaming performance:
```bash
sudo apt install gamemode
```

## 🧪 Testing System Stability

You can verify your system's stability by running:

```bash
sudo apt install stress-ng
stress-ng --cpu 4 --vm 2 --hdd 1 --timeout 60s
```

If this test completes without errors or crashes, your system should be stable for daily use and gaming.

## 👥 Contributing

If you've found additional tweaks or configurations that help with the HP Pavilion TG01 series, please submit a pull request or open an issue to share your findings.

