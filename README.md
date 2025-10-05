![OS](https://img.shields.io/badge/OS-Ubuntu_22.04+-blue.svg)
![Hardware](https://img.shields.io/badge/Hardware-ThinkPad_E14_Gen_5-red.svg)

# [GUIDE] Elan Fingerprint Reader (04f3:0c4b) on ThinkPad E14 Gen 5 for Linux

This is a guide to enable the Elan fingerprint reader (04f3:0c4b) on the Lenovo ThinkPad E14 Gen 5 for Linux distributions based on Ubuntu 22.04 (like Pop!_OS).

By default, the system doesn't detect this hardware and fprintd-enroll fails with a "No devices available" error. The fix is to use an official binary driver from Lenovo, which works perfectly even though it's listed for the Gen 4 model.

---

## The Fix: Step-by-Step

### 1. Download the Lenovo Driver

- **Link:** https://support.lenovo.com/us/en/downloads/ds560939-elan-fingerprint-driver-for-ubuntu-2204-thinkpad-e14-gen-4-e15-gen-4
- Download the .zip file (r1slf01w.zip).
- **Note:** The site may prompt for a serial number. Just click "cancel" on the pop-up and click the download button a second time.

### 2. Install the Driver

Extract the .zip file. You will find a single driver file inside: `libfprint-2-tod1-elan.so`.

Open a terminal **inside the folder where you extracted that file** and run the following commands:

# Install the required base library and packages
`sudo apt install libfprint-2-tod1 fprintd libpam-fprintd`

# Create the specific directory for this driver type
`sudo mkdir -p /usr/lib/x86_64-linux-gnu/libfprint-2/tod-1/`

# Copy the Lenovo driver into the system directory
`sudo cp libfprint-2-tod1-elan.so /usr/lib/x86_64-linux-gnu/libfprint-2/tod-1/`

# Restart the fingerprint service to load the new driver
`sudo systemctl restart fprintd.service`

### 3. Enroll Your Fingerprint

Your fingerprint reader should now be working. You can enroll your finger through the GUI or the command line.

- **GUI:** Settings > Users > Fingerprint Login
- **Terminal:** fprintd-enroll

---

## How to Check if it Worked
To verify that the system correctly sees your device, you can run:

`fprintd-list $USER`

The output should list your username and the "ELAN Fingerprint Sensor".

---

## Optional Tweak: Fixing Keyring Password Prompts

After setting up your fingerprint reader, the system will still prompt for your password to unlock the "Login Keyring". Here is a concise, two-step fix for that.

### 1. Allow Fingerprint Login to Unlock the Keyring

First, configure the login process to accept your fingerprint as sufficient.

Edit the file `/etc/pam.d/gdm-password`:

`sudo nano /etc/pam.d/gdm-password`

Add the following line to the very top of the file:

`auth       sufficient   pam_fprintd.so`

WARNING: A typo in this file can lock you out of your system. Please double-check the line before saving.

---

### 2. Remove the Keyring Password for Applications [WARNING]

Second, remove the keyring's own password so applications can access it seamlessly.

- **WARNING** This stores saved passwords unencrypted on disk. This is a common and low-risk trade-off if you already use full-disk encryption.

Open the Passwords and Keys application.

Right-click the Login keyring and select Change Password.

Enter your current password, then leave the new password fields blank and save. Acknowledge the security warning when it appears.

Reboot your computer to apply the changes. The password prompts will now be gone.

---

## TL;DR

The Elan fingerprint reader (04f3:0c4b) on the ThinkPad E14 Gen 5 works on Ubuntu 22.04-based distros using the official Lenovo binary driver for the E14 Gen 4. Download it here: https://support.lenovo.com/us/en/downloads/ds560939-elan-fingerprint-driver-for-ubuntu-2204-thinkpad-e14-gen-4-e15-gen-4
