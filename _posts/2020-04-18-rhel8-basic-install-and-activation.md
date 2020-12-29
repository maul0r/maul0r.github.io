---
title: "Getting the basics right - RHEL 8.1 installation"
date: 2020-03-18T12:00:00
excerpt: "Basic RHEL8.1 installation, activation, autoupdate, cockpit setup"
categories:
  - blog
tags:
  - rhel
  - rhel8
  - howto
  - basic
---

Red Hat Enterprise Linux is a linux distrubtion aimed at companies who value the full supportability and the assurance that comes with it. While build on open-source any service associated with (ie. updates) will not work until you pay the company. In return you get very long supportability and a very stable OS.

I have been wanting to setup a [Zabbix][zabbix-home] monitoring server at work, but Zabbix seems to have a lot dependencies. I therefore installed RedHat for the first time after I read that RedHat now has a [free developer subscription][rhel-dev-annoucement]. This is the perfect time to expand my horizon a bit and therefore got myself the subscritption and started spinning up some VMs. This is really not difficut, so what I want to note down here is what I changed from the standard install to get a VM that has the settings I need in an attempt to memorize this stuff and to avoid having to Goolge-Fu it everytime I do it. Settings I want changed are:

- [ ] Correct hostname
- [ ] Correct time configuration
- [ ] an active subscription 
- [ ] current updates
- [ ] [Cockpit][cockpit-project] installed and started  

When going through the installation, you can already change a lot of these things and I will. However, I will also write down how to accomplish and verify these tasks in the command line. I will start with a minimal install on VMware ESXi 6.7 U3. 

I always install on the US locale with US keyboard layout. On the next screen you will see the setup parameters. The only options I touch here marked with a red box. 

<figure>
	<a href="{{ site.url }}{{ site.baseurl }}/assets/2020-04-18/2020-04-18-rhel8-basic-install-and-activation-screen1.png"><img src="{{ site.url }}{{ site.baseurl }}/assets/2020-04-18/2020-04-18-rhel8-basic-install-and-activation-screen1.png" width="100%%" height="100%"></a>
	<figcaption>Figure 1: Installation Menu Red Hat 8.1</figcaption>
</figure>

First the **Network & Hostname** section will be configured. Without that, some NTP settings won't be available. 

<figure>
	<a href="{{ site.url }}{{ site.baseurl }}/assets/2020-04-18/2020-04-18-rhel8-basic-install-and-activation-screen2.png"><img src="{{ site.url }}{{ site.baseurl }}/assets/2020-04-18/2020-04-18-rhel8-basic-install-and-activation-screen2.png"></a>
	<figcaption>Figure 2: Network & Hostname Section Red Hat 8.1 Setup</figcaption>
</figure>

Enable the network connection at the top right of the screen. If you have DHCP configured, wait until a lease is assigned and will be shown under the Ethernet icon. The screenshot has this information redacted. If a static IP needs to be used, click on the **Configure** button. You can also change your **Hostname** here, but I will do this later, to show how it is done. Click **Done**. 

Next the System Package will be specified in **Software Selection**. 

<figure>
	<a href="{{ site.url }}{{ site.baseurl }}/assets/2020-04-18/2020-04-18-rhel8-basic-install-and-activation-screen3.png"><img src="{{ site.url }}{{ site.baseurl }}/assets/2020-04-18/2020-04-18-rhel8-basic-install-and-activation-screen3.png"></a>
	<figcaption>Figure 3: Software Selection Red Hat 8.1 Setup</figcaption>
</figure>

I want a sleek and tiny OS, therefore I choose **Minimal Install**. This will not include the [Cockpit][rhel-cockpit] installation, but this can be added later. Click **Done** again to return to the setup screen shown in Figure 1. 

Next the **Time & Date** section will be configured. Click it and the screen depicted in Figure 4 will be shown. 

<figure>
	<a href="{{ site.url }}{{ site.baseurl }}/assets/2020-04-18/2020-04-18-rhel8-basic-install-and-activation-screen4.png"><img src="{{ site.url }}{{ site.baseurl }}/assets/2020-04-18/2020-04-18-rhel8-basic-install-and-activation-screen4.png"></a>
	<figcaption>Figure 4: Time & Date Configuration Red Hat 8.1 Setup</figcaption>
</figure>

At the top right, change **Region** and **City** to suit your needs. In my example, since I live in Germany, ``Europe`` and ``Berlin`` was chosen. The next step is personal prefence of course, but I prefer to use th **24 hour format** so I ensure it is selected. Cick the wheels next to **Network Time** and if you like, you can enter your own NTP server of choice here - RedHat does provide a default. I use ``de.pool.ntp.org``, indicate that it is a pool of NTP servers and click **In Use**. I also remove the check mark from the default RedHat entry. When all of this has been configured, click **Done** at the top right of the page again. 

Lastly I will confirm the default partition layout. Usually, nothing fancy is needed and unless there are special needs, you can do the same. 

