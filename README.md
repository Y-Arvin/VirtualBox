# VirtualBox & Active Directory

## Objective

The VirtualBox & Active Directory project aims to establish a controlled environment for simulating virtual machines. The primary focus was to set up a lab environment for future lab projects & to enhance my skills in virtualization, networking, and system administration. This hands-on experience was designed to serve for testing and development purposes without the need for physical hardware.

### Skills Learned

- Virtualization Fundamentals: VM creation, ISO files, SHA256 integrity hash, and resource allocation.
- Network Configuration: Configuring IP addresses, subnetting, and DNS settings within virtual networks.
- System Administration: Installing and configuring operating systems (Windows 10, Windows Server, Ubuntu, and Kali Linux) on virtual machines. Adding & managing user accounts, permissions, and group policies such as resetting passwords, account lockout, auto-updates, and enabling remote desktop.

### Tools Used

- Hypervisor Software: [Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads) Utilized for the creating, managing, and running virtual machines on a host system.
- Operating System Images: [Windows 10 ISO](https://www.microsoft.com/en-ca/software-download/windows10), [Windows Server 2022 ISO](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2022), [Ubuntu Server 24.04](https://ubuntu.com/download/server), and [Kali Linux](https://www.kali.org/get-kali/#kali-virtual-machines)
  
## Walkthrough
Before starting, here is a network diagram that will be referred to throughout the project.

<img src="https://i.imgur.com/rBM0iXH_d.jpg?maxwidth=550&shape=thumb&fidelity=high" > <br> <sup> Ref 1: Network Diagram; Splunk represents Ubuntu server, ActiveDirectory represents Windows server, Kali represents Linux machine, Windows User represents Windows 10 </sup>	

**Installing ISO files and performing an SHA256 hash** <br>
Install all necessary files to run VirtualBox, ISO files for Windows 10, Windows Server 2022, Ubuntu Server, and Kali Linux.<br>
A hash check is done on localhost to ensure the integrity of each file. In Windows Powershell running the command, pointing the path of the executable file. 
```bash
Get-Filehash 
```

<img src="https://i.imgur.com/nD22Dy7.png" width="600"> <br> <sup>Ref 2: SHA256 hash check for VirtualBox  </sup>	

**Loading ISO files into Virtualbox** <br>
Open VirtualBox, press the new icon at the top and fill out the dialog box, select the ISO file image, click next. This is where you will allocate resources for each VM's specifications such as the amount of memory and processors. This will be repeated for Windows 10, Windows Server, Ubuntu, and Linux. 

<img src="https://i.imgur.com/HrMzIr8.png" width="600"> <br> <sup>Ref 3: ISO files for the creation of VM in VirtualBox </sup>	

<img src="https://i.imgur.com/Asxprog.png" width="600"> <br> <sup>Ref 4: VirtualBox manager should look similar to this. Windows 10, Windows Server, Ubuntu, and Linux in VirtualBox </sup>	 

**Configure network settings to be on the same network** <br>
On the tools tab in Virtualbox Manager click the three dots and select network. Go to the NAT networks tab and press green plus create at the top. Choose a name`ADproject` and input an IPv4 network with the CIDR.`192.168.10.0/24` based on Ref<sup>1</sup>.<br>
Now attach the network to each VM by pressing the VM and then the yellow wheel settings icon at the top. Go to the network tab and in the dropdown box select `NAT NETWORK`; the name dropdown box should be the one created `ADproject`.

<img src="https://i.imgur.com/gd8TgGh.png" width="380"> <img src="https://i.imgur.com/XObxRIw.png" width="300"><br>
<img src="https://i.imgur.com/bqHnfTG.png" > <br>
<sup>Ref 5: Create NAT network and attach to each VM </sup>	

**Installation of Operating Systems** <br>
On VirtualBox press the green arrow start button for Windows 10, Windows Server, Linux & Ubuntu. This will turn on the systems and begin the installation of each operating systems.

<img src="https://i.imgur.com/1RdfRDy.png" width="600"> <br> <sup>Ref 6: Installation of Windows 10 & Windows Server operating system </sup>	

**Apply static IP address. Each VM's IPv4 address will reflect the network diagram**

**Ubuntu Server** <br>
Edit the file in `/etc/netplan/50-cloud-init.yaml` add the IPv4, DNS and save the changes.<br> To apply the changes type in the command.
```bash
$ sudo nano /etc/netplan/50-cloud-init.yaml
$ sudo netplan apply
```

<img src="https://i.imgur.com/n1BDSjW.png" width="600"> <br> <sup>Ref 7: Add IP to Ubuntu, apply changes, ping google.com to test connection </sup>	

**Windows 10 & Windows Server** <br>
Press the `Windows key + R` to open up run. Type in `ncpa.cpl` and press ok. <br> Right-click the PC icon and go into properties, double-click internet protocol version 4.<br>This is where you will type in the IPv4 address, subnet mask, default gateway, and DNS server.

<img src="https://i.imgur.com/IJxCoUO.png" width="320"> <img src="https://i.imgur.com/rfxXO76.png" width="320"><br>
<sup>Ref 8: IP 192.168.10.100/24 for Windows 10 & IP 192.168.10.7/24 fro Windows Server with gateway of 192.168.10.1 & DNS of 8.8.8.8</sup>	  

**Kali Linux** <br>
Right-click the ethernet icon and click edit connections. Select Wired connection1 and press the cog icon at the bottom. <br>Click the IPv4 settings tab and on the methods drop-down box switch to manual then press add. <br>For the IPv4 changes to apply disconnect and reconnect the ethernet connection. 

<img src="https://i.imgur.com/0etm7g2.png" width="600"> <br> <sup>Ref 9: IP 192.168.10.250 Kali  </sup>	 

**Setting up Active Directory on Windows Server** <br>
Open up Server Manager. Select the manage tab at the top and click Add Roles And Features. In the installation type section make sure role-base is selected. In the server roles section check the box that says Active Directory Domain Services.

<img src="https://i.imgur.com/PeyyZin.png" width="500"> <img src="https://i.imgur.com/l0eTiAD.png" width="465"> <br>
<sup>Ref 10: Adding Active Directory Domain Services to server </sup>	

**Promote to Domain Controller** <br>
Click the ⚠️ icon at the top then click promote this server to a domain controller.
<img src="https://i.imgur.com/JRHVuA0.png" width="600"> <br> <sup>Ref 11: Promote the server to domain controller </sup>	

Select add a new forest as this is creating a new domain. The domain name must have a top-level domain.<br>
The server will require a restart once the installation has been finished.

<img src="https://i.imgur.com/TiiiMxl.png" width="500"> <img src="https://i.imgur.com/oyx93mA.png" width="500"> <br> <sup>Ref 12: Configuration new forest & root domain name </sup><br>
<sup> Active Directory Domain Services & Promoted to domain controller  </sup>	

**Create a new Organizational Unit called IT** <br>
Open up Server Manager. Click the tools tab at the top and select active directory users and computers.
Right-click the domain `domain.test` and select new, then Organizational Unit as this mimics real-world departments. 
<img src="https://i.imgur.com/UEXlO8r.png" width="500"> <img src="https://i.imgur.com/Ps4QKEq.png" width="370">
<img src="https://i.imgur.com/8435dDA.png" width="400"> <br> <sup>Ref 13: Creation of new Organizational Unit called IT </sup>	 

**Create new users within IT** <br>
Click IT OU and right-click to add a new user. Here you will fill out the information based on the user such as their first & last name and their login information 

<img src="https://i.imgur.com/h4uzGuh.png" width="400"><img src="https://i.imgur.com/S38TCAD.png" width="400">
<img src="https://i.imgur.com/uDsHmvJ.png" width="400"> <br> <sup>Ref 14: User being added to the OU of IT  </sup>	

**Windows 10 joins Windows Server Domain** <br>
On the Windows 10 VM right-click the Windows icon at the bottom left and click on system. Scroll down to Advance System Settings. <br>
Go to the computer name tab and click the box that says change. Click the radio button for Domain and type the domain in.

<img src="https://i.imgur.com/nNxokCC.png" width="500"><br> <sup>Ref 15: Join domain  </sup>	

**Log on to the domain using a user account** <br>
When you sign on it points to the domain we have created.`DOMAIN`

<img src="https://i.imgur.com/3xuqS37.png" width="270"> <img src="https://i.imgur.com/DBeIxAB.png" width="300"> <br> <sup>Ref 16: Sign in to the domain with the user that has been created  </sup>	

**User Password Reset** <br>
Location the user within the domain, right-click the user and select reset password. It will prompt you to create a new password, Check the box to make the user change to a password of their choice the next time they log in. 

<img src="https://i.imgur.com/JZcIlv7.png" width="500"><img src="https://i.imgur.com/84sLwCA.png" width="300"> <br> <sup>Ref 17: Resetting user password </sup>	

If you don't know where the user is located you can use search. Right-click the domain and select find. 

<img src="https://i.imgur.com/EYTIU48.png" width="400"><img src="https://i.imgur.com/C0J8oSu.png" width="400"> <br> <sup>Ref 18: Searching for user & resetting user password </sup>	

**Account lockout** <br>
Open up Server Manager. Click the tools at the top and group policy management.

<img src="https://i.imgur.com/dWjAnS8.png" width="600"> <br> <sup>Ref 19: Group policy management  </sup>	

Under the domain right-click default domain policy and click edit. <br>
Navigate to Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Account Lockout Policy.<br>
Right-click account lockout threshold and select properties. Here is where you can edit the number of times wrong login attempts can occur before the account is locked out.

<img src="https://i.imgur.com/DZnf3hR.png" width="400"><img src="https://i.imgur.com/gODrk7m.png" width="500"><br> <sup>Ref 20: Domain policy for account lockout </sup>	
	
**Group Policy Objects** <br>
Right-click the OU. Click create a GPO in this domain. Right-click the new GPO and select edit 

<img src="https://i.imgur.com/kmx6KPj.png" width="400"><img src="https://i.imgur.com/00yfIDq.png" width="500"><br> <sup>Ref 21: Domain policy for account lockout </sup>	

**Configure Automatic Updates** <br>
Locate computer configurations. Policies> administrative templates> Windows components> Windows update>  click on “configure automatic updates”. Click the enabled radio button towards the bottom to configure automatic updating. Selection option 4 in the dropdown box. Add a time frame and press apply. 

<img src="https://i.imgur.com/RakHjPu.png" width="400"><img src="https://i.imgur.com/VGICa4v.png" width="350"><br> <sup>Ref 22: Automatic updates to occur at a specific time </sup>	

**Configure Policy for Users** <br>
Locate user configurations. Policies> administrative templates> all settings. Click on prohibit deleting items. 

<img src="https://i.imgur.com/MFJ7Qpr.png" width="600"> <br> <sup>Ref 23: GPO for User </sup>	

For the changes to apply, Open the command prompt and run `gpupdate /force` <br>
<img src="https://i.imgur.com/sDxaVtz.png" width="400"> <br> <sup>Ref 24: Apply changes </sup>	

**Allowing Remote Desktop Connection for Users** <br>
On the Windows 10 VM right-click the Windows icon at the bottom left and click on system. Scroll down to Advance System Settings, go to the remote tab, and click the checkbox and radio button at the bottom that says allow remote connections to this computer.
Click select users and at the bottom dialog box type the users or groups to add to be able to remote to this computer, then click check names and press ok. 

<img src="https://i.imgur.com/zCSbpBF.png" width="450"> <img src="https://i.imgur.com/THEXe6W.png" width="450"> <br> <sup>Ref 25: Enabling RDP for users </sup>	 

## Issues
**Setting static IP for Ubuntu VM** <br>
When trying to set a static ip for the Ubuntu machine the guide had a different netplan file. `/etc/netplan/00-installer-config.yaml` Which confused me if I needed that specific one but it turns out you can use the one that the Ubuntu system has by default. For my case, it was`/etc/netplan/50-cloud-init.yaml`

As I've now learned that the format has to be exact for each Ubuntu version as the guide was running an older Ubuntu version. I obtained the format from <a href="https://Linuxconfig.org/setting-a-static-ip-address-in-ubuntu-24-04-via-the-command-line">here</a> to correctly configure the IP.

<img src="https://i.imgur.com/orlYUfn.png" width="460"> <img src="https://i.imgur.com/8NVBgYI.png" width="480"> <br> <sup>Ref 26: Netplan guide IP format </sup> <br> <sup>Ref 27: Netplan IP format that worked for Ubuntu24.04 </sup>	

**Windows 10 joining the Windows server domain due to DNS configuration** <br>
Press the `Windows key + R` to open up run. Type in `ncpa.cpl` and press ok. Then right-click and go into properties, click Internet Protocol Version 4. Change the DNS to point at the domain controller `192.168.10.7`.

<img src="https://i.imgur.com/Wpk3Z0v.png" width="380"> <img src="https://i.imgur.com/ZRI4ds9.png" width="280"> <br> <sup>Ref 28: Issue with DNS  </sup>	

-------------------------------------------------------------------
