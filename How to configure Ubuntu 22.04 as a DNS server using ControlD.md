# Configure Ubuntu 22.04 as a DNS server using Control D

## Introduction
* If you want to run your own DNS server for increased security, privacy, and control, using `Control D` is a great way to accomplish this.
* [Control D](https://controld.com) is a fully customizable DNS service, similar to Pi-Hole, AdGuard or NextDNS, but with proxy capabilities. 
* This means it not only blocks things (ads, porn, etc), but can also unblock websites and services.

## Prerequisites
* An active `Control D` account.
* A `Control D` **Resolver ID**.
* A device and profile already configured on the [Dashboard](https://controld.com/dashboard/profiles).
## Installation

1. Install the `ctrld` utility.
~~~
$ sudo sh -c 'sh -c "$(curl -sL https://api.controld.com/dl)" -s RESOLVER_ID_HERE'
~~~
Substitute `RESOLVER_ID_HERE` for your actual **Resolver ID**.

2. Start the `Control D` DNS proxy service.
~~~
$ sudo ctrld start
~~~
Here is a successful start:
~~~
$ sudo ctrld start
Aug 20 16:15:03.000 NTC Starting service
Aug 20 16:15:10.000 NTC Service started
~~~
3. Edit the `/etc/controld/ctrld.toml` file to change the `ip = '127.0.0.1'` line to the actual **IP address** of the server.
~~~
[listener]
  [listener.0]
    ip = '192.168.4.136'
    port = 53
~~~
4. Restart the `ctrld` service.
~~~
$ sudo ctrld restart
~~~
5. Verify that the server's **IP address** is listed as the only **nameserver** in the `/etc/resolv.conf` file and change this if necessary, for example:
~~~
nameserver 192.168.4.136
~~~
6. Make the following changes to the `/etc/systemd/resolved.conf` file:
~~~
DNS=76.76.2.22#RESOLVER_ID_HERE.dns.controld.com
DNSOverTLS=yes
~~~
Substitute `RESOLVER_ID_HERE` for your actual **Resolver ID**.   
Use one of `ControlD`'s DNS resolvers as the DNS **IP address**, for example: **76.76.2.22**.    
See the [Free DNS Resolvers](https://controld.com/free-dns) page for more detail.

7. Restart the `systemd-resolved` service.
~~~
$ sudo systemctl restart systemd-resolved.service
~~~
8. Restart the `ctrld` service one more time.
~~~
$ sudo ctrld restart
~~~
9. Run a test query using the `dig` command specifying the **IP address** of the system.
~~~
$ dig verify.controld.com @192.168.4.136 +short
~~~
If `verify.controld.com` resolves, you are successfully using `Control D` for DNS requests. You can now use this system as the DNS server for your entire network by simply configuring your router to use this system's **IP address**.    

If you are unable to specify a DNS server in your router, you can also change any client on your network to point to this system's **IP address** as an alternative.

