# Udroid Ubuntu Installer & Fixer for Android

Run full Ubuntu Desktop (XFCE/GNOME) on Android using Termux and Udroid. This repository provides scripts to automate the setup and fix common audio/display issues, but also includes full manual instructions below.

## 🚀 Features
*   **One-Click Setup:** Installs dependencies (X11, Audio) automatically.
*   **Auto-Fix Script:** Clears "Access Denied" or "Display Locked" errors.
*   **Audio Support:** Enables PulseAudio for sound in Linux (fixes Firefox audio).
*   **No Root Required.**

## 📚 Official Documentation
This guide is based on the official Udroid documentation. If you encounter any issues not covered here, please check the official guide:
[**https://docs.udroid.org/**](https://docs.udroid.org/)

## 📥 Prerequisites
Before you begin, you must install these two apps on your Android device:
1.  **Termux** (Download from [F-Droid](https://f-droid.org/en/packages/com.termux/)) - *Do not use the Play Store version.*
2.  **Termux-X11** (Download from [GitHub Actions](https://github.com/termux/termux-x11/actions)) - *Install the debug APK.*

---

## ⚡ Option 1: Automatic Setup (Easy)

If you just want to get it running quickly, use the included scripts.

1.  Open Termux and clone this repo:
    ```bash
    pkg install git -y
    git clone https://github.com/jarvesusaram99/ubuntu-on-android.git
    cd ubuntu-on-android
    chmod +x setup.sh launch.sh
    ```

2.  Run the installer:
    ```bash
    ./setup.sh
    ```

3.  **To Start Ubuntu (Every time):**
    Run the launch script. It automatically fixes audio and display locks.
    ```bash
    ./launch.sh
    ```

---

## 🛠️ Option 2: Manual Installation (Step-by-Step)

If you prefer to run the commands yourself, follow these steps exactly.

### Step 1: Install Dependencies
Open Termux and run:
```bash
pkg update && pkg upgrade -y
pkg install x11-repo -y
pkg install termux-x11-nightly -y
pkg install proot pulseaudio -y
```

### Step 2: Install Udroid (Ubuntu)
Run the official installer:
```bash
. <(curl -Ls https://bit.ly/udroid-installer)
```
*   Select `install` -> `jammy` (Ubuntu 22.04) -> `xfce4` (Recommended) or `gnome`.

---

## 🖥️ Manual Start & Fix Guide (Run Every Time)

If you are **not** using the `launch.sh` script, you must run these commands manually every time you want to use Linux. This ensures audio works and the display doesn't crash.

### 1. Kill Stuck Processes (The Fix)
First, clear any old sessions to avoid "Display :0 is already in use" errors:
```bash
killall -9 termux-x11
rm -rf /tmp/.X11-unix
rm -rf /tmp/.X0-lock
```

### 2. Start Audio Server
Enable sound support (PulseAudio) before starting the desktop:
```bash
export LD_PRELOAD=/system/lib64/libskcodec.so
pulseaudio --start --load="module-native-protocol-tcp auth-ip-acl=127.0.0.1 auth-anonymous=1" --exit-idle-time=-1
```

### 3. Start Display Server
Start the X11 display in the background:
```bash
termux-x11 :0 &
```
*(Make sure you have opened the **Termux:X11** app in the background first!)*

### 4. Login to Ubuntu
Log into your installed system:
```bash
udroid login jammy:xfce4
```
*(Use `jammy:gnome` if you installed GNOME)*

### 5. Start the Desktop (Inside Ubuntu)
Once you see the `root@localhost` prompt, run:
```bash
export DISPLAY=:0
export PULSE_SERVER=127.0.0.1
startxfce4
```

---

## 🔊 Fix Firefox Audio (Important)

If you have no sound in Firefox:
1.  Open Firefox inside Ubuntu.
2.  Type `about:config` in the address bar.
3.  Search for: `security.sandbox.content.level`
4.  Change the value to `0`.
5.  Restart Firefox.

---

## ❓ Troubleshooting

*   **Black Screen?**
    Make sure `termux-x11` app is open. Run the "Kill Stuck Processes" commands and try again.
*   **Audio not working?**
    Ensure you ran the `pulseaudio` command *before* logging into Udroid.
