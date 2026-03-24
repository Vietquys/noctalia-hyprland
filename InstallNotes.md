# README2 - Extra Install Notes

This document is an add-on to the main README. It covers practical setup details users commonly need before and after running the installer.

## ⚠️ Important Notes

- This setup is intended for fresh, vanilla Arch Linux installs.
- Run the installer from your normal user with sudo, not from a root login shell.
- Internet and correct system time are required for package keys, mirrors, and AUR operations.
- The installer modifies files in `.config` and creates timestamped backups when replacing existing configs.

## 🌐 Arch ISO Wi-Fi Setup (Before Archinstall)

If you are booted into the Arch ISO and need wireless internet before running archinstall, use the following flow.

### Step 1: Confirm Your Wireless Interface

```bash
ip link
```

Look for an interface such as `wlan0` or `wlp2s0`.

### Step 2: Unblock Wi-Fi (If Needed)

```bash
rfkill list
rfkill unblock wifi
```

### Step 3: Connect With iwd

```bash
iwctl
```

Inside iwctl:

```bash
device list
station YOUR_INTERFACE scan
station YOUR_INTERFACE get-networks
station YOUR_INTERFACE connect YOUR_SSID
exit
```

### Step 4: Verify Network Access

```bash
ping -c 3 archlinux.org
```

### Step 5: Sync System Time

```bash
timedatectl set-ntp true
timedatectl status
```

### Step 6: Launch Archinstall

```bash
archinstall
```

## ⚙️ Before Running install.sh

- Run from repository root: `sudo ./install.sh`
- Ensure stable internet for pacman and AUR package operations.
- Verify time sync with timedatectl.
- Expect one or more reboot-required changes by design.

## 🔆 DDC / Brightness Behavior (Noctalia)

The installer supports monitor brightness through ddcutil and ddcutil-service.

For brightness control to work:

- Your monitor must support DDC/CI and have it enabled in the monitor OSD.
- I2C devices must be present (`/dev/i2c-*`).
- The user must be in the i2c group.
- Reboot or relogin is required after group changes.

The installer now handles:

- ddcutil installation (pacman)
- ddcutil-service installation (yay/AUR)
- i2c-dev module loading and persistence
- udev reload/trigger for immediate permission refresh
- enabling Noctalia DDC support in Noctalia settings when DDC setup is selected

## ✅ Post-Install Validation

After reboot/login, these commands should work:

```bash
groups | grep i2c
ddcutil detect
ddcutil-client detect
ddcutil-client -d 1 getvcp 0x10
```

If command checks pass but the brightness widget does not move:

- Confirm Noctalia has DDC enabled in settings.
- Restart the shell/bar session.
- Re-check monitor OSD DDC/CI setting.

## 🧭 Current Installer Flow

1. Base package + repo setup
2. Post-install packages and options (Noctalia, browser, DDC)
3. Config deployment
4. Optional backup restore
5. Bookmark + permissions setup
6. Reboot confirmation loop

This order avoids backup/deploy overwrite conflicts and preserves DDC settings after backup restores.

## 🛠️ Common Failure Cases

- Running script directly as root instead of sudo from a normal user
- Network interruptions during pacman/yay operations
- Monitor firmware with DDC/CI disabled
- Assuming ddcutil-service is a systemd unit (it is D-Bus activated)

## 🔁 Final Action

The reboot prompt loops intentionally. Reboot is required so group, module, and configuration changes are fully applied before first normal use.