<figure>
	<a href="{{ site.url }}{{ site.baseurl }}/assets/2020-04-18/2020-04-18-rhel8-basic-install-and-activation-screen5.png"><img src="{{ site.url }}{{ site.baseurl }}/assets/2020-04-18/2020-04-18-rhel8-basic-install-and-activation-screen5.png"></a>
	<figcaption>Figure 5: Partioning Red Hat 8.1 Setup</figcaption>
</figure>

Even though I don't change anything, I need to enter the **Installation Destination** section of the setup and click **Done**. 

**Watch out!** If this is not a clean, fresh install, make sure that you don't format the wrong disk. **You will loose data!**
{: .notice--danger}

When this has been done, the **Beginn Installation** button becomes available and clickable. Click it to actually beginn the installation process. 

<figure>
	<a href="{{ site.url }}{{ site.baseurl }}/assets/2020-04-18/2020-04-18-rhel8-basic-install-and-activation-screen6.png"><img src="{{ site.url }}{{ site.baseurl }}/assets/2020-04-18/2020-04-18-rhel8-basic-install-and-activation-screen6.png"></a>
	<figcaption>Figure 6: Create Initial User Accounts 8.1 Setup</figcaption>
</figure>

While the installation is proceeding, the setup routine provides the opportunity to assign a password for the **root** user and for a normal user. Let's start with the root user by clicking **Root Password**. Make this password super secure, yet memorable. Root = god! Click **Done**. 

The next steop is not mandatory, but stronlgy reccomended, create a standard user account. Root should only be used as a last resort. Click **User Creation**. 

<figure>
	<a href="{{ site.url }}{{ site.baseurl }}/assets/2020-04-18/2020-04-18-rhel8-basic-install-and-activation-screen7.png"><img src="{{ site.url }}{{ site.baseurl }}/assets/2020-04-18/2020-04-18-rhel8-basic-install-and-activation-screen7.png"></a>
	<figcaption>Figure 7: Create Standard User 8.1 Setup</figcaption>
</figure>

Type in your **Full Name**, a **User Name** which might be the same as the prevous field or a nickname. If you select the **Make this user administrator** you enable the user to perform [_sudo_][sudo-linuxadacemdy] tasks. Regardless, choose another super secure password and do not reuse the root password. You can do more by clicking the **Advanced** button, like changing your home directory or changing the UID, GUID the user belongs to or add the user to additional security groups. I didn't do any of that. 

By now the installation may be done already, in which case the bottom left of the screen will sport a **Finish Configuration** button. The system will now configure itself for first use. 

Once done, the lower right will show a **Reboot* Button. Click that. The system will reboot now and we can start using the fresh install. 

Log in with the standard user. The first thing we should do, if we didn't assign a static IP, is to find the IP of the system: 
```
[user@hostname ~]$ ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens192: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:9f:14:67 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.26/24 brd 192.168.0.255 scope global dynamic noprefixroute ens192
       valid_lft 86217sec preferred_lft 86217sec
    inet6 fe80::d3af:5a5d:6f66:823e/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```
In this case the ip is ```inet 192.168.0.26/24```. The SSH server is enabled by default, so I can connect to it straight away using ```user@192.168.0.26```. 

Looking back on our To-Do list, the first item is the hostname. the current hostname is shown in the beginning of the [bash][bash-academy] prompt. It can also be shown using these three commands

```
[user@hostname ~]$ cat /etc/hostname
localhost.localdomain

[user@hostname ~]$ hostname
localhost.localdomain

[user@hostname ~]$ hostnamectl
   Static hostname: localhost.localdomain
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 3abb006d1e414bb8b91b5ec40f1b6203
           Boot ID: 4805905157cd46c78a9e4b0346ce9c72
    Virtualization: vmware
  Operating System: Red Hat Enterprise Linux 8.1 (Ootpa)
       CPE OS Name: cpe:/o:redhat:enterprise_linux:8.1:GA
            Kernel: Linux 4.18.0-147.el8.x86_64
      Architecture: x86-64
```
Since RedHat uses [systemd][systemd-wiki] for daemon and service management, we can use the ```hostnamectl``` command to change the hostname.

```
[user@hostname ~]$sudo hostnamectl set-hostname test-vm

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

[sudo] password for user:

[user@hostname ~]$ hostnamectl
   Static hostname: test-vm
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 3abb006d1e414bb8b91b5ec40f1b6203
           Boot ID: 4805905157cd46c78a9e4b0346ce9c72
    Virtualization: vmware
  Operating System: Red Hat Enterprise Linux 8.1 (Ootpa)
       CPE OS Name: cpe:/o:redhat:enterprise_linux:8.1:GA
            Kernel: Linux 4.18.0-147.el8.x86_64
      Architecture: x86-64
```
Notice how the command prompt prefix does not change. It will after a reboot. There we can now say: 

- [X] Correct hostname

Next up, the time configuration. We first start with NTP. We have done this in the setup already, but lets verify and see how we would this via the command line. 

