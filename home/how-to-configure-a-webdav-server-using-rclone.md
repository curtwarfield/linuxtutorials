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

# How to configure a WebDAV server using rclone

### Introduction

[WebDAV](http://www.webdav.org/) is an extension of the `HTTP` protocol that allows you share files remotely. Most web server software such as `Apache HTTP`have built-in `WebDAV` modules but they usually require complex configuration steps.

One of the easiest ways to configure a `WebDAV` server is by using `rclone`.

[Rclone](https://rclone.org/) is a command line application used to manage files on cloud storage, but it also be used to configure a `WebDAV` server.

### Step 1

1. Install `rclone`.

```
$ sudo dnf install rclone -y
```

2. Take a look at the `rclone serve webdav` command:

```
rclone serve webdav remote:path [flags]
```

The `remote:path` specifies the directory for the WebDAV server.

For example:

```
/media/webdav/
```

We will use the `addr`, `user`, and `pass` flags.

The `addr` flag is used to specify the IP address and port.

```
--addr 192.168.4.200:8111
```

The `user` flag is used to specify a username. Choose a new name that doesn't exist on the server.

```
--user poochie
```

The `pass` flag is the password for the `WebDAV` user.

```
--pass myRandomPassword
```

4. Here is the full command to start the `WebDAV` server:

```
$ rclone serve webdav /media/webdav/ --addr 192.168.4.200:8111 --user poochie --pass myRandomPassword
```

## Step 2

#### Create a custom `systemd` unit file to control the `WebDAV` server.

1. Create a `bash` script that contains the complete `rclone serve` command.

```
$ sudo vi /usr/bin/rclone/rclone_webdav.sh
```

```
#!/bin/bash
/usr/bin/rclone serve webdav /media/webdav/ --addr 192.168.4.200:8111 --user poochie --pass myRandomPassword
```

2. Make the script executable.

```
$ chmod +X /usr/bin/rclone/rclone_webdav.sh
```

4. Create a `systemd` unit file for the script.

```
$ vi /etc/systemd/system/rclone_webdav.service
```

5. Add the following lines:

```
[Unit]
Description=Rclone WebDAV Service
After=network.target

[Service]
ExecStart=/usr/bin/rclone/rclone_webdav.sh
Restart=always

[Install]
WantedBy=multi-user.target
```

6. Run the following `systemctl` commands to start the service:

```
$ systemctl daemon-reload
$ systemctl start rclone_webdav.service
$ systemctl enable rclone_webdav.service
```

7. Verify the WebDAV server is running:

```
$ systemctl status rclone_webdav.service
```
