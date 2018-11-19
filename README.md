# Setting up OpenVpn - Quickly on Mac and Digital Ocean

Step 1

Create a public private key locally which you will use for SSH into the server at Digital Ocean.

Your ssh keys will be stored in this location on your mac.  You will will need to substitute
your username for <username>.

```bash

	$ /Users/<username>/.ssh/
	$ .ssh $ ssh-keygen -t rsa

```

Output from ssh-keygen

```bash
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/cn/.ssh/id_rsa): digitalocean_ssh_key_example
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in digitalocean_ssh_key_example.
Your public key has been saved in digitalocean_ssh_key_example.pub.
The key fingerprint is:
SHA256:******************************************************** username@macbook-Air
The key's randomart image is:
+---[RSA 2048]----+
|              .  |
|               o |
|                +|
|         .  ...o+|
|        S + .o+.=|
|         . = *+o+|
|          o.X+oO |
|         ..O+=B.+|
|         E=+*++O*|
+----[SHA256]-----+

$ ls
digitalocean_ssh_key_example
digitalocean_ssh_key_example.pub

```

This is the private key.  Do not share this key.

```
cn .ssh $ cat digitalocean

-----BEGIN RSA PRIVATE KEY-----
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
***************************************************************
**************************************************
-----END RSA PRIVATE KEY-----

```

This is the public key which you will copy over to Digital Ocean.
Select all the output cmd-c and paste into Digital Ocean public
key.

```bash

$ cat digitalocean.pub 
ssh-rsa ************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************ username@macbook-Air

```

## Step 2

TODO: ADD IMAGES FROM Digital Ocean HERE

CREATE YOUR Digital Ocean VM - IMAGE

## Step 3 

Log into your server from you machine/pc/laptop etc....

```bash

cn .ssh $ ssh root@xx.xx.xx.xxx -i /Users/cn/.ssh/digitalocean.pub
The authenticity of host 'xx.xx.xx.xxx (xx.xx.xx.xxx)' can't be established.
ECDSA key fingerprint is SHA256:fLG5iNC6Ca86MThS2Ye/59TB/b6tMTlYTR3Up562Hqw.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'xx.xx.xx.xxx' (ECDSA) to the list of known hosts.
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0644 for '/Users/cn/.ssh/digitalocean.pub' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "/Users/cn/.ssh/digitalocean.pub": bad permissions
Permission denied (publickey).
cn .ssh $ ssh root@xx.xx.xx.xxx -i /Users/cn/.ssh/digitalocean
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.4.0-87-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

```

## Step 4

Get the version of Ubunutu or your server's OS so we can find the correct 
version of openvpn to download and install.

```bash

# lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 16.04.3 LTS
Release:	16.04
Codename:	xenial

```

### Step 5 
Download open vpn...

How to find the download link for your version...

```bash

# wget http://swupdate.openvpn.org/as/openvpn-as-2.1.9-Ubuntu16.amd_64.deb
--2017-08-07 23:05:35--  http://swupdate.openvpn.org/as/openvpn-as-2.1.9-Ubuntu16.amd_64.deb
Resolving swupdate.openvpn.org (swupdate.openvpn.org)... 104.24.1.59, 104.24.0.59
Connecting to swupdate.openvpn.org (swupdate.openvpn.org)|104.24.1.59|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 32066316 (31M) [application/octet-stream]
Saving to: ‘openvpn-as-2.1.9-Ubuntu16.amd_64.deb’
openvpn-as-2.1.9-Ubuntu16.amd_64.deb 100%[===================================================================>]  30.58M   164MB/s    in 0.2s    
2017-08-07 23:05:35 (164 MB/s) - ‘openvpn-as-2.1.9-Ubuntu16.amd_64.deb’ saved [32066316/32066316]

```
Step 6 - Install OpenVpn

