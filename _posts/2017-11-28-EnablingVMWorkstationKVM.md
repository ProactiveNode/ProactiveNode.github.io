---
layout: default
title: 'Using VMWare Workstation to create KVM'
excerpt: Quick post about allowing a VM in VMWare Workstation to create a KVM.
---
In VMWare workstation, there are a good amount of options you can choose from. To allow your Linux VM to create KVMs, you will need to navigate to VM --> Settings. Once you are in the settings, click on Processors. From there, you can see on your left side a checkbox labeled 'Virtualize Intel VT-x/EPT or AMD-V/RVI'. Click on the checkbox as shown in the image below and you will be able to use that Linux machine to create KVMs.

![VMWare Workstation Settings](/assets/virtual.PNG)
