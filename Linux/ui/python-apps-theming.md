
## Make python apps UI follow general theme styling

- Install `qt5ct`: (`# sudo pacman -Sy qt5ct`)
- Edit `/etc/environment` as root by `sudo nano /etc/environment`, for instance, and add the line `QT_QPA_PLATFORMTHEME=qt5ct` and save.
- Log out and in (or reboot)
- Now in qt5ct you can change your theme and settings

> The important part is adding `QT_QPA_PLATFORMTHEME=qt5ct` to `/etc/environment`. This makes the Qt theme redirect to `qt5ct`.
