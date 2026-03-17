# NVIDIA Driver Setup for Linux Mint with Secure Boot Enabled

Quick overview to get the NVIDIA driver running when dual-booting Linux and Windows with Secure Boot enabled.

## Problem Description

When dual-booting **Linux Mint** and **Windows**, I encountered an issue where my second monitor was not being detected and the NVIDIA driver was not working as expected. Running `nvidia-smi` produced the following error:
```
NVIDIA-SMI has failed because it couldn't communicate with the NVIDIA driver. Make sure that the latest NVIDIA driver is installed and running.
```

### Root Cause

The issue was related to **Secure Boot** interfering with the loading of the NVIDIA kernel module. Since the proprietary NVIDIA kernel modules are not signed by a trusted authority, Secure Boot prevents them from loading. To resolve this, the modules need to be signed locally and the signing key enrolled using **MOK (Machine Owner Key)**.

## Solution

### Step 1: Verify the Module Signing Status

Verify that the NVIDIA kernel module was signed locally by running:
```bash
modinfo nvidia | grep signer
```

The output should indicate that the module is **locally signed**.

### Step 2: Import the MOK Key

Import the signing key into the **MOK database** with the following command:
```bash
sudo mokutil --import /var/lib/shim-signed/mok/MOK.der
```

You will be prompted to set a password. **Remember this password**, as you will need it to complete the enrollment process during the next reboot.

### Step 3: Enroll the Key at Boot

After rebooting, you will be prompted by the **MOK Manager** to enroll the key. Follow these steps:

1. Select **"Enroll MOK"**.
2. Enter the password you set during the import step.
3. Confirm the key enrollment.

Once the key is successfully enrolled, Secure Boot will allow the NVIDIA kernel module to load and the second monitor should be detected correctly.

### Step 4: Verify Driver Installation

Verify that the NVIDIA driver is loaded correctly by running:
```bash
nvidia-smi
```

This should display detailed information about your GPU and driver status.

## Conclusion

By signing the NVIDIA kernel modules and enrolling the key with MOK, the issue of Secure Boot blocking the driver can be resolved, allowing the second monitor to work properly.


