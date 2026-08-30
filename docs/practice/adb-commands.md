# ADB Commands Guide:

A comprehensive guide to Android Debug Bridge (ADB) for developers new to the Android ecosystem.

## Table of Contents
1. [Installation & Setup](#installation--setup)
2. [Device Connection](#device-connection)
3. [Essential Commands](#essential-commands)
4. [File Transfer](#file-transfer)
5. [App Management](#app-management)
6. [System Information](#system-information)
7. [Debugging & Logs](#debugging--logs)
8. [Network & Remote Access](#network--remote-access)
9. [Troubleshooting](#troubleshooting)

---

## Installation & Setup

### Linux/Mac
```bash
# Debian/Ubuntu
sudo apt-get install android-tools-adb android-tools-fastboot

# macOS (via Homebrew)
brew install android-platform-tools

# Verify installation
adb version
```

### Windows
1. Download Android SDK Platform Tools from [Google's developer site](https://developer.android.com/studio/releases/platform-tools)
2. Extract to a known location (e.g., `C:\android-sdk\platform-tools`)
3. Add to PATH or use full path when running commands
4. Open PowerShell and verify: `adb version`

### USB Driver Setup (Important for IoT devices)

**Windows:**
- Download appropriate USB drivers from your device manufacturer
- Some devices use generic Android drivers; others have custom drivers

**Linux/Mac:**
- Usually work out-of-the-box
- If issues persist, check device permissions: `sudo usermod -aG plugdev $USER`

**Enable USB Debugging on Device:**
1. Navigate to Settings → About Phone/Device
2. Tap "Build Number" 7 times (may vary by device)
3. Go to Developer Options (now visible in Settings)
4. Enable "USB Debugging"
5. For network debugging: Enable "TCP/IP Debugging" (if available)

---

## Device Connection

### Via USB Cable

```bash
# List all connected devices
adb devices

# Output example:
# List of attached devices
# emulator-5554          device
# 192.168.1.100:5555    device

# Check device status in detail
adb devices -l

# Output with details:
# List of attached devices
# FA7AX1A123          device usb:1-1 product:flame model:Pixel_4a device:flame
```

### Via Network (TCP/IP)

**For IoT devices on the same network:**

```bash
# Enable TCP/IP debugging on device (via USB first)
adb tcpip 5555

# Disconnect USB cable

# Connect via network (use device IP address)
adb connect 192.168.1.100:5555

# Verify connection
adb devices

# To revert to USB mode
adb usb
```

### Finding Device IP Address

```bash
# Method 1: Via ADB (when connected via USB)
adb shell ip addr show

# Method 2: Via ADB shell
adb shell netstat

# Method 3: Check device WiFi settings
# Navigate to Settings → WiFi → (Connected Network) → Properties
```

### Multiple Device Handling

```bash
# When multiple devices are connected, use -s flag to specify device
adb -s <device-id> <command>

# Example:
adb -s 192.168.1.100:5555 shell

# Connect to specific device for all following commands
adb -s FA7AX1A123 install app.apk

# Disconnect specific device
adb disconnect 192.168.1.100:5555

# Disconnect all networked devices
adb disconnect
```

---

## Essential Commands

### Device Connection Management

```bash
# Kill ADB server (useful for resetting connections)
adb kill-server

# Start ADB server
adb start-server

# Restart ADB
adb restart

# Reboot device into normal mode
adb reboot

# Reboot into bootloader/recovery
adb reboot bootloader
adb reboot recovery
```

### Remote Shell Access

```bash
# Open interactive shell on device
adb shell

# Run single command on device
adb shell <command>

# Examples:
adb shell ls /data
adb shell ps                    # Process list
adb shell whoami               # Current user
adb shell getprop ro.build.version.release  # Android version
```

### Push/Pull Files

```bash
# Copy file from PC to device
adb push <local-path> <device-path>
adb push myfile.txt /sdcard/

# Copy file from device to PC
adb pull <device-path> <local-path>
adb pull /data/app/myapp.apk ./

# Copy directory
adb push localdir/ /sdcard/remotedir/
adb pull /sdcard/remotedir/ ./localdir/
```

---

## File Transfer

### Working with File Systems

```bash
# List files on device
adb shell ls -la /sdcard/

# Create directory on device
adb shell mkdir /sdcard/mydir

# Remove file from device
adb shell rm /data/app/myapp.apk

# Check file size
adb shell du -sh /sdcard/

# Copy between device locations
adb shell cp /sdcard/file.txt /sdcard/backup/

# Check disk usage
adb shell df -h
```

### Batch File Operations

```bash
# Upload multiple files
for file in *.apk; do adb push "$file" /sdcard/; done

# Download entire directory with timestamps
adb pull /sdcard/DCIM/ ./phone_photos/

# Sync directory (push changes only)
adb push ./local_data /sdcard/app_data
```

---

## App Management

### Install/Uninstall

```bash
# Install APK
adb install app.apk

# Install with specific options
adb install -r app.apk           # Replace existing
adb install -g app.apk           # Grant permissions
adb install -s app.apk           # Install on SD card

# Uninstall app
adb uninstall com.example.app

# Uninstall but keep data
adb uninstall -k com.example.app

# Get package name from installed apps
adb shell pm list packages | grep -i myapp
```

### App Management

```bash
# Start application
adb shell am start -n com.example.app/.MainActivity

# Stop application
adb shell am force-stop com.example.app

# Get app info
adb shell dumpsys package com.example.app

# List installed apps with paths
adb shell pm list packages -f

# Get app data size
adb shell du -sh /data/data/com.example.app
```

### Working with APK Files

```bash
# Extract APK from device
adb pull /data/app/com.example.app-1/base.apk ./myapp.apk

# Get APK path of installed app
adb shell pm path com.example.app

# List all APK files on device
adb shell find /data/app -name "*.apk"
```

---

## System Information

### Device Properties

```bash
# Get all system properties
adb shell getprop

# Get specific property
adb shell getprop ro.build.version.release    # Android version
adb shell getprop ro.product.manufacturer     # Manufacturer
adb shell getprop ro.product.model            # Device model
adb shell getprop ro.serialno                 # Serial number
adb shell getprop ro.build.fingerprint        # Build fingerprint
```

### Hardware Information

```bash
# CPU info
adb shell cat /proc/cpuinfo

# Memory info
adb shell free -h
adb shell cat /proc/meminfo

# Battery info
adb shell dumpsys battery

# Display info
adb shell wm size
adb shell wm density

# Storage info
adb shell df -h
```

### Runtime Information

```bash
# Currently running services
adb shell dumpsys activity services

# Running processes
adb shell ps

# Network connections
adb shell netstat

# Open TCP/UDP ports
adb shell ss -tunap
```

---

## Debugging & Logs

### Logcat (System Logs)

```bash
# View live logs
adb logcat

# Clear logcat buffer
adb logcat -c

# View logs with filters
adb logcat *:V    # Verbose
adb logcat *:D    # Debug
adb logcat *:I    # Info
adb logcat *:W    # Warning
adb logcat *:E    # Error
adb logcat *:F    # Fatal

# Filter by tag
adb logcat -s TAG_NAME

# Filter by app package
adb logcat --pid=$(adb shell pidof com.example.app)

# Save logs to file
adb logcat > logfile.txt &

# Stop logcat
adb logcat -d    # Dump and exit
```

### Advanced Logging

```bash
# Filter multiple tags
adb logcat -s TAG1:* TAG2:* TAG3:*

# Exclude specific tags
adb logcat -v brief TAG1:S TAG2:S

# Show logs since app start
adb logcat --since "2024-04-17 10:00:00.000"

# Format options
adb logcat -v brief      # Short format
adb logcat -v process    # Include PID/TID
adb logcat -v threadtime # Include timestamp
adb logcat -v long       # Full format

# Continuous logging to file (daily rotation)
adb logcat > ./logs/logcat-$(date +%Y%m%d-%H%M%S).txt
```

### Stack Traces & Crashes

```bash
# Get native crash logs
adb logcat | grep FATAL

# Java crash logs
adb logcat | grep Exception

# Dump heap for app
adb shell dumpsys meminfo --local com.example.app

# ANR (Application Not Responding)
adb logcat | grep ANR
```

---

## Network & Remote Access

### ADB over Network (Advanced Setup)

```bash
# Find device IP via ADB
adb shell ip route | grep wlan0

# Enable wireless debugging (Android 11+)
adb shell setprop service.adb.tcp.port 5555
adb shell stop adbd
adb shell start adbd

# Connect via wireless
adb connect <device-ip>:5555

# Verify connection
adb devices
```

### Reverse Port Forwarding

```bash
# Forward local port to device
adb forward tcp:8080 tcp:3000
# Connects localhost:8080 on PC to localhost:3000 on device

# Reverse forward (useful for IoT)
adb reverse tcp:3000 tcp:3000
# Device can reach localhost:3000 on your PC

# List all forwards
adb forward --list
adb reverse --list

# Remove specific forward
adb forward --remove tcp:8080

# Remove all forwards
adb forward --remove-all
adb reverse --remove-all
```

### Tunnel Setup for Remote IoT Devices

```bash
# If device is on different network, use SSH tunneling
ssh -L 5555:device-ip:5555 user@remote-server
adb connect localhost:5555

# Persistent connection script
while true; do
  adb connect 192.168.1.100:5555
  sleep 5
done
```

---

## Troubleshooting

### Common Issues & Solutions

#### Device Not Detected

```bash
# Check if device appears
adb devices

# Restart ADB server
adb kill-server
adb start-server

# Check USB connection
adb devices -l

# Linux: Fix permission issues
sudo usermod -aG plugdev $USER
# Log out and back in for changes to take effect

# Add udev rule (Linux)
echo 'SUBSYSTEM=="usb", ATTRS{idVendor}=="18d1", MODE="0666"' | sudo tee /etc/udev/rules.d/51-android.rules
sudo udevadm control --reload-rules
```

#### "Device offline" or "No device"

```bash
# Check device USB debugging is enabled
# On device: Settings → Developer Options → USB Debugging (enable)

# Restart ADB server
adb kill-server
adb devices

# Try different USB cable or port
# Check Windows Device Manager for "Unknown Device"
```

#### Connection Refused (Network)

```bash
# Verify device IP is correct
ping 192.168.1.100

# Check if port 5555 is open
adb shell netstat | grep 5555

# Restart network debugging on device
adb -s device-id shell setprop service.adb.tcp.port 5555

# Reconnect
adb disconnect
adb connect 192.168.1.100:5555
```

#### Insufficient Permissions

```bash
# Linux: User not in plugdev group
sudo usermod -aG plugdev $USER
newgrp plugdev

# Windows: Run as Administrator
# Start PowerShell as Admin before running ADB

# Clear ADB authorization
adb shell rm ~/.android/adbkey*
adb kill-server
adb start-server
```

### Debugging Commands

```bash
# Check ADB version
adb version

# Enable verbose output
adb -d logcat 2>&1 | head -100

# Check all connected devices with details
adb devices -l

# Verify shell access
adb shell "echo 'Connection successful'"

# Test file transfer
adb push test.txt /sdcard/test.txt && adb pull /sdcard/test.txt test_retrieved.txt
```

---

## Quick Reference

### Most Used Commands

```bash
# Connect to device
adb devices
adb connect 192.168.1.100:5555

# Access device shell
adb shell

# Transfer files
adb push local.txt /sdcard/
adb pull /sdcard/file.txt ./

# View logs
adb logcat -s TAG_NAME

# Install app
adb install app.apk

# Get system info
adb shell getprop ro.build.version.release

# Restart device
adb reboot
```

### Useful Aliases (Add to ~/.bashrc or ~/.zshrc)

```bash
# View device serial
alias adb-serial='adb shell getprop ro.serialno'

# Quick shell access
alias adb-shell='adb shell'

# View real-time logs
alias adb-logs='adb logcat -v threadtime'

# List installed packages
alias adb-packages='adb shell pm list packages'

# Check connection
alias adb-check='adb devices && adb shell "echo Connected"'
```

---

## Resources

- [Android Developer Documentation](https://developer.android.com/tools/adb)
- [Platform Tools Release Notes](https://developer.android.com/studio/releases/platform-tools)
- [ADB GitHub Repository](https://github.com/mjevans/adb_guide)

---

**Last Updated:** April 2026  
**ADB Version:** Platform Tools 35.0+