# nvidia-driver-setup-linux-mint
Quick overview to get nvidia driver running when dualbooting linux and windows with secure boot enabled

# NVIDIA Driver Setup for Linux Mint with Secure Boot Enabled

This repository provides a step-by-step guide for setting up NVIDIA drivers on a Linux Mint system, particularly in a dual-boot configuration with Windows. It addresses issues that may arise due to Secure Boot blocking the NVIDIA driver from loading.

## Problem Description

When dual-booting **Linux Mint** and **Windows**, I encountered an issue where my second monitor was not being detected, and the NVIDIA driver was not working as expected. Running the `nvidia-smi` command produced the following error:


```
NVIDIA-SMI has failed because it couldn't communicate with the NVIDIA driver. Make sure that the latest NVIDIA driver is installed and running.
```


### Root Cause

A quick search revealed that the issue was related to **Secure Boot** interfering with the loading of the NVIDIA kernel module. Since the proprietary NVIDIA kernel modules are not signed by a trusted authority, Secure Boot prevents them from loading. To resolve this, I needed to sign the modules locally and enroll the signing key using **MOK (Machine Owner Key)**.

## Solution

### Step 1: Verify the Module Signing Status

I verified that the NVIDIA kernel module was signed locally by running:

modinfo nvidia | grep signer


The output indicated that the module was indeed **locally signed**.

### Step 2: Import the MOK Key

To allow the system to load the signed module, I imported the signing key into the **MOK database**. This can be done with the following command:

sudo mokutil --import /var/lib/shim-signed/mok/MOK.der


When running this command, you'll be prompted to set a password. **Be sure to remember this password**, as you will need it to complete the enrollment process during the next reboot.

### Step 3: Enroll the Key at Boot

After rebooting the system, you will be prompted by the **MOK Manager** to enroll the key. Follow these steps:
1. Select **"Enroll MOK"**.
2. Enter the password you set during the import step.
3. Confirm the key enrollment.

Once the key is successfully enrolled, Secure Boot will allow the NVIDIA kernel module to load, and the second monitor should be detected correctly.

### Step 4: Verify Driver Installation

To ensure everything is working correctly, you can check if the NVIDIA driver is loaded by running:

nvidia-smi


This should display detailed information about your GPU and driver status.

## Conclusion

By signing the NVIDIA kernel modules and enrolling the key with MOK, I was able to resolve the issue of Secure Boot blocking the driver, allowing the second monitor to work properly.




