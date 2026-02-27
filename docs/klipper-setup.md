# Klipper Setup Guide

## Add Spoolman Macros

Add the following to your `printer.cfg` (or include `spoolman_macros.cfg`):

```ini
[gcode_macro SET_ACTIVE_SPOOL]
description: Set the active spool in Spoolman via Moonraker
gcode:
  {% if params.ID is defined %}
    {action_call_remote_method("spoolman_set_active_spool", spool_id=params.ID|int)}
  {% endif %}

[gcode_macro CLEAR_ACTIVE_SPOOL]
description: Clear the active spool in Spoolman
gcode:
  {action_call_remote_method("spoolman_set_active_spool", spool_id=None)}
```

## Update Toolhead Macros

Add `variable_spool_id: None` to each of your T0-T3 toolchange macros so Fluidd can display and assign spools per toolhead.
The example below is from my madmax toolchanger setup. I edited the toolhead_X.cfg files

Example for T0 (replicate for T1, T2, T3):

```ini
[gcode_macro T0]
variable_color: ""
variable_tool_number: 0
variable_spool_id: None
gcode:
  _CHANGE_TOOL T={tool_number}
  {% if spool_id != None %}
    SET_ACTIVE_SPOOL ID={spool_id}
  {% endif %}
```

## Restart Klipper

```bash
sudo systemctl restart klipper
```

## Fluidd Multi-Toolhead Support

Fluidd natively supports per-toolhead spool selection when `variable_spool_id` is present in the toolchange macros.

Install Fluidd alongside Mainsail:

1. Download Fluidd:
```bash
mkdir -p ~/fluidd
cd ~/fluidd
wget -q -O fluidd.zip https://github.com/fluidd-core/fluidd/releases/latest/download/fluidd.zip
unzip fluidd.zip
rm fluidd.zip
```

2. Create nginx config at `/etc/nginx/sites-available/fluidd`:
```nginx
server {
    listen 81;
    listen [::]:81;

    root /home/YOUR_USERNAME/fluidd;
    index index.html;

    server_name _;

    location / {
        try_files $uri $uri/ /index.html;
    }

    location /websocket {
        proxy_pass http://apiserver;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    location ~ ^/(printer|api|access|machine|server)/ {
        proxy_pass http://apiserver;
        proxy_http_version 1.1;
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

3. Enable and restart nginx:
```bash
sudo ln -s /etc/nginx/sites-available/fluidd /etc/nginx/sites-enabled/fluidd
sudo nginx -t
sudo systemctl restart nginx
```

4. Access Fluidd at `http://YOUR_KLIPPER_IP:81`

5. In Fluidd Settings, add your Spoolman URL under the Spoolman section.
