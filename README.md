<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Video Demonstration</h2>

- ### [YouTube: How to Deploy on-premises Active Directory within Azure Compute](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (22H2)

<h2>Setup Resources in Azure</h2>

### In case you haven't done this in the previous Repositories this should help you do a quick catch up. For more details go to https://github.com/LamareMartin98/osticket-prereqs.git

**1.) The first thing you are going to want to do is create a virtual machine by going to https://portal.azure.com/. Setup your virtual machine with Windows 10 Pro, version 22H2. Note, you will want to create a virtual machine with atleast 2 vcpus and 16 gbs of memory.**

-  Create the Domain Controller VM (Windows Server 2022) named “DC-1”

      a.  Take note of the Resource Group and Virtual Network (Vnet) that get created at this time

Set Domain Controller’s NIC Private IP address to be static

Create the Client VM (Windows 10) named “Client-1”. Use the same Resource Group and Vnet that was created in Step 1.a

Ensure that both VMs are in the same Vnet (you can check the topology with Network Watcher

**2.) Once you have created your virtual machine you will want to connect to it by using the public ip address the vm is setup with. You will connect using the remote desktop connection app.**


### Ensure Connectivity between the client and Domain Controller

- Using the same Username and Password that you made with Client-1, login into Remote Desktop
   
  a. Go into the command menu, ( Windows Key (Command on Mac) + R, then type "cmd." Press "Enter" to open the regular Command Prompt, OR Ctrl + Shift + Enter to open as an Administrator. Another way is to press Windows Key (Command on Mac) + X, use the arrow keys to select "Command Prompt," then press Enter),
  b. ping DC-1’s private IP address with "ping -t <ip address> " (perpetual ping) which you will then see it fail.

- Login to the Domain Controller which is DC-!, using another window of remote desktop may make it easier, and enable ICMPv4 in on the local windows Firewall

- Check back at Client-1 to see the ping succeed, you may want to clear it first by typing into a new line "CLS" and the pressing enter


### Install Active Directory

Login to DC-1 and install Active Directory Domain Services

Promote as a DC: Setup a new forest as mydomain.com (can be anything, just remember what it is)

Restart and then log back into DC-1 as user: mydomain.com\labuser


### Create an Admin and Normal User Account in AD

In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called “_EMPLOYEES”

Create a new OU named “_ADMINS”

Create a new employee named “Jane Doe” (same password) with the username of “jane_admin”

Add jane_admin to the “Domain Admins” Security Group

Log out/close the Remote Desktop connection to DC-1 and log back in as “mydomain.com\jane_admin”

User jane_admin as your admin account from now on


### Join Client-1 to your domain (mydomain.com)

From the Azure Portal, set Client-1’s DNS settings to the DC’s Private IP address

From the Azure Portal, restart Client-1

Login to Client-1 (Remote Desktop) as the original local admin (labuser) and join it to the domain (computer will restart)

Login to the Domain Controller (Remote Desktop) and verify Client-1 shows up in Active Directory Users and Computers (ADUC)

inside the “Computers” container on the root of the domain

Create a new OU named “_CLIENTS” and drag Client-1 into there


### Setup Remote Desktop for non-administrative users on Client-1

Log into Client-1 as mydomain.com\jane_admin and open system properties

Click “Remote Desktop”

Allow “domain users” access to remote desktop

You can now log into Client-1 as a normal, non-administrative user now

Normally you’d want to do this with Group Policy that allows you to change MANY systems at once (maybe a future lab)


### Create a bunch of additional users and attempt to log into client-1 with one of the users

Login to DC-1 as jane_admin

Open PowerShell_ise as an administrator

Create a new File and paste the contents of the script into it (https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)

Run the script and observe the accounts being created

When finished, open ADUC and observe the accounts in the appropriate OU

attempt to log into Client-1 with one of the accounts (take note of the password in the script)

Finish.
