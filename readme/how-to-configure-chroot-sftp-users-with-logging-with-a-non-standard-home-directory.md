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

# How to configure chroot sftp users with logging with a non-standard home directory

## Environment

* Red Hat Enterprise Linux
* openssh

## Issue

* How to configure chroot sftp users with logging with a non-standard home directory.

## Resolution

1. Create the chroot directory including the user's home directory.

```
# mkdir -p /path/to/directory/user/home
```

For example:

```
# mkdir -p /storage/media/myuser/home
```

In this example, `home` was used for the name of the directory under the username, but this subdirectory can be named whatever you want.

For example:

```
# mkdir -p /storage/media/myuser/uploads
```

2. Create the user and specify the newly created home directory.

```
# useradd -d /path/to/directory/user/home -M username
# passwd username
```

For example:

```
# useradd -d /storage/media/myuser/home -M myuser
# passwd myuser
```

3. Create a `/dev` directory in the user directory.

```
# mkdir /path/to/directory/user
```

For example:

```
# mkdir /storage/media/myuser/dev
```

4. Change the ownership and permissions of the home directory.

```
# chown username:username /path/to/directory/user/home
# chmod 700 /path/to/directory/user/home
```

For example:

```
# chown myuser:myuser /storage/media/myuser/home
# chmod 700 /storage/media/myuser/home
```

Because of the requirements of `chroot`, all directories except for the `home` directory need to be owned by root.

For example:

```
# ls -l / | grep storage
drwxr-xr-x.   4 root root   32 Sep 22 09:23 storage

# ls -l /storage/
drwxr-xr-x. 3 root root 20 Sep 22 09:23 media

# ls -l /storage/media/
drwxr-xr-x. 4 root root 29 Sep 22 09:23 myuser

# ls -l /storage/media/myuser/
drwxr-xr-x. 2 root   root   6 Sep 22 09:23 dev
drwx------. 2 myuser myuser 6 Sep 22 09:23 home
```

If you use a different directory besides `home`, the same ownership and permissions apply.

```
# ls -l /storage/media/myuser/
drwxr-xr-x. 2 root   root   6 Sep 22 09:23 dev
drwx------. 2 myuser myuser 6 Sep 22 09:23 uploads
```

5. Use the `ssh-copy-id` command to copy the `ssh` key from a client to the server.

```
$ ssh-copy-id user@ip_address
```

For example:

```
$ ssh-copy-id myuser@192.0.2.1
```

6. Use the `chcon` command to apply the correct `SELinux` security context to the `authorized_keys` file.

```
# chcon -R -v system_u:object_r:usr_t:s0 /path/to/directory/username/home/.ssh/authorized_keys
```

For example:

```
# chcon -R -v system_u:object_r:usr_t:s0 /storage/media/myuser/home/.ssh/authorized_keys
```

7. Create an entry in the `/etc/rsyslog.conf` file to create the log file for the user.

```
$AddUnixListenSocket /path/to/directory/user/dev/log
input(type="imuxsock" Socket="/path/to/directory/user/dev/log" CreatePath="on")
if $programname == 'internal-sftp' then /var/log/sftp.log
& stop
```

For example:

```
$AddUnixListenSocket /storage/media/myuser/dev/log
input(type="imuxsock" Socket="/storage/media/myuser/dev/log" CreatePath="on")
if $programname == 'internal-sftp' then /var/log/sftp.log
& stop
```

8. Run the bind option of the `mount` command.

```
# mount -o bind /dev /path/to/directory/user/dev
```

For example:

```
# mount -o bind /dev /storage/media/myuser/dev
```

9. Apply the correct `SELinux` label to the log file.

```
# semanage fcontext -a -t devlog_t /path/to/directory/user/dev/log
```

For example:

```
# semanage fcontext -a -t devlog_t /storage/media/myuser/dev/log
```

10. Edit the `/etc/ssh/sshd_config` file and add a `Match` section for the user:

```
Match User username
ChrootDirectory /path/to/directory/%u
ForceCommand internal-sftp -f AUTH -l VERBOSE -d /UsersSubdirectory
```

For example:

```
Match User myuser
ChrootDirectory /storage/media/%u
ForceCommand internal-sftp -f AUTH -l VERBOSE -d /home
```

11. Enable the `SELinux` boolean for ssh chroot.

```
# setsebool -P ssh_chroot_rw_homedirs on
# setsebool -P selinuxuser_use_ssh_chroot on
```

12. Restart `rsyslog` and `sshd`.

```
# systemctl restart rsyslog
# systemctl restart sshd
```
