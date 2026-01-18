# Fedora Workstation 43 — Complete System Setup Log

User: rahulbagul  
Install Type: Clean Install (NVMe SSD + HDD)  
Desktop: GNOME (Wayland)  
Shell: bash  
Date: Jan 2026  
Status: **STABLE · VERIFIED · PRODUCTION READY**

=========================================================

## BASE SYSTEM
- Fedora Workstation 43 (Clean Install)
- GNOME Desktop on Wayland
- Default shell: bash
- No zsh / no oh-my-zsh
- No PATH modifications

**Goal:**  
Stable, predictable, professional Linux workstation.

=========================================================

## STORAGE SETUP (HDD AUTO-MOUNT)

### Disk Layout
**SSD — System Disk**  
/dev/nvme0n1  
├─ nvme0n1p1 → EFI  
├─ nvme0n1p2 → /boot  
└─ nvme0n1p3 → / (root + home)

**HDD — Data Disk**  
/dev/sda  
├─ sda1 → ext4 → LABEL=Code → UUID=9cdd6ed9-d124-4a0a-9ea2-eb9aefde82f8  
├─ sda2 → ext4 → LABEL=Temp → UUID=5858e361-d76f-4fde-ae7f-93c025274dd7  
└─ sda3 → ext4 → LABEL=DATA → UUID=9c8f550a-435c-4897-9db7-8d5925b747bb

### Mount Points
```
sudo mkdir -p /mnt/code
sudo mkdir -p /mnt/temp
sudo mkdir -p /mnt/data
```

### /etc/fstab
```
UUID=9cdd6ed9-d124-4a0a-9ea2-eb9aefde82f8  /mnt/code  ext4  defaults,x-gvfs-show  0  2
UUID=5858e361-d76f-4fde-ae7f-93c025274dd7  /mnt/temp  ext4  defaults,x-gvfs-show  0  2
UUID=9c8f550a-435c-4897-9db7-8d5925b747bb  /mnt/data  ext4  defaults,x-gvfs-show  0  2
```

Apply & permissions:
```
sudo systemctl daemon-reload
sudo mount -a
sudo chown -R rahulbagul:rahulbagul /mnt/code /mnt/temp /mnt/data
```

=========================================================

## DOWNLOADS FOLDER (UPDATED — HDD STORAGE + SAME PATH)

**Requirement:**  
Downloads data physically HDD par store ho, lekin user ko `~/Downloads` hi dikhai de.

**Solution:**  
Bind mount using `/mnt/data/Downloads` → `~/Downloads`

**Why bind mount (not symlink):**
- Applications ko real directory milta hai
- Permissions & sandbox friendly
- GNOME / browsers / Flatpak safe
- Path change nahi hota (`~/Downloads` same)

### Setup Steps (One‑Time)
```
# Create target directory on HDD
mkdir -p /mnt/data/Downloads

# Ensure home Downloads exists
mkdir -p /home/rahulbagul/Downloads
```

### /etc/fstab Entry
```
/mnt/data/Downloads  /home/rahulbagul/Downloads  none  bind,x-gvfs-show  0  0
```

### Apply
```
sudo systemctl daemon-reload
sudo mount -a
```

**Result:**  
- Files physically stored on HDD (`/mnt/data/Downloads`)  
- Visible & usable as `~/Downloads`  
- No symlink, no path breakage

=========================================================

## MULTIMEDIA CODECS (RPM FUSION)
- RPM Fusion enabled
- ffmpeg-free replaced with full ffmpeg
- x264 / x265 / HEVC / HEIF
- GStreamer bad + ugly
- PipeWire aptX

=========================================================

## GNOME EXTENSIONS (USER-INSTALLED)

Enabled via GNOME Extensions app only:
- Blur my Shell
- Clipboard Indicator
- Compiz Alike Magic Lamp Effect
- Coverflow Alt-Tab
- gTile
- Impatience
- Just Perfection
- Native Window Placement
- User Themes

Policy:
- No Flatpak extensions
- Extensions app only

=========================================================

## APPLICATIONS INSTALLED (VERIFIED)

### System / Core
- Fedora Media Writer
- Settings
- GNOME Tweaks
- Extensions
- Boxes
- Maps
- Weather
- Clocks
- Contacts
- Document Viewer

### Development & Networking
- Visual Studio Code
- Postman
- Dev Toolbox
- Git
- GCC / Make
- Htop
- What IP

### Multimedia
- VLC Media Player
- GNOME Video Player
- Camera
- PsTube
- Clapper

### Graphics & Images
- Inkscape
- GIMP
- Upscayl
- Image Viewer

### Office & Documents
- LibreOffice (Writer, Calc, Impress)
- ONLYOFFICE
- PDF Arranger
- Warehouse

### Utilities & Productivity
- AppFlowy
- Apostrophe
- Planify
- Resources
- Save Desktop
- Switcheroo
- Mission Center
- Logged Devices
- Concessio
- Buzz
- Brief
- Pin It!
- Characters
- Gaphor
- Eyedropper

=========================================================

## DEVELOPMENT TOOLS
- VS Code (DNF)
- Git
- GCC / Make
- Brave Browser (DNF)
- No Flatpak dev tools

=========================================================

## NODE.JS ENVIRONMENT
- System-wide Node.js v22.x
- Installed via Fedora repos
- No NVM
- Clean PATH

=========================================================

## SHELL PROMPT (STARSHIP)
- Installed via COPR (atim/starship)
- Binary: /usr/bin/starship
- Config: ~/.config/starship.toml
- No PATH hacks

=========================================================

## DISPLAY POWER BEHAVIOR
```
gsettings set org.gnome.desktop.session idle-delay 0
```
- No auto screen-off

=========================================================

## SECURITY & FIREWALL
```
sudo systemctl enable --now firewalld
firewall-cmd --state
```

=========================================================

## SYSTEM SNAPSHOTS (TIMESHIFT)
- Mode: RSYNC
- /mnt excluded
- Restore SELinux fix:
```
touch /.autorelabel
```

=========================================================

## BACKUP STRATEGY
```
rsync -aAXHv --delete /home/rahulbagul /mnt/data/backup/home
```

=========================================================

## WI-FI 5 GHz LOCK
```
nmcli con modify "SSID" wifi.band a
nmcli con modify "SSID" wifi.bssid AA:CA:E7:B8:E9:92
nmcli con down "SSID"
nmcli con up "SSID"
```
Verify:
```
iw dev wlp5s0 link
```

=========================================================

## MAINTENANCE COMMANDS
```
sudo dnf upgrade --refresh
sudo dnf autoremove
sudo dnf clean packages
sudo journalctl --vacuum-time=7d
```

=========================================================

## FINAL STATUS
✔ Clean Fedora 43 install  
✔ Stable storage layout  
✔ Verified apps & extensions  
✔ GNOME tuned & locked  
✔ Production-ready system

---------------------------------------------------------
Document Generated: $(date)  
Hostname: $(hostname)  
Kernel: $(uname -r)  
Uptime: $(uptime -p)

