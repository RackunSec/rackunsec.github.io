---
layout: post
title: Kerberos Within Linux Browser 
subtitle: How to use kerberos Authentication for web applications in Linux
cover-img: /assets/img/screenshots/code-bg.jpg
thumbnail-img: /assets/img/screenshots/kerberos.png
share-img: /assets/img/screenshots/kerberos.png
gh-badge: follow
tags: [demon linux,linux, kerberos]
comments: true
---
# Kerberizer?
I was on a penetration test recently in which the target web application used SSO and a kerberos ticket for authentication. The authentication for the web application was done using a cutomized version of [Kerberizer](https://kerberizer.sourceforge.net/). 

My client sent me a computer to use for the test which connected to their domain via VPN. Once connected, the established session had a valid Kerberos ticket and the browser was automatically set to use this for the target web application. 
The probalem that I had was that the system had Crowdstrike installed which disagreed with a lot of the tools that I normally use during a web application penetation test. Plus it was a Windows system. That's always a problem for me but I digress. I *was* allowed to install VirtualBox and use a VM, however.

So, to get the Windows/Kerberos session into my VirtualBox was actually quite easy, but I muffed up in the process during my initial attempt. 
I naively thought, "*why not just use [lsassy](https://github.com/Hackndo/lsassy) and extract the current ticket from my host? I mean, what could go wrong?* Crowdstrike put an end to the testing for that day. To be fair, this is actually how I learned that CS was installed on this system. 

Embarassed, but determined, I tried the next day with some more research. *"How could I extract the ticket in the Windows host safely and place it int my VM?"* Then the obvious dawned on me: I was in the client's domain. Derp. Sometimes, I just can't come up with excuses for why I do some things. 
## Step 1 - Identify the Domain Name
From the Windows host, I ran the following command in a DOS terminal:
```dos
ipconfig /all
```
This command told me the domain name I was conencted to. 
## Step 2 - Identify the Domain Controller(s) 
Then, I ran:
```dos
nltest /dclist:(DOMAIN NAME)
```
Substitute the `(DOMAIN NAME)` for your domain. This command told me the domain controllers used in the client's domain (which, surprisingly was just 1 domain controller). Then, I got the DC IP address with:
```dos
nslookup (DC NAME).(DOMAIN NAME)
```
With the DC name and IP address, I went to my VM and added them into my `/etc/hosts` file:
```bash
(IP ADDRESS) (FQDN OF DC)
```
## Step 3 - Test the DC and your Creds
I used [CrackMapExec](https://github.com/Porchetta-Industries/CrackMapExec) to test my credentials to the domain controller like so:
```bash
crackmapexec smb (FQDN OF DC) -u (USERNAME) -d (DOMAIN NAME) -p (PASSWORD) 
```
## Step 4 - Get a Kerberos Ticket
I got a success message, which meant I could query the DC directly, so why not use [Impacket](https://github.com/SecureAuthCorp/impacket)'s `getTGT.py`? I used the following command to get the ticket into a `.ccache` file:
```bash
python3 getTGT.py -dc-ip (DC IP ADDRESS) (DOMAIN NAME)/(USERNAME)
```
## Step 5 - Test the Ticket
This saved the file to disk as `(USERNAME).ccache`. Great, now what? Well, I recommend testing the ticket first before attempting to use it in the browser. I used CrackMapExec again with the `-k` and `-kdcHost` options, which failed left and right. It wasn't until [Tw1sm](https://tw1sm.github.io) told me that CME has issues with Kerberos tickets and to try an Impacket tool for authentication that if finally worked for me. 
```bash
export KRB5CCNAME=/path/to/my/(USERNAME).ccache
python3 smbclient.py -k -no-pass (DC FQDN)
```
Just to note: There are two ways that I know of that we can cache a kerberos ticket for use with Linux:
 1. Export the ccache file with `export KRB5CCNAME=/path/to/your/(USERNAME).ccache`
 2. Use the actual Kerberos utilities to cache the ticket to be used by Firefox. 

To install the Kerberos utilities in Debian/Kali Linux, I used the following command:
```bash
apt install krb5-pkinit krb5-auth-dialog heimdal-clients
```
During the installation, you will be asked your default domain name, which you obtained from the DOS commands in Windows if you were following along. Once installed, I verified that no tickets were currently cached: 
```
klist
```
When I ran `export KRB5CCNAME=/path/to/your/(USERNAME).ccache`, the output of `klist` changed and I saw my ticket ready to use. To cache a ticket with the kerberos utilities, run:
```bash
kinit
```
You will be prompted for your username and password and the ticket will now show when you use `klist`
## Step 6 - Configure Firefox to Use the Ticket(s)
Open Firefox using the same shell you cached your ticket in:
```bash
firefox-esr
```
Once open, browse to `about:config` and accept the warning. In the search filter bar search `negotiate` and you will see about half a dozen entries show up. The two that we are going to use are:
1. `network.negotiate-auth.delegation-uris`
2. `network.negotiate-auth.trusted-uris`

The values of these fields SHOULD match that of the fields in your Windows host. If you are using another browser, or do not know how to find these values, you can use wildcard URLs like so,
```
https://.(DOMAIN NAME)
```
## That's All, Folks
This was all it took for me to successfully login to the target web application using the kerberos ticket that Impacket obtained for me. If you are on a network penetration test and you obtain Kerberos tickets in memory using [Rubeus](https://github.com/GhostPack/Rubeus) or some other method, you may be able to use this technique to establish authentication from your implant/drop box to internal web applications that require SSO/Kerberos. 







