# Connecting to Your Printer via SSH

SSH lets you access your printer's terminal directly from your computer. This is needed for many configuration tasks, installing software, and troubleshooting.

---

## Default Credentials

| Field    | Value       |
|----------|-------------|
| Username | `makerbase` |
| Password | `mks`       |
| Port     | `22`        |

---

## Finding Your Printer's IP Address

On the printer touchscreen, go to **Settings → Network**. Your IP address is displayed at the top of the screen for both WiFi and wired connections.



![Network settings screen showing IP address](images/network-screen.png)



> **Tip:** Assign your printer a static IP in your router's DHCP settings so the address never changes between reboots.

---

## Windows

### Option 1: Windows Terminal / Command Prompt (built-in)

Windows 10 and 11 include SSH natively. Open **Command Prompt** or **Windows Terminal** and run:
ssh makerbase@YOUR_PRINTER_IP
Enter `mks` when prompted for the password. That's it.

### Option 2: GUI SSH Clients

If you want more than a basic terminal, several free GUI clients are popular in the community. All use the same credentials (host = printer IP, port `22`, username `makerbase`, password `mks`).

**[Bitvise](https://www.bitvise.com/ssh-client)** — Good starting point if you're new to SSH. Opens a graphical file browser alongside the terminal automatically, so you can drag and drop files to/from the printer without any extra tools. Also supports saved profiles if you have multiple printers.

**[PuTTY](https://www.putty.org/)** — The classic. Lightweight, no install required, and has been around forever so there's no shortage of tutorials. Terminal only — no built-in file browser.

**[MobaXterm](https://mobaxterm.mobatek.net/)** — Feature-rich option that includes a built-in file browser (like Bitvise), multiple tabs, and a bunch of other network tools bundled in. The free Home edition is plenty for printer use.

---

## macOS

macOS has SSH built in. Open **Terminal** (Applications → Utilities → Terminal) and run:
ssh makerbase@YOUR_PRINTER_IP
Enter `mks` when prompted.

> If you want a graphical file browser similar to Bitvise, [Cyberduck](https://cyberduck.io/) is a free option — connect using SFTP with the same credentials.

---

## Linux

You already know what you're doing. Just in case:

```bash
ssh makerbase@YOUR_PRINTER_IP
For file transfer, scp or sftp work fine, or mount via SSHFS if you prefer.
After Connecting
You should see a prompt like:
makerbase@mkspi:~$
You're in. Some useful first commands:
# Check memory usage
free -h

# View running processes
htop

# Check Klipper/Moonraker service status
sudo systemctl status klipper
sudo systemctl status moonraker
Troubleshooting
Connection refused / timeout:
Double check the IP on the touchscreen — it may have changed if you don't have a static IP set
Make sure the printer is fully booted (give it 60 seconds after powering on)
Confirm you're on the same network as the printer
Permission denied:
Username is makerbase, password is mks — both lowercase, no spaces
---

Watch out for the code blocks — the triple backticks are the most likely thing to get mangled by autocorrect.