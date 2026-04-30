# ArChromebook

Welcome to the Arch (btw) life, but with one major limit. You only have a stinky Chromebook with 4GB of RAM and a small eMMC drive. Half the time, ChromeOS is way too big (especially with those heavy Linux apps), and we want more. That’s where we come in—this is the home for installing Arch on a real (x86_64) Chromebook!

## Phase 1: Preparation and Firmware

You can't just slap Arch on a stinky Chromebook without fixing the firmware first. ChromeOS locks everything down, so we have to break it open.

### 1. Developer Mode
Turn off your Chromebook. Hold `Esc + Refresh` and tap the `Power` button. When you see the recovery screen, press `Ctrl + D`. This will wipe your data, but you're a pro, so you probably already backed up your Python scripts.

### 2. The Firmware Script
Boot into ChromeOS, open a terminal (`Ctrl+Alt+T`), and type `shell`. Now run the MrChromebox script to give your laptop a "brain":

```bash
curl -LO mrchromebox.tech/firmware-util.sh && sudo bash firmware-util.sh
```

### 3. Choosing your Path
- **RW_LEGACY**: Use this if you want to keep ChromeOS and boot Arch from an SD card.
- **Full ROM (UEFI)**: Use this to wipe ChromeOS forever and make your Chromebook act like a normal laptop.

## Phase 2: The Base Installation

Boot your Arch USB. Since we only have 4GB of RAM, we need to keep this install lean. No bloat allowed.

### 1. Networking
Use `iwctl` to connect. If you’re at home, it’s probably:

```bash
iwctl
device list
station wlan0 scan
station wlan0 connect YourSSID
```

### 2. Partitioning the stinky eMMC
Use `fdisk /dev/mmcblk0`. Since space is tight:
- 512MB EFI partition (Type: EFI System)
- The rest for your Root (Type: Linux Filesystem)

### 3. Pacstrap
Include the AMD microcode for your A4 chip so it doesn't freak out:

```bash
pacstrap /mnt base linux linux-firmware amd-ucode
```

## Phase 3: Solving the Black Screen

If you reboot and the screen stays black, don't panic. It's just the kernel fighting with your AMD A4 chip. 

### 1. Edit GRUB
Open your config: `nano /etc/default/grub`

### 2. Add the Fix
Find `GRUB_CMDLINE_LINUX_DEFAULT` and add `amdgpu.dc=0`. It should look like this:

```text
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash amdgpu.dc=0"
```

### 3. Update
Save it and run:

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

## Phase 4: Desktop and Aesthetics

You only have 4GB of RAM. Don't install GNOME. It will be slow and you'll hate it. 

### 1. The "Light" Life
We recommend **Xfce** or **LXQt**. They are the only ones that respect your hardware.

### 2. Terminal and Fonts
Install **JetBrains Mono** so your code actually looks good:

```bash
sudo pacman -S ttf-jetbrains-mono
```

### 3. ZRAM Setup (Mandatory!)
Since your 4GB of RAM is stinky, you **must** use ZRAM. It compresses your RAM so you can actually open more than three tabs in a browser without the system freezing.

```bash
sudo pacman -S zram-generator
```
