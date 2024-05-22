---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# How to configure ErsatzTV as a systemd service

## Introduction

[ErsatzTV](https://ersatztv.org/docs/intro) can be used for creating custom live streaming channels from your media files.

## Installation

1. Create a folder named `ersatztv` in the `/opt` directory.

```
sudo mkdir /opt/ersatztv
```

2. Download and then extract the [latest release from GitHub](https://github.com/ErsatzTV/ErsatzTV/releases) to the `/opt/ersatztv` folder.

```
sudo tar --strip-components=1 -xvf ErsatzTV-v0.8.6-beta-linux-x64.tar.gz -C /opt/ersatztv
```

## Configuration

1. Create the `systemd` service unit in the `/usr/lib/systemd/user` directory.

```
sudo vi /usr/lib/systemd/user/ersatztv.service
```

Add the following lines:

```
[Unit]
Description=ErsatzTV channel service
After=network.target

[Service]
Type=simple
ExecStart=/opt/ersatztv/ErsatzTV

[Install]
WantedBy=default.target
```

2. Reload the `systemctl` daemon.

```
sudo systemctl daemon-reload
```

5. Enable the service.

```
systemctl --user enable ersatztv.service
```

6. Start the service.

```
systemctl --user start ersatztv.service
```

7. Run the `loginctl` command with the `linger` option to ensure the service runs in the background even when you are logged out.

```
sudo loginctl enable-linger <username>
```

Replace `<username>` with your actual username.

## Summary

The `Ersatztv` service will now run in the background as a `systemd` service and be managed with `systemctl` commands.
