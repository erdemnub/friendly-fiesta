hello ! 


**This documentation prepared and optimized for Alpine and Red hat Linux.**

Alpine Linux uses OpenRC for its init system.

first method remove unnecessary services on your machine.

In alpine linux The default startup uses the sysinit, boot, and default runlevels.

**First Disabled all unnecessary services.** 

```bash
rc-status --all    # to see all running service:
```
```bash
ps aux     # show detailed live processes.
``` 



To the delete unwanted services run:
```bash
rc-status del <service_name> boot
#Kill the running instance immediately since it is still active in memory.
rc-status <service_name> stop
```

 
