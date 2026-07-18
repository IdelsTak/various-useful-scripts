# Flameshot 14 screenshot and overlay failure on XFCE/X11

## Symptoms

- Choosing **Take Screenshot** from the Flameshot tray menu did not display the
  selection/annotation overlay.
- `flameshot gui` remained running, but its X11 windows were unmapped (hidden).
- `flameshot full --path <file>` hung and did not create a screenshot.

## Cause

The system was running Flameshot `14.0.0-1` on XFCE/X11.  Flameshot 14 uses
`xdg-desktop-portal` for screen capture by default, including on X11.

This XFCE session had only the GTK portal backend installed:

```text
xdg-desktop-portal
xdg-desktop-portal-gtk
```

That backend did not export the `org.freedesktop.portal.Screenshot` interface.
XFCE's portal preference file listed the `xapp` screenshot backend first, but
`xdg-desktop-portal-xapp` was not installed.  As a result, Flameshot waited on
an unavailable portal screenshot implementation, so both capture and the GUI
flow stalled without a useful Flameshot error.

## Remedy applied

Enable Flameshot's legacy X11 capture method in:

```text
~/.config/flameshot/flameshot.ini
```

Under `[General]`, add:

```ini
useX11LegacyScreenshot=true
```

Then restart Flameshot so it reloads the configuration:

```bash
pkill flameshot
flameshot &
```

This bypasses the unavailable portal Screenshot interface and uses the native
X11 capture path instead.

## Verification performed

After the change:

```bash
flameshot full --path /tmp/flameshot-capture-verify.png
```

completed successfully and created a valid `3000 x 1920` PNG.  Invoking
`flameshot gui` also created a viewable X11 capture window, confirming that the
tray capture/annotation workflow could appear on screen again.

## Notes

- The notification daemon message `Image data is not the correct type` was
  unrelated; it concerned rendering a notification image, not screen capture.
- The existing `buttons=@Invalid()` value was investigated, but an isolated
  clean Flameshot configuration showed the same failure before this fix.  It
  was not the capture failure's cause.
- An alternative system-level solution is to install and configure a portal
  backend that implements the Screenshot interface.  The legacy X11 setting is
  the smaller, reliable fix for this XFCE/X11 session.
