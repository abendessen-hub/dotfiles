# Dotfiles

Personal Arch Linux + Hyprland dotfiles managed with **GNU Stow**.
Shout out to [Ascanio's Hyprlain which im using rn](https://github.com/Ascaniolamp/Hyprlain)

## Packages Overview

| Package | Target | Description |
| :--- | :--- | :--- |
| `aben-lain` | `~` | Active Hyprland, Waybar, Rofi, Kitty, GTK, Qt, Spicetify, Vesktop, and Zsh configs |
| `aben` | `~` | Original/previous desktop configuration backup |
| `sddm-lain-v2` | `/` | Active SDDM login theme (`Hyprlain`) and `sddm.conf` (`DisplayServer=x11`) |
| `grub-lain` | `/` | GRUB configuration (`/etc/default/grub`) and `lain` GRUB theme backup |

---

## Setup on a New PC / Laptop

### 1. Prerequisites
Install `git` and `stow` (plus Hyprland, SDDM, and theme dependencies on your new machine):

```bash
sudo pacman -S --needed git stow
```

### 2. Clone the Repository
Clone this repository into your home directory (`~/dotfiles`):

```bash
git clone <YOUR_GIT_REPO_URL> ~/dotfiles
cd ~/dotfiles
```

### 3. Stow User Desktop Configs (`aben-lain`)
Back up or remove any default configs generated during installation (such as `~/.config/hypr`, `~/.config/gtk-3.0`, `~/.config/gtk-4.0`, or `~/.zshrc`), then stow `aben-lain`:

```bash
stow -d ~/dotfiles -t ~ aben-lain
```

*(Optional: To switch back to the older `aben` setup anytime, run `stow -D -t ~ aben-lain && stow -t ~ aben`.)*

### 4. Stow SDDM Login Screen (`sddm-lain-v2`)
SDDM runs as its own system user and needs execute (`+x`) traversal permissions on your home and dotfiles directories to read symlinked theme files:

```bash
chmod o+x "\$HOME" "HOME/dotfiles" "HOME/dotfiles/sddm-lain-v2"
sudo rm -f /etc/sddm.conf
sudo rm -rf /usr/share/sddm/themes/Hyprlain
sudo stow -d ~/dotfiles -t / sddm-lain-v2
```

Verify SDDM before rebooting:
```bash
sddm-greeter --test-mode --theme /usr/share/sddm/themes/Hyprlain
```

### 5. Stow GRUB Configuration (`grub-lain`)
Because UEFI `/boot` partitions use `vfat` (FAT32), they do not support symbolic links. Copy the `lain` theme folder directly to `/boot/grub/themes/`, stow `/etc/default/grub` with `--ignore='^boot'`, and regenerate your GRUB config:

```bash
chmod o+x "\$HOME" "HOME/dotfiles" "HOME/dotfiles/grub-lain"
sudo mkdir -p /boot/grub/themes
sudo cp -r ~/dotfiles/grub-lain/boot/grub/themes/lain /boot/grub/themes/
sudo rm -f /etc/default/grub
sudo stow -d ~/dotfiles -t / --ignore='^boot' grub-lain
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

---

## Quick Reference: Re-Stowing Later

```bash
# User dotfiles
stow -d ~/dotfiles -t ~ aben-lain

# SDDM
sudo stow -d ~/dotfiles -t / sddm-lain-v2

# GRUB (ignores FAT32 /boot partition)
sudo stow -d ~/dotfiles -t / --ignore='^boot' grub-lain
```