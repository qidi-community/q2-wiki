# Manually Downloading Updates

Download firmware updates directly from Qidi's servers when automatic updates aren't working.

## Version Check API

The printer checks this API endpoint to determine the latest available firmware version:

```
https://api.qidi3dprinter.com/code/x_q_2_version_info
```

The API returns a JSON response with firmware information including version, size, date, and descriptions in multiple languages.

## Download URL

You can download the latest update zip file directly from:

```
https://download_aws.qidi3dprinter.com/QD_Q2/Q2_V1.0.8.zip
```

Replace `1.0.8` with the current version number from the API response.

> [!NOTE]
> Older versions are removed from the server, so only the latest version is available for download.

## Usage

1. Check the API for the current version number
2. Download the zip file for that version
3. Extract the zip file on your computer
4. Find the extracted update folder (usually named something like "update" or with version number)
5. Rename this folder to exactly "QD_Update"
6. Copy the "QD_Update" folder to the root of your USB stick
7. Insert the USB stick into the printer
8. Select "Offline Update" from the printer menu to install

> [!IMPORTANT]
> The printer will only recognize the update if the folder is named exactly "QD_Update" (case-sensitive). Do not copy the original zip file to the USB - only the renamed folder.