---
layout: post
title: Demon Linux 3.4
subtitle: Penetration Testing Debian Distro
cover-img: /assets/img/screenshots/code-bg.jpg
thumbnail-img: /assets/img/screenshots/demon-3.4-clouds.png
share-img: /assets/img/screenshots/demon-3.4-clouds.png
gh-badge: follow
tags: [demon linux,linux]
comments: true
---
## Penetration Testing
Demon Linux is a penetration tester-themed distribution of Debian Linux. This project is a new spin on the WeakNet Linux pentesting distribution that I began in 2008. This distribution was built out of the frustrations that I had with current off-the-shelf penetration testing distributions. Demon Linux is also a packaged environment in which I can distribute all of my own custom tools. Such as the Demon Pentest Shell and the Demon App Store. 

The distribution manages all penetration tools individually rather than using aptitude. I have created a huge menu to use to help quickly navigate your way through the distro and mostly all of the penetration testing tools are located in the ```/cyberpunk``` directory. I also designed a [Demon App Store](rackunsec/Demon-App-Store) in which you can easily hekout new tools at your leisure. If you discover a new tool or find one that you use on a daily basis is missing, simply send it over to me for review and I will add it to the store.

![Demon Linux 3.4.x Screenshot](/assets/img/screenshots/demon-3.4-clouds.png "Demon Linux 3.4.x Screenshot")
_Figure 0x0: Demon Linux desktop environment screenshot_
## Security
The latest version of my [Custom Debian Installer](RackunSec/Demon-Linux-Installer) allows you to set a secure root password showing you password stats, and alos allows yous to encrypt your entire drive using [LUKS](https://en.wikipedia.org/wiki/Linux_Unified_Key_Setup).

![Demon Linux 3.4.x LightDM Screenshot](/assets/img/screenshots/demon-3-4-lightdm.PNG)
_Figure 0x1: Demon Linux LightDM Screenshot_

## Download and Installation
You can download a copy of the Demon Linux ISO [from my website here](https://demonlinux.com). Installation is pretty straight forward, but ensure that you are only installing it within VMWare as I cannot support all hardware for systems all over the world. Keeping your penetration testing distribution containerized with VMWare is a convenient way to restore it and shred sensitive information after each penetration test you perform for your clients.



