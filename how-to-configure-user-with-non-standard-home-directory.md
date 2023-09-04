---
tags:
  - ssh
  - seLinux
  - configuration
title: How to configure an SSH user with a non-standard home directory
---
# How to configure an SSH user with a non-standard home directory

## Environment 
* Red Hat Enterprise Linux
* openssh
## Issue
* Configure a user with a non-standard home directory.
## Resolution
1. Create the root directory for the user.
~~~
# mkdir -p /path/to/directory
~~~
For example:
~~~
# mkdir -p /storage/media
~~~
2. Create the user and specify the home directory.
~~~
# useradd -d /path/to/directory/username username
# passwd username
~~~
For example:
~~~
# useradd -d /storage/media/myuser myuser
# passwd myuser
~~~

3. Use the `ssh-copy-id` command to copy the `ssh` key from a client to the server.
~~~
$ ssh-copy-id user@ip_address
~~~
For example:
~~~
$ ssh-copy-id myuser@192.0.2.1
~~~
4. Use the `chcon` command to apply the correct `SELinux` security context to the `authorized_keys` file.
~~~
# chcon -R -v system_u:object_r:usr_t:s0 /path/to/directory/username/.ssh/authorized_keys
~~~
  For example:
~~~
# chcon -R -v system_u:object_r:usr_t:s0 /storage/media/myuser/.ssh/authorized_keys
~~~