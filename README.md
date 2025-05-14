# ASUS ROG Ally X - HHD Service Fix (SteamOS 3.8)

This guide walks you through getting `hhd@deck.service` running and **persisting across reboots** on systems like SteamOS 3.8 (ASUS ROG Ally X).

---

## Step 0: Unlock the Filesystem (SteamOS Only)

SteamOS uses a read-only root filesystem. To make changes like editing systemd units or installing packages, you must disable this protection:

```bash
sudo steamos-readonly disable
```

> Optionally, ensure `/` is mounted read-write:
>
> ```bash
> sudo mount -o remount,rw /
> ```

---

## Step 1: Install Required Build Tools

```bash
sudo pacman -S --needed base-devel git
```

If you get keyring errors, initialize and populate the keyring:

```bash
sudo pacman-key --init
sudo pacman-key --populate
```

---

## Step 2: Install `yay` AUR Helper (Optional)

If you don’t already have `yay`, install it manually:

```bash
cd ~
git clone https://aur.archlinux.org/yay.git
tar -xvf yay && cd yay
makepkg -si
```

---

## Step 3: Install `hhd-git` from AUR

```bash
yay -S hhd-git
```

---

## Step 4: Create Override for hhd\@deck Service

Create the override directory:

```bash
sudo mkdir -p /etc/systemd/system/hhd@deck.service.d
```

Then create the override config:

```bash
sudo nano /etc/systemd/system/hhd@deck.service.d/override.conf
```

Paste the following content:

```ini
[Unit]
After=graphical.target inputplumber.service

[Service]
ExecStartPre=/bin/sleep 5
```

Save and exit (CTRL+O, ENTER, CTRL+X).

---

## Step 5: Enable the Service for Reboot (System User Session Fix)

Because this is a user-specific service, enable it specifically for the current user session with:

```bash
sudo ln -s /usr/lib/systemd/system/hhd@.service /etc/systemd/system/graphical.target.wants/hhd@deck.service
```

Then reload and enable:

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable hhd@deck
```

You can check its current status:

```bash
systemctl status hhd@deck
```

---

## Step 6: Reboot & Confirm

Now reboot your system:

```bash
sudo reboot
```

After rebooting, confirm the service is active:

```bash
systemctl --user status hhd.service
```

You should see:

```bash
Active: active (running)
```

---

## Success

The `hhd` service should now launch automatically on every boot, **after** graphics and `inputplumber` are ready.

---

## Notes

* This guide is written for SteamOS systems.
* If future updates overwrite system-level units, you may need to **reinstall `hhd-git`** or reapply the override.

Contributions welcome!
