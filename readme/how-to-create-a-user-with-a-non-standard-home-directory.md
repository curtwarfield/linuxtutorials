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

# How to create a user with a non-standard home directory

## Environment

* Red Hat Enterprise Linux
* openssh

## Issue

* How to create an `SSH` user with a non-standard home directory.

## Resolution

1. Create the home directory for the user.

```plaintext
# mkdir -p /path/to/directory/user
```

For example:

```
# mkdir -p /storage/media/myuser
```

2. Apply the correct `SELinux` label.

```
# chcon -Rv --type=user_home_dir_t /path/to/directory/user
```

For example:

```
# chcon -Rv --type=user_home_dir_t /storage/media/myuser
```

3. Create the user and specify the home directory.

```
# useradd -d /path/to/directory/username -M username
# passwd username
```

For example:

```
# useradd -d /storage/media/myuser -M myuser
# passwd myuser
```

4. Change the file permission and ownership of the home directory.

```
# chown -R user:user /path/to/directory/user
# chmod 700 /path/to/directory/user
```

For example:

```
# chown -R myuser:myuser /storage/media/myuser
# chmod 700 /storage/media/myuser
```

5. Use the `ssh-copy-id` command to copy the `ssh` key from the client to the server.

```
$ ssh-copy-id user@ip_address
```

For example:

```
$ ssh-copy-id myuser@192.0.2.1
```

6. Apply the correct `SELinux` security context to the `authorized_keys` file.

```
# chcon -R -v system_u:object_r:usr_t:s0 /path/to/directory/username/.ssh/authorized_keys
```

For example:

```
# chcon -R -v system_u:object_r:usr_t:s0 /storage/media/myuser/.ssh/authorized_keys
```

Verify that you can log in successfully.

```
$ ssh myuser@192.0.2.1

Last login: Fri Sep 29 16:19:37 2023 from 192.0.2.33
[myuser@example ~]$ 
```