> In Red Hat Enterprise Linux 8, the NTP protocol is implemented by the chronyd daemon, available from
the repositories in the chrony package. [...] In Red Hat Enterprise Linux 8, ntp is no longer supported. chrony is enabled by default. For this reason, you might need to migrate from ntp to chrony.
<cite>RHEL8 - [Basic Admin Guide][rhel8-admin-guide-ntp]</cite>
{: .small}

While the ***chronyc*** application can be used to manipulate settings, they will not be persitant. Once the ***chronyd*** service is restart, the settings from ```/etc/chrony.conf``` will be used. Therefore, in order to make permanent changes, this configuration file will need to be edited. 
The NTP server we configured during the setup routine is listed in the config file and can be shown via ```$ head -2 /etc/chrony.conf```. 
```
$ head -2 /etc/chrony.conf
# These servers were defined in the installation:
pool de.pool.ntp.org iburst
```
The command ```chronyc sources -v``` shows the NTP servers we are connected to and the ```-v``` flag indicates verbose output. This will explain each position of the output like so: 
```
$ chronyc sources -v
210 Number of sources = 4

  .-- Source mode  '^' = server, '=' = peer, '#' = local clock.
 / .- Source state '*' = current synced, '+' = combined , '-' = not combined,
| /   '?' = unreachable, 'x' = time may be in error, '~' = time too variable.
||                                                 .- xxxx [ yyyy ] +/- zzzz
||      Reachability register (octal) -.           |  xxxx = adjusted offset,
||      Log2(Polling interval) --.      |          |  yyyy = measured offset,
||                                \     |          |  zzzz = estimated error.
||                                 |    |           \
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^+ vmhost-external-minor.wo>     2   9   377   477  +1960us[+2103us] +/-   65ms
^* static.116.231.130.94.cl>     2  10   377   283   -373us[ -233us] +/-   39ms
^+ time01.leardev.de             2  10   377   472  +1717us[+1860us] +/-   33ms
^+ de.danzuck.eu                 2  10   377   469  -2378us[-2236us] +/-   50ms
```

If a change is done to any setting within Crony, you need to also restart the systemd managed timedate deamon. 
```systemctl restart systemd-timedated.service```

We will also use this service to list, change and verify the timeconfiguration. If you don't who the format of your timezone you can get started by entering ```timedatectl list-timezones```. This will provide a list for you that you can choose from. 

In order to change the timezone and then verify it use the ***set-timezone*** command. Verify by only using the ```timedatectl``` command. 

```
[root@zabbix ~]# timedatectl set-timezone Europe/Berlin
[root@zabbix ~]# timedatectl
               Local time: Fri 2020-03-27 22:08:43 CET
           Universal time: Fri 2020-03-27 21:08:43 UTC
                 RTC time: Fri 2020-03-27 21:08:43
                Time zone: Europe/Berlin (CET, +0100)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```
Now we have verified time synchronization and we have ensure that we are in the right timezone. 

- [X] Correct time configuration

Now we need to register the system, in order to start using the RedHat repositories and update our linux. RedHat provides an application for that called ***subscription-manager*** and you can use it to first register the installation and then attach it to a subscription. The quick and dirty way is to do it in one step like so: 

```
subscription-manager register --username <username> --password <password> --auto-attach
```

This is what I will use, I this is a lab. There is an KB by RedHat [here][[rhel-subscription-manager], that will provide information for additional scenarios. 

```
# subscription-manager register --username <username> --password <password> --auto-attach
Registering to: subscription.rhsm.redhat.com:443/subscription
The system has been registered with ID: XXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX
The registered system name is: <hostname>
Installed Product Current Status:
Product Name: Red Hat Enterprise Linux for x86_64
Status:       Subscribed
```
- [X] an active subscription 

Next up, the installation has to be secured by use of updates. Package management used to be done with [yum][yum], however, the more performant [dnf][dnf] is becoming the standard. To update simply type ```dnf update```. You can append a ```-y``` to automatically download and install. This might take a while 

- [X] current updates






[zabbix-home]: https://www.zabbix.com/
[rhel-dev-annoucement]: https://developers.redhat.com/blog/2016/03/31/no-cost-rhel-developer-subscription-now-available/
[cockpit-project]: https://cockpit-project.org/
[rhel-cockpit]: https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/managing_systems_using_the_rhel_8_web_console/index
[sudo-linuxadacemdy]: https://linuxacademy.com/blog/linux/linux-commands-for-beginners-sudo/
[bash-academy]: https://guide.bash.academy/
[systemd-wiki]: https://en.wikipedia.org/wiki/Systemd
[rhel8-admin-guide-ntp]: https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/configuring_basic_system_settings/index#using-chrony-to-configure-ntp
[rhel-subscription-manager]: https://access.redhat.com/solutions/253273
[yum]: https://linux.die.net/man/8/yum
[dnf]: https://dnf.readthedocs.io/en/latest/command_ref.html

