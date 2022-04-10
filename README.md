# Unifi Network Application

Latest available version is 7.0.23.

+ UniFi Network Application 7.0.23 for Debian/Ubuntu Linux and UniFi Cloud Key
+ [Release notes](https://community.ui.com/releases/UniFi-Network-Application-7-0-23/f1b404b4-f595-4346-aaa1-df6941e35525)
+ [How to install and update via APT on Debian or Ubuntu](https://help.ui.com/hc/en-us/articles/220066768-UniFi-How-to-Install-and-Update-via-APT-on-Debian-or-Ubuntu)

> Java 9 and later are not yet supported.
> We support **MongoDB 3.6** since Unifi 5.13.10, older UniFi Network Application versions only support up to MongoDB 3.4.
> Minimum supported device firmware for U6-Series devices is 5.29.0, UAP/USW is 4.0.9, and for USG it's 4.4.34.

> If you are installing in a VM or a headless server, you may encounter entropy issues. 
> This could be anything from slow service start/restart to complete service failure. 
> The fix is to install `haveged`.
> https://www.digitalocean.com/community/tutorials/how-to-setup-additional-entropy-for-cloud-servers-using-haveged

> As of UniFi Network Application version 5.9, **if using Cloud Access**, 
> the host system/device requires outbound `8883/tcp` to be open/unrestricted.

You can change some Unifi settings by editing `/usr/lib/unifi/data/system.properties`.

Unifi installs a systemd service `unifi.service`:
> To stop the UniFi service: `sudo systemctl stop unifi`
> To restart the UniFi service: `sudo systemctl start unifi`
> To see the status of UniFi service: `sudo systemctl status unifi`

**How to access the controller** (I have not configured any DNS records yet):
+ `https://<ip-address>:8443` - WORKS, with an insecure certificate warning
+ https://<domain-name>:8443 - does not work, because the domain uses HSTS so the browser disallows the connection since it is insecure

I have downloaded the Easy Install scripts by Glenn R. into the `files/` directory.
For now I'm not using them, just drawing inspiration from them.
Note that Glenn R. has published **two** scripts, one for
[*installing*](https://get.glennr.nl/unifi/install/install_latest/unifi-latest.sh)
and one for [*updating*](https://get.glennr.nl/unifi/update/unifi-update.sh) Unifi.



## Adopting Unifi devices (such as UAPs) to controller

Easiest way is to SSH into the Unifi device (you'll need username/password):

```
UBNT-BZ.v4.3.20# set-inform http://<ip-address>:8080/inform
Adoption request sent to 'http://<ip-address>:8080/inform'.  Use the controller to complete the adopt process.
```


## On migrating Unifi controller to a new host

+ https://www.smarthomebeginner.com/move-unifi-controller/
+ https://www.tim-kleyersburg.de/articles/unifi-network-for-homeoffice/
+ https://miketabor.com/install-ubiquiti-unifi-controller-cloud/
+ [Adoption methods](https://help.ui.com/hc/en-us/articles/204909754)
+ [Chrome extension "Device Discovery Tool"](https://chrome.google.com/webstore/detail/ubiquiti-device-discovery/hmpigflbjeapnknladcfphgkemopofig)



## Special considerations for arm64 architecture

> At the moment Ubiquiti does not support arm64, hence it is not available via the repo. 
> A download and manual installation will be necessary for this.
> https://community.ui.com/questions/apt-get-Unable-to-find-expected-entry-ubiquiti-binary-arm64-Packages/30a08d62-e5a9-49bb-b5f7-fa4f55bdd17f  

I found what looks like good instructions here:  
https://station.eciton.net/running-unifi-controller-on-arm64-or-ppc64el.html  
(my own implementation in this role is heavily based on this blogpost).



## Log file location

```
/usr/lib/unifi/logs/server.log
/usr/lib/unifi/logs/mongod.log
```



## localhost line in /etc/hosts

The line `127.0.0.1 localhost` (arbitrary spaces) should exist in your `/etc/hosts` file, 
otherwise you will most likely see controller startup issues.

Our `hostname` role *assumes* that this line already exists in the file. 
I have not added any check for this line.



## Settings in the Network Controller

Notes on some particularly tricky settings.

**DNS server** settings can be set for both WAN and LAN (DHCP server).
It seems our "old" behaviour is best emulated by setting DNS for LAN,
but setting DNS for WAN might also be useful.
https://reddit.com/r/Ubiquiti/comments/9qgl1w/settings_networks_wan_dns_server/

It was *necessary* to use multiple **sites** in order to manage two networks
with two separate routers (I tried without using sites, but then nothing made sense).



## Refs

+ https://www.ui.com/download/unifi/
+ https://community.ui.com/releases/UniFi-Network-Application-6-5-54/d717f241-48bb-4979-8b10-99db36ddabe1
+ https://help.ui.com/hc/en-us/articles/220066768-UniFi-How-to-Install-and-Update-via-APT-on-Debian-or-Ubuntu
+ https://community.ui.com/questions/UniFi-Installation-Scripts-or-UniFi-Easy-Update-Script-or-UniFi-Lets-Encrypt-or-Ubuntu-16-04-18-04-/ccbc7530-dd61-40a7-82ec-22b17f027776
+ https://reddit.com/r/Ubiquiti/comments/da6dpq/setup_unms_and_unifi_network_controller_on/
+ https://gist.github.com/codeniko/381e8be3b0236a602e02f0a9fac13b3d
