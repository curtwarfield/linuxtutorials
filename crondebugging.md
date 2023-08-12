## How to configure cron with debug logging

### Environment
Red Hat Enterprise Linux

### Issue

How can `cron` be configured to run in debug mode for more detailed logging?

### Resolution

The crond daemon has an `-x` option to set the following debug flags:
~~~
crond -x [ext,sch,proc,pars,load,misc,test,bit]

ext   print extended debugging information
sch   scheduling
proc  process control
pars  parsing
load  database loading
misc  miscellaneous
test  test mode - do not actually execute any commands
bit   show how various bits are set (long)
~~~

**Add** the following line to the `/etc/sysconfig/crond` file to enable debugging:

`CRONDARGS="-x ext,sch,proc,pars,load,misc,bit"`

**Restart** the `crond` daemon:
***
**Red Hat Enterprise Linux 6**   
`$ sudo service crond restart`
     
**Red Hat Enterprise Linux 7 or later**   
`$ sudo systemctl restart crond`
***
Additional log entries for `cron` will be in the `/var/log/messages` file.