# Updating and Fixing KIAUH

Update KIAUH and fix common line ending errors that occur on the Q2 printer.

## Prerequisites

> [!NOTE]
> First update your package sources by following the [Debian Package Sources guide](../debian-package-sources/README.md).

## Update KIAUH

1. Install git (if not already installed):

   ```
   sudo apt install git
   ```

2. Navigate to KIAUH directory and update:
   ```
   cd kiauh
   git pull
   ```

## Fix Line Ending Error

When running KIAUH, you may encounter this error:

```
/home/mks/.kiauh.ini: line 19: $'\r': command not found
```

### Install dos2unix

```
sudo apt install dos2unix
```

### Modify KIAUH Script

Edit `~/kiauh/scripts/utilities.sh`:

1. Find the `read_kiauh_ini` function (around line 130)
2. Add `dos2unix -q "${INI_FILE}"` above the `source` command

The function should look like this:

```bash
function read_kiauh_ini() {
  local func=${1}

  if [[ ! -f ${INI_FILE} ]]; then
    log_warning "Reading from .kiauh.ini failed! File not found! Creating default ini file."
    init_ini
  fi

  log_info "Reading from .kiauh.ini ... (${func})"
  dos2unix -q "${INI_FILE}"
  source "${INI_FILE}"
}
```

## Usage

After applying these fixes, KIAUH should run without line ending errors. You can now use it to install and manage software.
