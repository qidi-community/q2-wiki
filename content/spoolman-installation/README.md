# Installing Spoolman

Install Spoolman for filament tracking and management through the Klipper ecosystem.

## Prerequisites

> [!NOTE]
> First ensure KIAUH is properly updated and fixed by following the [Updating and Fixing KIAUH guide](../kiauh-update-and-fix/README.md).

## Installation

1. Launch KIAUH:
   ```
   ~/kiauh/kiauh.sh
   ```

2. Navigate through the KIAUH menus to install Spoolman:
   - Choose "Install" from the main menu
   - Select "Spoolman" from the available software options
   - Follow the prompts to complete the installation

## Moonraker Configuration

Add the following section to your `moonraker.conf`:

```ini
[spoolman]
server: http://<PRINTER_IP>:7912
sync_rate: 5
```

Replace `<PRINTER_IP>` with your printer's IP address. The port remains `7912`.

## Final Steps

Restart Moonraker to apply the configuration changes:
```
sudo systemctl restart moonraker
```

## Important Considerations

> [!WARNING]
> Spoolman uses a significant amount of RAM. Ensure your printer has sufficient memory available before installation.

Spoolman will now be accessible through Mainsail/Fluidd and can track your filament spools, remaining filament, and usage history.