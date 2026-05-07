# Arch Linux CUPS PDF Printing Setup Guide

This guide installs and configures a fully working virtual PDF printer on Arch Linux using:

- CUPS
- cups-pdf
- GhostScript
- cups-filters

The result:
- applications can print normally
- output becomes PDF files
- PDFs are written to `~/PDF`

# 1. Install Required Packages

Install all required printing components.

```bash
sudo pacman -S cups cups-pdf cups-filters ghostscript gsfonts
```

Package roles:

| Package      | Purpose                        |
| ------------ | ------------------------------ |
| cups         | Core printing system           |
| cups-pdf     | Virtual PDF printer backend    |
| cups-filters | Modern print filter support    |
| ghostscript  | PS/PDF rendering engine        |
| gsfonts      | Additional fonts for rendering |

# 2. Enable and Start CUPS

Enable the CUPS scheduler permanently.

```bash
sudo systemctl enable --now cups
```

Verify:

```bash
systemctl status cups --no-pager
```

The service must be:

- active
- running

# 3. Add User to Printer Administration Group

```bash
sudo usermod -aG lpadmin $USER
```

Log out and log back in afterward.

Verify:

```bash
groups
```

You should see:

```text
lpadmin
```

# 4. Open the CUPS Web Interface

Open:

```text
http://localhost:631
```

This is the CUPS administration UI.

# 5. Configure cups-pdf

Edit configuration:

```bash
sudo nano /etc/cups/cups-pdf.conf
```

Use these settings.

## Output Directory

Uncomment or add:

```ini
Out ${HOME}/PDF
```

This writes generated PDFs to:

```text
~/PDF
```

## Keep Default GhostScript Invocation

Use the default GSCall exactly:

```ini
GSCall %s -q -dCompatibilityLevel=%s -dNOPAUSE -dBATCH -dSAFER -sDEVICE=pdfwrite -sOutputFile="%s" %s
```

Do not simplify this command.

Incorrect GSCall values can:

- silently break printing
- produce blank PDFs
- prevent PDF generation entirely

## PDF Version

Recommended:

```ini
PDFVer 1.4
```

# 6. Restart CUPS

```bash
sudo systemctl restart cups
```

# 7. Remove Broken PDF Printer Queues

If previous PDF printers existed, remove them first.

Example:

```bash
sudo lpadmin -x Virtual_PDF_Printer
```

List printers:

```bash
lpstat -p
```

# 8. Find Correct cups-pdf Model

List available models:

```bash
lpinfo -m | rg -i 'cups-pdf|Generic CUPS-PDF'
```

Example output:

```text
drv:///cups-pdf/CUPS-PDF_noopt.ppd Generic CUPS-PDF Printer (no options)
```

Copy the exact model URI.

# 9. Create the Virtual PDF Printer

Example:

```bash
sudo lpadmin \
  -p Virtual_PDF_Printer \
  -E \
  -v cups-pdf:/ \
  -m drv:///cups-pdf/CUPS-PDF_noopt.ppd
```

Explanation:

| Option | Meaning        |
| ------ | -------------- |
| -p     | Printer name   |
| -E     | Enable printer |
| -v     | Backend URI    |
| -m     | PPD/model      |

# 10. Verify Printer Registration

Run:

```bash
lpstat -v
```

Expected:

```text
device for Virtual_PDF_Printer: cups-pdf:/
```

# 11. Test Printing

## Plain Text Test

```bash
printf 'hello cups-pdf\n' | lp -d Virtual_PDF_Printer
```

## File Test

```bash
lp -d Virtual_PDF_Printer /etc/hosts
```

## PDF File Test

```bash
lp -d Virtual_PDF_Printer /home/<user>/Documents/sample.pdf
```

# 12. Locate Output PDFs

Generated PDFs appear in:

```text
~/PDF
```

Example:

```text
~/PDF/home_user_PDF.pdf
```

# 13. Common Failure Modes

## Blank 1-Page PDFs

Cause:

- broken printer queue
- stale PPD/filter configuration
- invalid cups-pdf backend association

Fix:

- remove and recreate printer queue
- use correct cups-pdf model URI

## No PDF Generated

Cause:

- invalid GSCall
- CUPS service not running
- broken backend path

Fix:

- restore default GSCall
- restart CUPS
- recreate printer

## Printer Missing in Web UI

Cause:

- cups-pdf package missing
- CUPS not restarted

Fix:

```bash
sudo pacman -S cups-pdf
sudo systemctl restart cups
```

# 14. Useful Diagnostics

## List Printers

```bash
lpstat -p
```

## List Devices

```bash
lpstat -v
```

## View CUPS Logs

```bash
sudo less /var/log/cups/error_log
```

## Enable Debug Logging

```bash
sudo cupsctl --debug-logging
sudo systemctl restart cups
```

Disable later:

```bash
sudo cupsctl --no-debug-logging
```

# 15. Known Reality About cups-pdf

cups-pdf is an older PostScript-era backend.

Modern Linux printing systems are increasingly:

- PDF-native
- IPP Everywhere based

Because of this:

- stale queues break easily
- backend mismatches produce blank PDFs
- recreating the printer queue often fixes problems immediately

The most reliable setup is:

- clean CUPS install
- correct cups-pdf backend URI
- correct PPD/model
- default GSCall
- cups-filters installed