```bash

# dpkg -i openvpn*.deb
Selecting previously unselected package openvpn-as.
(Reading database ... 54295 files and directories currently installed.)
Preparing to unpack openvpn-as-2.1.9-Ubuntu16.amd_64.deb ...
Unpacking openvpn-as (2.1.9-Ubuntu16) ...
Setting up openvpn-as (2.1.9-Ubuntu16) ...
The Access Server has been successfully installed in /usr/local/openvpn_as
Configuration log file has been written to /usr/local/openvpn_as/init.log
Please enter "passwd openvpn" to set the initial
administrative password, then login as "openvpn" to continue
configuration here: https://xx.xx.xx.xxx:943/admin
To reconfigure manually, use the /usr/local/openvpn_as/bin/ovpn-init tool.

Access Server web UIs are available here:
Admin  UI: https://xx.xx.xx.xxx:943/admin
Client UI: https://xx.xx.xx.xxx:943/

```

Step 
root@ubuntu-512mb-lon1-01-london:~# passwd openvpn
Enter new UNIX password: 
Retype new UNIX password: 
passwd: password updated successfully

root@ubuntu-512mb-lon1-01-london:~# cat /usr/local/openvpn_as/bin/ovpn-init
#!/bin/bash

# Check root is running this...
if [ "$(id -u)" != "0" ]; then
echo Error: You must be root to run this. 2>&1
exit 1
fi

# Setup pyovpn env vars...
. /usr/local/openvpn_as/exports

# Needed for certain operations inside the python script...
export CONFIG_JSON=/usr/local/openvpn_as/etc/config.json
export WIN_INSTALL=/usr/local/openvpn_as/etc/exe/OpenVPN_Installer.exe

# Execute the python script...
python /usr/local/openvpn_as/bin/_ovpn-init "$@"
root@ubuntu-512mb-lon1-01-london:~# sudo cat /usr/local/openvpn_as/bin/ovpn-init
#!/bin/bash

# Check root is running this...
if [ "$(id -u)" != "0" ]; then
echo Error: You must be root to run this. 2>&1
exit 1
fi

# Setup pyovpn env vars...
. /usr/local/openvpn_as/exports

# Needed for certain operations inside the python script...
export CONFIG_JSON=/usr/local/openvpn_as/etc/config.json
export WIN_INSTALL=/usr/local/openvpn_as/etc/exe/OpenVPN_Installer.exe

# Execute the python script...
python /usr/local/openvpn_as/bin/_ovpn-init "$@"
root@ubuntu-512mb-lon1-01-london:~# /usr/local/openvpn_as/bin/ovpn-init
Detected an existing OpenVPN-AS configuration.
Continuing will delete this configuration and restart from scratch.
Please enter 'DELETE' to delete existing configuration: DELETE

          OpenVPN Access Server
          Initial Configuration Tool
