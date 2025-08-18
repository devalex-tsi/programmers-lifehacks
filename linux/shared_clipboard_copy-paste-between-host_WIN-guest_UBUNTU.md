# Installing VirtualBox Guest Additions on Ubuntu Guest

This guide explains how to install **VirtualBox Guest Additions** on an Ubuntu guest machine to enable features like **Shared Clipboard** (copy-paste between host and guest) and **Drag & Drop**.

## Prerequisites

Before starting the installation, make sure the following settings are configured in VirtualBox:

1. **Shared Clipboard**: `Bidirectional`
2. **Drag & Drop**: `Bidirectional`
3. **Guest OS**: Ubuntu (tested with 20.04, 22.04)

> These settings are found in VirtualBox Manager → Select your VM → Settings → General → Advanced.

---

## Step 1: Prepare Ubuntu for Guest Additions

Open a terminal in your Ubuntu guest and run:

```bash
sudo apt update
sudo apt install -y build-essential dkms linux-headers-$(uname -r)
```

This installs required compilers, headers, and DKMS modules.

---

## Step 2: Insert Guest Additions CD

In the VirtualBox window (top menu):

```
Devices → Insert Guest Additions CD image...
```

Ubuntu should automatically detect the CD. If not, you can mount it manually.

---

## Step 3: Install Guest Additions

```bash
sudo mkdir -p /mnt/vbox
sudo mount /dev/cdrom /mnt/vbox  # if not auto-mounted
sudo sh /mnt/vbox/VBoxLinuxAdditions.run
```

- Follow the prompts and agree to install drivers.
- When installation completes, **reboot** the guest:

```bash
sudo reboot
```

---

## Step 4: Verify Clipboard Functionality

After reboot, ensure **Shared Clipboard** is still set to `Bidirectional`.

In Ubuntu, check that the VirtualBox client is running:

```bash
pgrep -a VBoxClient
```

You should see a process like:

```
/usr/bin/VBoxClient --clipboard
```

If not, you can start it manually:

```bash
VBoxClient --clipboard & disown
```

---

## Step 5: After Restart (if clipboard stops working)

If after restarting (either **reboot** inside Ubuntu or **powering off and starting** the VM from VirtualBox Manager) the clipboard does not work:

1. Make sure the required packages are installed:

   ```bash
   sudo apt install -y virtualbox-guest-utils virtualbox-guest-x11
   ```

2. Start the `VBoxClient` clipboard service manually:

   ```bash
   pkill -f "VBoxClient --clipboard" 2>/dev/null
   VBoxClient --clipboard & disown
   ```

3. If it works, the service will usually also run automatically on future startups.

4. Otherwise, reboot the VM:

   ```bash
   sudo reboot
   ```

5. Verify again that `VBoxClient --clipboard` is running (see Step 4).

---

## Notes

- **Drag & Drop** is less reliable on Linux guests; consider using **Shared Folders** for file transfers.
- Make sure the version of Guest Additions matches your VirtualBox version.
- If using Ubuntu on Wayland, switching to **Xorg** may improve clipboard support.

---

This setup enables seamless copy-paste and other integration features between your host and Ubuntu guest in VirtualBox.

