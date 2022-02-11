# Easily create unprivileged Linux containers in Slackware

This script assumes that you have successfully completed all steps in part 1 from Chris Willing's [guide](https://www.chriswilling.com/lxc/setup-unpriv-slackware.html) and worked through the steps described in this [post](https://nixing.mx/posts/unprivileged-containers-in-slackware-15.html) up until the "Host networking" section. The script will not change any configuration on the host.

Check the [post](https://nixing.mx/posts/unprivileged-containers-in-slackware-15.html) that covers using Slackware based Linux containers for more details on the individual steps

Once you have the container host ready, check the script's help message by running it without parameters:

    root@slack15:~# ./create-lxc
    The create-lxc script takes the following options:
        -r  Set Slackware release to use, if not set use the default specified in /usr/share/lxc/templates/lxc-slackwa    re
        -m  Set mirror to use, if not set use the default specified in /usr/share/lxc/templates/lxc-slackware
        -n  Set container name to use, this parameter is required
        -t  Set template to use, if not set use /usr/share/lxc/templates/lxc-slackware
        -f  Set config file to use
        -u  Set non privileged user that will own the resulting container, this parameter is required
        -g  Set user group that will be used to set the necessary permissions, this parameter is required
        -d  Create the container with a basic DHCP configuration
        -h  Print help options and exit

Creating an unprivileged container:

    root@slack15:~# ./create-lxc -dr 15.0 -n test1 -u lxcuser -g lxc -f /root/lxc/default.conf

The above command will run the `create-lxc` script which will execute the following actions:

- Create a privileged container based on Slackware `15.0`
- Container's name will be `test1`
- Use the default `slackpkg` mirror defined in `/usr/share/lxc/templates/lxc-slackware`
- The `/root/lxc/default.conf` file will be used as a custom LXC configuration file
- A basic DHCP configuration will be set in the container i.e. `USE_DHCP[0]="yes"`
- Once the `test1` privileged container is created, convert it to unprivileged and transfer it to the `lxcuser`'s environment
- Download and build tools required for `uid` and `gid` remapping if necessary, search path is `/tmp/create-lxc-tmpdir`
- Remap `uid`s and `gid`s
- Ownership of the relevant container files will be granted to the `lxcuser` user and the `lxc` group.
- Update `test1` configuration from privileged to unprivileged
- Update `test1` mount point configuration
- The original `test1` privileged container will then be deleted.

Once the script finishes the above tasks the user can expect the following output, as the `lxcuser`:
	
    lxcuser@slack15:~$ lxc-ls -f
    NAME     STATE   AUTOSTART GROUPS IPV4       IPV6 UNPRIVILEGED 
    test1    STOPPED 0         -      -          -    true         

Start the container as usual:

    lxcuser@slack15:~$ lxc-start -n test1
    lxcuser@slack15:~$ lxc-ls -f
    NAME     STATE   AUTOSTART GROUPS IPV4       IPV6 UNPRIVILEGED 
    test1    RUNNING 0         -      10.0.3.65  -    true         
    
Enjoy!
