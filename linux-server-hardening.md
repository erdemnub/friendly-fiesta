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
Here's a breakdown of the command:

ps: is the process status command.

a: displays information about other users' processes as well as your own.

u: displays the processes belonging to the specified usernames.

x: includes processes that do not have a controlling terminal.

To the delete unwanted services run:
```bash
rc-status del <service_name> boot
#Kill the running instance immediately since it is still active in memory.
rc-status <service_name> stop
```

*For Rhel:*

```bash
systemctl --type=service # to show running service on machine.
``` 
*Stopping and disabled service:*
```bash
$sudo systemctl stop <service_name>
$sudo systemctl disable <service_name>
```




### Configured and hardening sshd service.

**Before applying any of these changes, keep your current SSH session open and make sure you have an alternative way to access your server, such as a console connection, in case you lock yourself out.**


### Back up your configuration:
```bash
cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
```

-Applying security settings to sshd config file: 

**Opening sshd file.**

For Alpine : 
```bash
vi /etc/ssh/sshd_config.
```
For Rhel : 
```bash
sudo nano /etc/ssh/sshd_config
```
```text
# Authentication Mechanism
PasswordAuthentication         no     #Force key-based authentication
KbdInteractiveAuthentication   no	  #Disable keyboard-interactive password prompts
PermitEmptyPasswords	       no     #Block empty passwords


# Access control 
PermitRootLogin no                   #Disable root SSH login
AllowUsers	deploy admin	           #Restrict SSH access to listed users


#Session and Tİmeout Policies

LoginGraceTime	30	                 #Shorten the authentication window
MaxAuthTries	3	                     #Limit failed login attempts
ClientAliveInterval	300	             #Set idle timeout interval
ClientAliveCountMax	2	               #Disconnect after 2 missed keep-alives

# Feature Disabling

X11Forwarding	no	                   #Disable X11 forwarding
AllowAgentForwarding	no	           #Disable agent forwarding
AllowTcpForwarding	no	             #Disable SSH tunnels for users who do not need them
```





















-After each change, test the configuration before SSH.
```bash
sudo sshd -t
```

If the command prints no output, the configuration syntax is valid. Then restart the SSH service:
```bash
sudo systemctl restart sshd
```
### Network Policies.

**Network Security Rules on linux.**

To alpine linux : 

```bash
# installing iptables (alpine linux firewall)
apk add iptables
rc-update add iptables
rc-service iptables start

# clearing all default rules
iptables -F

# droping all incoming network traffic. We only allow output traffic.

iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT

# allowing lo(localhost) network traffic 
iptables -A INPUT -i lo -j ACCEPT

# allowing new sshd port 
iptables -A INPUT -p tcp --dport 2222 -j ACCEPT

# saving the rule
rc-service iptables save
```

** On Rhel : 

```bash
# identifying new sshd port.
sudo firewall-cmd --permanent --add-port=2222/tcp

# removing the old sshd service. 
sudo firewall-cmd --permanent --remove-service=ssh

# saving the rules.
sudo firewall-cmd --reload


