------------------------------------------------------
OpenVPN Access Server End User License Agreement (OpenVPN-AS EULA)

    1. Copyright Notice: OpenVPN Access Server License;
       Copyright (c) 2009-2013 OpenVPN Technologies, Inc.. All rights reserved.
       "OpenVPN" is a trademark of OpenVPN Technologies, Inc.
    2. Redistribution of OpenVPN Access Server binary forms and related documents,
       are permitted provided that redistributions of OpenVPN Access Server binary
       forms and related documents reproduce the above copyright notice as well as
       a complete copy of this EULA.
    3. You agree not to reverse engineer, decompile, disassemble, modify,
       translate, make any attempt to discover the source code of this software,
       or create derivative works from this software.
    4. The OpenVPN Access Server is bundled with other open source software
       components, some of which fall under different licenses. By using OpenVPN
       or any of the bundled components, you agree to be bound by the conditions
       of the license for each respective component. For more information, you can
       find our complete EULA (End-User License Agreement) on our website
       (http://openvpn.net), and a copy of the EULA is also distributed with the
       Access Server in the file /usr/local/openvpn_as/license.txt.
    5. This software is provided "as is" and any expressed or implied warranties,
       including, but not limited to, the implied warranties of merchantability
       and fitness for a particular purpose are disclaimed. In no event shall
       OpenVPN Technologies, Inc. be liable for any direct, indirect, incidental,
       special, exemplary, or consequential damages (including, but not limited
       to, procurement of substitute goods or services; loss of use, data, or
       profits; or business interruption) however caused and on any theory of
       liability, whether in contract, strict liability, or tort (including
       negligence or otherwise) arising in any way out of the use of this
       software, even if advised of the possibility of such damage.
    6. OpenVPN Technologies, Inc. is the sole distributor of OpenVPN Access Server
       licenses. This agreement and licenses granted by it may not be assigned,
       sublicensed, or otherwise transferred by licensee without prior written
       consent of OpenVPN Technologies Inc. Any licenses violating this provision
       will be subject to revocation and deactivation, and will not be eligible
       for refunds.
    7. A purchased license entitles you to use this software for the duration of
       time denoted on your license key on any one (1) particular device, up to
       the concurrent user limit specified by your license. Multiple license keys
       may be activated to achieve a desired concurrency limit on this given
       device. Unless otherwise prearranged with OpenVPN Technologies, Inc.,
       concurrency counts on license keys are not to be divided for use amongst
       multiple devices. Upon activation of the first purchased license key in
       this software, you agree to forego any free licenses or keys that were
       given to you for demonstration purposes, and as such, the free licenses
       will not appear after the activation of a purchased key. You are
       responsible for the timely activation of these licenses on your desired
       server of choice. Refunds on purchased license keys are only possible
       within 30 days of purchase of license key, and then only if the license key
       has not already been activated on a system. To request a refund, contact us
       through our support ticket system using the account you have used to
       purchase the license key. Exceptions to this policy may be given for
       machines under failover mode, and when the feature is used as directed in
       the OpenVPN Access Server user manual. In these circumstances, a user is
       granted one (1) license key (per original license key) for use solely on
       failover purposes free of charge. Other failover and/or load balancing use
       cases will not be eligible for this exception, and a separate license key
       would have to be acquired to satisfy the licensing requirements. To request
       a license exception, please file a support ticket in the OpenVPN Access
       Server ticketing system. A staff member will be responsible for determining
       exception eligibility, and we reserve the right to decline any requests not
       meeting our eligibility criteria, or requests which we believe may be
       fraudulent in nature.
    8. Activating a license key ties it to the specific hardware/software
       combination that it was activated on, and activated license keys are
       nontransferable. Substantial software and/or hardware changes may 
       invalidate an activated license. In case of substantial software and/or
       hardware changes, caused by for example, but not limited to failure and
       subsequent repair or alterations of (virtualized) hardware/software, our
       software product will automatically attempt to contact our online licensing
       systems to renegotiate the licensing state. On any given license key, you
       are limited to three (3) automatic renegotiations within the license key
       lifetime. After these renegotiations are exhausted, the license key is
       considered invalid, and the activation state will be locked to the last
       valid system configuration it was activated on. OpenVPN Technologies, Inc.
       reserves the right to grant exceptions to this policy for license holders
       under extenuating circumstances, and such exceptions can be requested
       through a ticket via the OpenVPN Access Server ticketing system.
    9. Once an activated license key expires or becomes invalid, the concurrency
       limit on our software product will decrease by the amount of concurrent
       connections previously granted by the license key. If all of your purchased
       license key(s) have expired, the product will revert to demonstration mode,
       which allows a maximum of two (2) concurrent users to be connected to your
       server. Prior to your license expiration date(s), OpenVPN Technologies,
       Inc. will attempt to remind you to renew your license(s) by sending
       periodic email messages to the licensee email address on record. You are
       solely responsible for the timely renewal of your license key(s) prior to
       their expiration if continued operation is expected after the license
       expiration date(s). OpenVPN Technologies, Inc. will not be responsible for
       any misdirected and/or undeliverable email messages, nor does it have an
       obligation to contact you regarding your expiring license keys.
   10. Any valid license key holder is entitled to use our ticketing system for
       support questions or issues specifically related to the OpenVPN Access
       Server product. To file a ticket, go to our website at http://openvpn.net/
       and sign in using the account that was registered and used to purchase the
       license key(s). You can then access the support ticket system through our
       website and submit a support ticket. Tickets filed in the ticketing system
       are answered on a best-effort basis. OpenVPN Technologies, Inc. staff
       reserve the right to limit responses to users of our demo / expired
       licenses, as well as requests that substantively deviate from the OpenVPN
       Access Server product line. Tickets related to the open source version of
       OpenVPN will not be handled here.
   11. Purchasing a license key does not entitle you to any special rights or
       privileges, except the ones explicitly outlined in this user agreement.
       Unless otherwise arranged prior to your purchase with OpenVPN Technologies,
       Inc., software maintenance costs and terms are subject to change after your
       initial purchase without notice. In case of price decreases or special
       promotions, OpenVPN Technologies, Inc. will not retrospectively apply
       credits or price adjustments toward any licenses that have already been
       issued. Furthermore, no discounts will be given for license maintenance
       renewals unless this is specified in your contract with OpenVPN
       Technologies, Inc.

Please enter 'yes' to indicate your agreement [no]: yes

Once you provide a few initial configuration settings,
OpenVPN Access Server can be configured by accessing
its Admin Web UI using your Web browser.

Will this be the primary Access Server node?
(enter 'no' to configure as a backup or standby node)
> Press ENTER for default [yes]: yes

Please specify the network interface and IP address to be
used by the Admin Web UI:
(1) all interfaces: 0.0.0.0
(2) eth0: xx.xx.xx.xxx
(3) eth0: 10.16.0.5
Please enter the option number from the list above (1-3).
> Press Enter for default [2]: 1

Please specify the port number for the Admin Web UI.
> Press ENTER for default [943]: 

Please specify the TCP port number for the OpenVPN Daemon
> Press ENTER for default [443]: 

Should client traffic be routed by default through the VPN?
> Press ENTER for default [yes]: 

Should client DNS traffic be routed by default through the VPN?
> Press ENTER for default [yes]: 

Use local authentication via internal DB?
> Press ENTER for default [no]: 

Private subnets detected: ['10.16.0.0/18']

Should private subnets be accessible to clients by default?
> Press ENTER for default [yes]: 

To initially login to the Admin Web UI, you must use a
username and password that successfully authenticates you
with the host UNIX system (you can later modify the settings
so that RADIUS or LDAP is used for authentication instead).

You can login to the Admin Web UI as "openvpn" or specify
a different user account to use for this purpose.

Do you wish to login to the Admin UI as "openvpn"?
> Press ENTER for default [yes]: 

> Please specify your OpenVPN-AS license key (or leave blank to specify later): 


Initializing OpenVPN...
Adding new user login...
useradd -s /sbin/nologin "openvpn"
Writing as configuration file...
Perform sa init...
Wiping any previous userdb...
Creating default profile...
Modifying default profile...
Adding new user to userdb...
Modifying new user as superuser in userdb...
Getting hostname...
Hostname: ubuntu-512mb-lon1-01-london
Preparing web certificates...
Getting web user account...
Adding web group account...
Adding web group...
Adjusting license directory ownership...
Initializing confdb...
Generating init scripts...
Generating PAM config...
Generating init scripts auto command...
Starting openvpnas...

NOTE: Your system clock must be correct for OpenVPN Access Server
to perform correctly.  Please ensure that your time and date
are correct on this system.

Initial Configuration Complete!

You can now continue configuring OpenVPN Access Server by
directing your Web browser to this URL:

https://xx.xx.xx.xxx:943/admin
Login as "openvpn" with the same password used to authenticate
to this UNIX host.

During normal operation, OpenVPN AS can be accessed via these URLs:
Admin  UI: https://xx.xx.xx.xxx:943/admin
Client UI: https://xx.xx.xx.xxx:943/

See the Release Notes for this release at:
   http://www.openvpn.net/access-server/rn/openvpn_as_2_1_9.html

root@ubuntu-512mb-lon1-01-london:~# passwd openvpn
Enter new UNIX password: 
Retype new UNIX password: 
Sorry, passwords do not match
passwd: Authentication token manipulation error
passwd: password unchanged
root@ubuntu-512mb-lon1-01-london:~# exit
logout
Connection to xx.xx.xx.xxx closed.
cn .ssh $ 

# References
https://www.hak5.org/frontpage/how-to-build-an-openvpn-access-point-hak5-2017
