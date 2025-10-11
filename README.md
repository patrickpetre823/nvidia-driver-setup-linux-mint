# nvidia-driver-setup-linux-mint
Quick overview to get nvidia driver running when dualbooting linux and windows with secure boot enabled

# NVIDIA Driver Setup for Linux Mint with Secure Boot Enabled

This repository provides a step-by-step guide for setting up NVIDIA drivers on a Linux Mint system, particularly in a dual-boot configuration with Windows. It addresses issues that may arise due to Secure Boot blocking the NVIDIA driver from loading.

## Problem Description

When dual-booting **Linux Mint** and **Windows**, I encountered an issue where my second monitor was not being detected, and the NVIDIA driver was not working as expected. Running the `nvidia-smi` command produced the following error:

NVIDIA-SMI has failed because it couldn't communicate with the NVIDIA driver. Make sure that the latest NVIDIA driver is installed and running.


```
NVIDIA-SMI has failed because it couldn't communicate with the NVIDIA driver. Make sure that the latest NVIDIA driver is installed and running.
```


### Root Cause

A quick search revealed that the issue was related to **Secure Boot** interfering with the loading of the NVIDIA kernel module. Since the proprietary NVIDIA kernel modules are not signed by a trusted authority, Secure Boot prevents them from loading. To resolve this, I needed to sign the modules locally and enroll the signing key using **MOK (Machine Owner Key)**.

## Solution

### Step 1: Verify the Module Signing Status

I verified that the NVIDIA kernel module was signed locally by running:




A quick google search showed the reason for this is Secure Boot interfering with the loading of Nvidida kernel module. Because the propiertary nvidia kernel modules are not trusted by UEFI I need to sign the key and enroll them using MOK (Machine Owner Key). 

Checkign for the nvidia kernel modules using told me it is locally signed.

```
modinfo nvidia | grep signer
```

So I imported the key into MOK. WHen doing this you will have to set a password. It is important to remember this, as you have to enter it later when rebooting.

```
sudo mokutil --import /var/lib/shim-signed/mok/MOK.der
```

After that I rebootet. While rebooting the option pops up to "Enroll MOK". Enter the password and confirm.

