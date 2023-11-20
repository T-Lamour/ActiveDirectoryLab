<h1>Active Directory Home Lab With Bulk User Creation</h1>

<h2>Description</h2>
This project is a walkthrough of how I created an Active Directory home lab Environment using VirtualBox. I set up and configure a Domain Controller to run Active Directory, run a PS script to create over 1000 users in AD and log into a newly created accounts on a client VM that uses the domain setup to connect to the internet. The purpose of this lab is to simulate a business environment and for me to operate as the Administrator.
<br />

<h2>Languages and Utilities Used</h2>

- <b>Active Directory</b> 
- <b>PowerShell ISE</b>
- <b>CMD</b>

<h2>Environments Used </h2>

- <b>VirtualBox</b>
- <b>Windows Server 2019</b>
- <b>Windows 10 Pro</b> (22H2)

<h2>Links</h2>

- <b>VirtualBox:</b> https://www.virtualbox.org/wiki/Downloads
- <b>Windows Server 2019:</b> https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019
- <b>Windows 10 ISO:</b> https://www.microsoft.com/en-us/software-download/windows10

<h2 align="center">Program walk-through</h2>

<p align="center">
<b>The network diagram I'll be using for this project.</b> <br/>
<img src="https://imgur.com/HE2sRnA.jpg" height="80%" width="80%" alt="Network Diagram"/>
<br />
<br />
<br />
<p align="center">
<b>For the VM that will be hosting my DC, I need two network adapters. I need the NAT that will use my host IP address from my home router and an Internal Network Adapter so my DC can communicate with other VMs. </b> <br/>
<img src="https://imgur.com/KPhbN22.jpg" height="80%" width="80%" alt="Configuring the Network Adapter for the Domain Controller Virtual Machine"/>
<br />
<br />
<br />
<img src="https://imgur.com/xMxJiEv.jpg" height="80%" width="80%" alt="Configuring the Network Adapter for the Domain Controller Virtual Machine"/>
<br />
<br />
<p align="center">
<b>Figuring out which NIC is our NAT and which is our internet network, I rename the adapters to differentiate as it is important later on when setting up the DC and DHCP.</b> <br/>
<img src="https://imgur.com/4QXwzaE.jpg" height="80%" width="80%" alt="Configuring the Network"/>
<br />
<br />
<b>Now I’ve configured the Internal network adapter and assign it an IP address based on the diagram above (172.16.0.1) and I do not need to give it a default gateway as the DC is the gateway. As for the DNS server, I assigned an IP based on the diagram because when we install Active Directory it will install DNS. I set it as a loopback address so it pings itself.</b> <br/>
<img src="https://imgur.com/4CqJmmN.jpg" height="80%" width="80%" alt="Configuring the Network"/>
<br />
<br />
<b>Now that the NICs are differentiated, I rename the VM as DC. This forces a restart</b> <br/>
<img src="https://i.imgur.com/TklrxzC.jpg" height="80%" width="80%" alt="Renaming the PC"/>
<br />
<br />
<b>After restarting, I installed Active Directory Domain Services through Server Manager, and setup the server as the domain right after. I named this Emirates.Stadium (as you can tell, I’m an Arsenal fan!) When the server is promoted to a domain, it forces a restart.</b> <br/>

<img src="https://imgur.com/lr8den7.jpg" height="80%" width="80%" alt="AD installation"/>
<br />
<p align="center">
 <img src="https://imgur.com/ZYztTgv.jpg" height="80%" width="80%" alt="Configuring the Network Adapter for the Domain Controller Virtual Machine"/>
 <p align="center">
<br />
<br />
<p align="center">
<b>Using the built in Admin account, I will create a dedicated domain Admin account, granting it Domain Admin permissions on AD. </b> <br/>
</p>
<p align="center">
 <img src="https://imgur.com/XKDbk55.jpg" height="80%" width="80%" alt="Creating Admin account"/>
<br />
<br />
<p align="center">
<b>Now I need to install RAS/NAT (incl Routing) for my Windows 10 client to gain access to the internet through the internal network via the DC.</b> <br/>
</p>
<p align="center">
 <img src="https://imgur.com/Gf6U38P.jpg" height="80%" width="80%" alt="Installing RAS"/>
