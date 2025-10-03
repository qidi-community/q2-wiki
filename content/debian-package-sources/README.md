# Updating Debian Package Sources

Replace the default package sources with functional repositories for better package availability and download speeds.

## Package Sources Configuration

Edit `/etc/apt/sources.list` and replace the contents with:

```
deb http://deb.debian.org/debian bullseye main contrib
deb-src http://deb.debian.org/debian bullseye main contrib
deb http://deb.debian.org/debian-security bullseye-security main contrib
deb-src http://deb.debian.org/debian-security bullseye-security main contrib
deb http://deb.debian.org/debian bullseye-updates main contrib
deb-src http://deb.debian.org/debian bullseye-updates main contrib
deb http://archive.debian.org/debian bullseye-backports main contrib
deb-src http://archive.debian.org/debian bullseye-backports main contrib
```

## Installation

1. Edit `/etc/apt/sources.list` with the content above
2. Update package lists:
   ```
   sudo apt update
   ```

## Why Update Sources

- Default sources may have non-functional backports
- Stock sources often use servers from China, which may be slow depending on your location
- Provides better access to required packages for various software installations