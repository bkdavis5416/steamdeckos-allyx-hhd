# ROG Ally X: Fully Working `hhd-git` Setup on SteamOS

This guide outlines the **step-by-step process** to fix the `OSError: [Errno 16] Device or resource busy` error and make `hhd@deck` reliably start on boot. It's tailored for Arch-based systems like HoloISO or SteamOS running on the ASUS ROG Ally X.

> **Tested on:** HoloISO (Steam Deck UI), Arch base, SteamOS-like environment on ROG Ally X.

---

## ✅ What Works After Following This

* `hhd` daemon auto-starts after reboot
* ROG Ally X controller is emulated properly
* No more grab errors
* RGB, power profiles, and controller mappings function correctly

---

## 🔧 Prerequisites

Make sure your system has the following:

* Arch-based distro (HoloISO, SteamOS, etc.)
* A working internet connection
* Terminal access with sudo privileges

---

## 📦 Step 1: Install `yay` (AUR Helper)

```bash
sudo pacman -S --needed base-devel git
cd ~
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

---

## 📥 Step 2: Install `hhd-git`

```bash
yay -S hhd-git
```

Accept any prompts and allow yay to build/install dependencies. If prompted, confirm installation of `hhd-license-git`.

---

## 🛠 Step 3: Create and Configure the Override File

```bash
sudo mkdir -p /etc/systemd/system/hhd@deck.service.d
sudo nano /etc/systemd/system/hhd@deck.service.d/override.conf
```

Paste this into the file:

```ini
[Unit]
After=graphical.target inputplumber.service

[Service]
ExecStartPre=/bin/sleep 5
```

Save and exit (CTRL+O, Enter, then CTRL+X).

---

## 🔁 Step 4: Reload and Enable the Service

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable hhd@deck
```

---

## 🧪 Step 5: Reboot and Verify

```bash
systemctl status hhd@deck
```

You should see something like:

```
Active: active (running)
```

If it says `inactive (dead)`, recheck your override file and `WantedBy`.

---

## 🧠 Bonus Tips

* To debug startup issues:

  ```bash
  journalctl -u hhd@deck --no-pager
  ```
* To manually start if testing:

  ```bash
  sudo systemctl start hhd@deck
  ```

---

## 🙌 Credits

This was a frustrating issue caused by conflicting service startup order and device grabbing. Thanks to everyone in the community who’s shared their findings.

---

## 📤 Share This

If this saved your sanity, please star the repo and share with fellow ROG Ally tinkerers. Let’s make Linux handhelds better together.

> Pull requests welcome to improve or automate this further!
