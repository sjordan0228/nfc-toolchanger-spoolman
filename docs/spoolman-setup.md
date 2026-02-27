# Spoolman Setup Guide

## Add Extra Fields

Spoolman needs two custom extra fields on spools to work with this system.

1. Go to your Spoolman UI (e.g. `http://YOUR_SPOOLMAN_IP:7912`)
2. Go to **Settings → Extra Fields → Spool**
3. Add the following fields:

### Field 1: NFC ID
- **Key:** `nfc_id`
- **Name:** `nfc_id`
- **Field Type:** Text
- **Order:** 1

### Field 2: Active Toolhead
- **Key:** `active_toolhead`
- **Name:** `active_toolhead`
- **Field Type:** Text
- **Order:** 2

## Register NFC Tags on Spools

For each spool:

1. Scan the NFC tag with one of your toolhead readers
2. Check the middleware logs — you'll see:
   ```
   No spool found in Spoolman for UID: XX-XX-XX-XX
   ```
3. Note the UID
4. Go to Spoolman → find or create your spool
5. Edit the spool and enter the UID in the `nfc_id` field

> **Note:** Spoolman stores the nfc_id with extra quotes internally. The middleware handles this automatically by stripping them during comparison.

## Moonraker Integration

Add the following to your `moonraker.conf`:

```ini
[spoolman]
server: http://YOUR_SPOOLMAN_IP:7912
sync_rate: 5
```

Then restart Moonraker:
```bash
sudo systemctl restart moonraker
```
