# LightDM dual-monitor layout and wallpapers

## Issue

The XFCE desktop has a two-monitor profile, but LightDM had only
`user-background = true` for each monitor. That leaves the greeter dependent on
the user's desktop background and does not recreate XFCE's monitor layout in
LightDM's separate X server. An old `monitor0` XFCE entry also references an
unmounted `/run/media/.../pro_pic_gdm.jpeg` file, so it must not be used by the
login screen.

## Saved desktop configuration

Source: `~/.config/xfce4/xfconf/xfce-perchannel-xml/{displays,xfce4-desktop}.xml`.

| Output | Layout | Wallpaper |
| --- | --- | --- |
| `VGA-1` | 1920x1080 at 60 Hz, rotate left, position `0x0` | `/usr/share/backgrounds/xfce/xfce-shapes.svg` |
| `DP-1` | 1920x1080 at 144 Hz, primary, position `1080x0` | `/usr/share/backgrounds/xfce/xfce-blue.jpg` |

The rotated VGA output is 1080 pixels wide; therefore DP-1 starts at `x=1080`.

## Remedy

LightDM starts its own X server before the user session, so it needs both a
display-setup script and an explicit greeter background per output. Create the
following three files on a new installation.

### `/etc/lightdm/display-setup.sh`

```sh
#!/bin/sh
# Recreate the saved Xfce "Default" display profile for LightDM's X server.
# VGA-1: 1920x1080, rotated left, positioned at 0x0.
# DP-1:  1920x1080 at 144 Hz, primary, positioned to its right.

xrandr \
  --output VGA-1 --mode 1920x1080 --rate 60 --rotate left --pos 0x0 \
  --output DP-1 --mode 1920x1080 --rate 144 --rotate normal --pos 1080x0 --primary
```

### `/etc/lightdm/lightdm.conf.d/20-dual-monitor.conf`

```ini
[Seat:*]
display-setup-script=/etc/lightdm/display-setup.sh
```

### `/etc/lightdm/lightdm-gtk-greeter.conf`

This is the complete local configuration used here. Preserve any deliberate
local theme or font changes if replacing it on another machine.

```ini
[greeter]
theme-name = Greybird
icon-theme-name = elementary-xfce
font-name = SF Pro Display 11
xft-antialias = true
xft-rgba = rgb
xft-hintstyle = hintslight
default-user-image = /data/profile-pic-cropped.png
background =

[monitor: DP-1]
background = /usr/share/backgrounds/xfce/xfce-blue.jpg
user-background = false

[monitor: VGA-1]
background = /usr/share/backgrounds/xfce/xfce-shapes.svg
user-background = false
```

The output names (`DP-1` and `VGA-1`) must match `xrandr --query` on the new
machine. Adjust them, and any unavailable mode/refresh rate, before installing.
`144` represents the saved XFCE rate of `143.999336...` Hz.

## Fresh-install prerequisites

1. Install and enable the `lightdm` and `lightdm-gtk-greeter` packages.
2. Ensure these system wallpaper files exist; install the XFCE background
   package if necessary, or copy chosen replacement images to a readable system
   path such as `/usr/share/backgrounds/custom/` and update the configuration.

   ```bash
   ls -l /usr/share/backgrounds/xfce/xfce-blue.jpg \
         /usr/share/backgrounds/xfce/xfce-shapes.svg
   ```

3. With both monitors connected, run `xrandr --query` from an XFCE session and
   confirm the connector names, supported modes, and placement requirements.
4. Do not put the login wallpaper under `/run/media`, a user-only directory, or
   another mount that may be unavailable before login.

## Files staged in this installation

The same content was prepared, before privileged installation, in:

- `~/lightdm-setup/display-setup.sh` — runs `xrandr` for the two outputs.
- `~/lightdm-setup/20-dual-monitor.conf` — registers that script as LightDM's
  `display-setup-script`.
- `~/lightdm-setup/lightdm-gtk-greeter.conf` — assigns the two system wallpaper
  files to their named monitor sections and disables `user-background`.

## Install

Run these commands in a terminal; administrator authentication is required.
The existing greeter configuration is backed up first.

```bash
sudo mkdir -p /etc/lightdm/lightdm.conf.d
sudo install -m 755 ~/lightdm-setup/display-setup.sh /etc/lightdm/display-setup.sh
sudo install -m 644 ~/lightdm-setup/20-dual-monitor.conf /etc/lightdm/lightdm.conf.d/20-dual-monitor.conf
sudo cp -p /etc/lightdm/lightdm-gtk-greeter.conf /etc/lightdm/lightdm-gtk-greeter.conf.codex-backup-20260718
sudo install -m 644 ~/lightdm-setup/lightdm-gtk-greeter.conf /etc/lightdm/lightdm-gtk-greeter.conf
```

On a clean machine, create the files from the contents above (or use the staged
copies after recreating them), then run the same install commands.

Restart LightDM or reboot to test from a fresh greeter session. Do not restart
LightDM from the active graphical session unless it is safe to end that session.

## Verify

At the next login screen, VGA-1 should show the shapes image in portrait
orientation and DP-1 should show the blue image to its right. If one display is
blank or misplaced, switch to a TTY, inspect the connector names with `xrandr`,
correct `/etc/lightdm/display-setup.sh`, and reboot.

## Rollback

```bash
sudo cp -p /etc/lightdm/lightdm-gtk-greeter.conf.codex-backup-20260718 /etc/lightdm/lightdm-gtk-greeter.conf
sudo rm -f /etc/lightdm/lightdm.conf.d/20-dual-monitor.conf /etc/lightdm/display-setup.sh
```
