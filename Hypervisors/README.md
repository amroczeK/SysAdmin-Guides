# VMware-vSphere-Hypervisor-Setup

### This guide explains the process of setting up the vSphere Hypervisor OS/Server, vSphere client and installation of Windows Server 2012 R2 on a Virtual Machine.

References: https://www.youtube.com/watch?v=Hm7kQHI4YnM&index=1&list=FLieEHWpE1PAq4zbOR3E66YQ&t=832s

NOTE: IP Address configuration isn't ideal, it's just what is suitable for my home network.

## Step 1: Bootable device & Troubleshooting
Use RUFUS to create a bootable USB using the VMware vSphere Hypervisor 6.0 ISO downloaded from the VMware website.

Note: If you have a Realtek network adapter, you might receive the error: "nfs41client failed to load" during installation/setup of the ESXi server, this is caused by unrecognized network adapter/drivers. To fix this you will need to inject the Realtek network adapter drivers into your vSphere Hypervisor ISO. Follow this guide: http://www.computertechblog.com/adding-realtek-r8168-driver-to-an-esxi-6-0-iso/

Note: If you are using Windows 10, the ISO customizer software linked above will need to be edited in notepad. Follow this guide: http://www.robsblog.co/2015/11/using-esxi-customizer-on-windows-10/

## Step 2: Installation & Network configuration
During installation, setup desired username and password.

Next "Configure Management Network". By default the IP, Subnet, Gateway and DNS will automatically be filled via DHCP, but it is better practice to set this up yourself based on your needs.

My setup:

IP: 192.168.1.254

Subnet: 255.255.255.0

Gateway: 192.168.1.1

DNS: 192.168.1.1

Hostname: esxi

DNS Suffix: esxi.lab.local

Finally test the network -> "Test management network" -> Enter
Note: Hostname will fail due to no reverse/forward lookup, unless you have that setup.

## Step 3: VMware vSphere installation
Install VMWare vSphere Client 6.0

If this is not installed, while your ESXi server is on, go to https://esxi and you can download it through that webpage.

### The following Steps 4-6 are for being able to SSH into the server using Putty, these steps can be skipped.

## Step 4: Putty configuration
Navigate to "C:\Windows\System32\drivers\etc" manually or via command prompt and open the "host" file in notepad to edit the file.

Add the following lines:

192.168.1.10  esxi.lab.local  esxi

192.168.1.11  vcenter.lab.local vcenter (you can skip doing adding this)

Test: Open cmd.exe (command prompt) and "ping esxi" or "ping 192.168.1.10" to test the connection.

Next create a Putty session to hostname = esxi and save it.

## Step 5: SSH configuration in vSphere client
To allow SSH connections to your ESXi server, login to your VMWare vSphere Client and do the following:

1. Select your server
2. Go to Configuration -> Security Profile -> Properties -> SSH -> Options -> Start service -> Change to Start & Stop with Host -> OK

Next remove Putty warnings in vSphere by:

1. Go to Advanced Settings -> User Vars
2. Set "SuppressShellWarnings" to equal 1

## Step 6: Edit host file in ESXi server using Putty
1. Create a session to ESXi server using Putty and login
2. Type "cd /etc" and press Enter
3. Type "cp hosts hosts.orig" and press Enter
4. Type "vi hosts" to open the file in vi and enable you to edit it
5. Rename domain / hosts, DNS & Routing -> "esxi.lab.local"
6. Add "192.168.1.11 vcenter.lab.local vcenter" if you are going to use the web interface
7. To save changes -> type ":q" and follow prompts
8. Type "cat hosts" to check if changes made are correct

## Step 7: Time configuration for ESXi server
1. Go to Setup Time Configuration -> Properties
2. enable "NTP Client"
3. Go to Options -> NTP Settings
4. Add "pool.ntp.org"
5. Next restart NTP -> Apply -> Start & Stop with host

Note: It will take a few minutes to take effect, refresh every so often.

