<properties
	pageTitle="How to update Azure Linux Agent "
	description="Learn how to update Azure Linux Agent from Github for your Linux VM in Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="3/23/2015"
	ms.author="mingzhan"/>


#How to update Azure Linux Agent to latest version from Github
In this guidance, it is assumed that the reader already has a Linux VM in Azure. If not it is recommended to sign up and get a Linux VM by visiting [Azure](http://azure.microsoft.com).It is assumed that the reader could already connect to Linux VM via SSH, and have a putty terminal connected to Linux VM. if not, see [here](http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-how-to-log-on/).NOTE: Most of situations, each Azure endorsed Distro has put the package of Azure Linux Agent in its reposition, we will recommend you check and install the latest version from Distro repository first:For Ubuntu:         #sudo apt-get install waagentFor CentOS, Oracle Linux    #sudo yum install waagentIf above is not helpful, we could update from Github. ##PreparationCheck your current version of Azure Linux Agent from Linux VM:    #waagent -versionInstall wget(some old distros didn't install it by default, you need install it first) like 6.4, 6.5 of Redhat, CentOS and Oracle Linux:    #sudo yum install wgetOpen [the release of Azure Linux Agent in Github](https://github.com/Azure/WALinuxAgent/releases) in a web page, and find out the latest version number like: 2.0.12.##ProcedureLogin your Linux VM via putty terminal.###Download latest version:    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  Use version 2.0.12 as an example:    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.12/waagent  ###Add executable permission to waagent file:    #chmod +x waagent###Replace old waagent file under /usr/sbin/:    #sudo cp waagent /usr/sbinFor CoreOS, use:    #sudo cp waagent /usr/share/oem/bin/ ###Restart waagent service:For most of linux distro:    #sudo service waagent restartFor ubuntu, use:    #sudo service walinuxagent restartFor CoreOS, use:    #sudo systemctl restart waagent ###The Azure Linux Agent is updated to new version right now, check the version:    #waagent -versionFor CoreOS, above command may not work.You will see the Linux Agent version has been updated to new version.For more information regarding Azure Linux Agent, refer [Azure Linux Agent Guide](http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-agent-user-guide/).