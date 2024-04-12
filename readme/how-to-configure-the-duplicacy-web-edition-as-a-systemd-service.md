# How to configure the Duplicacy Web edition as a systemd service

### Introduction

The [Duplicacy Web Edition](https://duplicacy.com/download.html) is a single binary file that can be run directly from the `Linux` command line.

This makes it simple to run and does not require installation.

However, if you prefer to run this as a `systemd` service that automatically starts up in the background, you can create a custom `systemd` service.

### Configuration

1. Copy the **Duplicacy** binary file to the `/usr/bin/` directory.

For example:

```
sudo cp duplicacy_web_linux_x64_1.8.0 /usr/bin
```

2. Change the permissions to `755`.

```
sudo chmod 755 /usr/bin/duplicacy_web_linux_x64_1.8.0
```

3. Create a `systemd` service unit in the `/usr/lib/systemd/user` directory.

For example:

```
sudo vi /usr/lib/systemd/user/duplicacy-web.service
```

The contents should look like the following:

```
[Unit]
Description=Duplicacy Web service
After=network.target

[Service]
Type=simple
ExecStart=/usr/bin/duplicacy_web_linux_x64_1.8.0

[Install]
WantedBy=default.target
```

4. Reload the `systemctl` daemon.

```
sudo systemctl daemon-reload
```

5. Enable the service.

```
systemctl --user enable duplicacy-web.service
```

6. Start the service.

```
systemctl --user start duplicacy-web.service
```

7. Run the `loginctl` command with the `linger` option to make sure the service runs in the background even when logged off from the system.

```
sudo loginctl enable-linger <username>
```

Change `<username>` to your actual username.

The **Duplicacy** web GUI will now automatically start an run in the background as a `systemd` service.
