# Bitz-Miner
# Detailed guide to Bitz miner on Android
Step-by-Step Instructions to Install Linux (Ubuntu) on Android Using proot-distro and Install Solana CLI + solana-keygen (Agave)
> Prerequisites
- [Android Device: Android 7.0+ recommended (proot-distro has issues below 7). At least 4GB RAM and 10GB free storage for smooth compilation.] 
- [Termux: The terminal emulator hosting proot-distro. Download on Playstore] 
- [Internet: Stable Wi-Fi/mobile data for downloads.] 
- [Storage Access: Ensure Termux has storage permissions (Settings > Apps > Termux > Permissions > Storage).]

# Part 1: Install Ubuntu via proot-distro on Android 
> Step 1: Install Termux

1. Download Termux:
- Get from F-Droid (preferred, up-to-date): https://f-droid.org/packages/com.termux/
- Avoid Google Play version (outdated).
Install and open Termux.

2. Grant Permissions:
- Run:
```
termux-setup-storage
```
- Allow storage access when prompted.

3. Update Termux:
```
pkg update && pkg upgrade
```
> Step 2: Install proot-distro
1. Install proot-distro:
```
pkg install proot-distro
```
2. List Available Distros:
```
proot-distro list
```
- Look for ubuntu (e.g., ubuntu or ubuntu-24.04). Ubuntu is best for Solana due to package support.

> Step 3: Install Ubuntu
1. Install Ubuntu:
```
proot-distro install ubuntu
```
- Downloads a minimal Ubuntu rootfs (usually 22.04 or 24.04, ~200-500MB).
- If it fails (e.g., network error), retry or check storage:
2. Log In to Ubuntu:
```
proot-distro login ubuntu
```
- Prompt changes to root@localhost:~#, indicating you’re in Ubuntu.
- If login fails, try:
```
proot-distro login ubuntu --fix-low-ports
```

> Step 4: Set Up Ubuntu Environment
1. Update Packages:
```
apt-get update
apt-get upgrade -y
```
2. Install Basic Tools:
```
apt-get install -y curl git
```
3. Verify Architecture:
```
uname -m
```
- Expect: aarch64. Confirms your environment matches Solana’s build target.

# Part 2: Install Solana CLI and solana-keygen (Agave)
We’ll build solana and solana-keygen from Agave v2.2.6 source, as no aarch64 binaries exist.
> Step 5: Install Build Dependencies
Install packages needed for Rust and Agave:
```
apt-get install -y build-essential clang pkg-config libssl-dev zlib1g-dev protobuf-compiler libprotobuf-dev cmake
```