<br />
<br />
<p align="center">
<b>Now that the role is installed, I need to configure the Routing and Remote Access under tools in Server Manager. Click tools in Server Manager and Right click ‘DC’ and configure and enable routing and remote access. Under configuration, select NAT and the external NIC (the internet).</b> <br/>
</p>
<p align="center">
 <img src="https://imgur.com/6SI4aOu.jpg" height="80%" width="80%" alt="Config Remote Access"/>
<br />
<br />
<p align="center">
<b>After Remote Access has been configured, install the DHCP Server. This will allow our Windows 10 clients to be assigned an IP address and gain access to the internet..</b> <br/>
</p>
<p align="center">
 <img src="https://imgur.com/VpQuR04.jpg" height="80%" width="80%" alt="Installl DCHP"/>
<br />
<br />
<p align="center">
<b>The purpose of the DHCP server is to allows machines on the network to automatically be assigned an IP address. The scope created will assign IP addresses in a range, that being 172.16.0.100-200 so the DHCP will effectively be able to give out 100 IP addresses (you can also configure the lease time of the IP addresses for the client machines so that the IP cannot be used by other devices).</b> <br/>
</p>
<p align="center">
<img src="https://imgur.com/fKG4HaY.jpg" height="80%" width="80%" alt="Installl DCHP"/>
<br />
<p align="center">
<img src="https://imgur.com/4MNAjEQ.jpg" height="80%" width="80%" alt="Installl DCHP"/>
<br />
<br />
<p align="center">
<b>Now the DC and AD is configured, I’m moving onto sourcing the PS script to create the 1000 users from the internet, however the security features on the DC needs to be disabled (Never do this in a production environment! Since this is only a lab environment for myself it is not an issue). In order to run the PS script, we need to open Powershell as admin and set the exercution policy as unrestricted – *Set-ExercutionPolicy unrestricted* </b> <br/>
</p>
<br />
<b>Open and run the sourced script. The script has run successfully however, there were some that were not created due to the name length. This can easily be solved by editing the script to change the username layout, or change character length on Group Policy on AD.</b> <br/>
<p align="center">
<img src="https://imgur.com/0ec9T9S.jpg" height="80%" width="80%" alt="Installl DCHP"/>
<br />
<br />
<p align="center">
<b>Now the users have been created, I need to setup the client Virtual Machine that will act as a user in the domain (ensure you select the NIC of the VM as internal instead of NAT in order to be assigned the IP from the DC). Once created, I search *sys.cpl* under Windows Search to rename the machine as CLIENT1 and join onto the domain. I use the credentials of the admin account created to confirm.</b> <br/>
</p>
<p align="center">
<img src="https://imgur.com/KtkSx5G.jpg" height="80%" width="80%" alt="Using Powershell to create 1000 user accounts in bulk"/>
<br />
<br />
<p align="center">
<b>The machine has successfully joined the domain! I log into a user account created from the PS script to check if everything is configured correctly. After loging in, run ipconfig on CMD to test if the client VM was assigned the properly assigned IP address by the DC. We can see that it was properly leased an IP address by the domain controller.</b> <br/>
</p>
<p align="center">
<img src="https://imgur.com/HRx5VCn.jpg" height="80%" width="80%" alt="Using Powershell to create 1000 user accounts in bulk"/>
<br />
<br />
<p align="center">
<b>The script has run successfully and the output confirmations that the user accounts has been created looks amazing. There were some duplicates that were not created, but that is easily solved by adding to the Powershell script a few lines of code that will tell it what to do in case duplicates occur. Perhaps something along the lines of "If a duplicate occurs, add a 1 to the end of the account name" for example. If you want to see the full code used, refer to the top of this repository. The script is under CREATE_USERS.ps1</b> <br/>
</p>
<p align="center">
<img src="https://i.imgur.com/MhlDg1o.jpg" height="80%" width="80%" alt="Using Powershell to create 1000 user accounts in bulk"/>
<br />
<br />
<p align="center">
<b>A final test to see that the work environment and bulk users I created is working, I head back to the server VM and review the users and computers on AD</b> <br/>
</p>
<p align="center">
<img src="https://imgur.com/hA5JYvw.jpg" height="80%" width="80%" alt="Setting up new Virtual Machine"/>
<br />
<br />
<p align="center">
<img src="https://imgur.com/HOr9M4i.jpg" height="80%" width="80%" alt="Using Powershell to create 1000 user accounts in bulk"/>
<br />

<b>This Finalises the Active Directory Lab. This can be further modified by implementing Group Policies, security features, programs etc.</b>  <br/>
</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
