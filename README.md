<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Setup Resources in Azure
- Ensure Connectivity between the client and the Domain Controller
- Install Active Directory on Domain Controller
- Create User Accounts in Active Directory
- Join Client to Domain
- Setup Remote Desktop for Non-Administrative Users on Client
- Clean Up Resources by Deleting Resource Groups

<h2>Deployment and Configuration Steps</h2>
<h2>Setting Up Resources in Azure</h2>

<p>
<img src="https://i.imgur.com/jUB7t0u.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
We first have to create a new resource group in Azure.

Create a new resource group and name it "AD-Lab", then select your region, then click "Review + create", then once validation is passed, click "Create" at the bottom
</p>
<br />

<p>
<img src="https://i.imgur.com/ybEEAlE.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now it's time to setup our virtual machines for our domain controller.

Create a new virtual machine and ensure your resource group is "AD-Lab", then name your virtual machine "DC-1", then ensure consistency between regions selected, then select "Windows Server 2022", then select "Standard_E2s_v3" as the size, then enter a username and password (write these down for later), then check the two boxes to continue, then click "Review + create", then once validation is passed, click "Create" at the bottom.
</p>
<br />

<p>
<img src="https://i.imgur.com/JkxtdPA.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now it's time to setup our virtual machine for our client.

Create a new virtual machine and ensure your resource group is "AD-Lab", then name your virtual machine "Client-1", then ensure consistency between regions selected, then select "Windows 10 Pro", then select "Standard_E2s_v3" as the size, then enter a username and password (write these down for later), then check the box to continue, then click "Review + create", then once validation is passed, click "Create" at the bottom.
</p>
<br />

<p>
<img src="https://i.imgur.com/K4yFttP.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now we must change our domain controllers Network Interface Card (NIC) Private IP address from dynamic to static.

Find your DC-1 virtual machine in Azure and click on it to open, then select "Networking" from the options available on the left-hand side, then click on "dc-1303_z1"
</p>
<br />

<p>
<img src="https://i.imgur.com/YDgbbAD.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
From "dc-1303_z1", click "IP configurations" from the options available on the left-hand side, then click on "10.0.0.4 (Dynamic)
</p>
<br />

<p>
<img src="https://i.imgur.com/xfAWHEN.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Click "Static", then click "Save" and you have successfully set your domain controller's Private IP address to static instead of dynamic.
</p>
<br />

<h2>Ensuring Connectivity Between Client and Domain Controller</h2>

<p>
<img src="https://i.imgur.com/djHK2Yh.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now we will login to Remote Desktop using Client-1's Public IP address.

Find Client-1 in Azure then click on it, then copy it's Public IP Address, then on your physical machine, launch "Remote Desktop Connection", then paste the public IP into it, then click "Connect", then enter the credentials we wrote down earlier, then click "Yes" to the prompt
</p>
<br />

<p>
<img src="https://i.imgur.com/TSAqeaA.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now that you're in Client-1, open "Command Prompt", then ping DC-1's Private IP address (the one we changed from dynamic to static), type "ping 10.0.0.4 -t" in the command prompt, then hit enter.

Notice how the pings are being timed out, that is due to the firewall setting for the domain controller. We must now Remote Desktop into our domain controller.
</p>
<br />

<p>
<img src="https://i.imgur.com/oo28oth.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Find DC-1 in Azure then click it. Then copy the public IP address, then open "Remote Desktop Connection" on your physical machine, then click "Connect", then enter the credentials we wrote down earlier, then click "Yes" to the prompt.
</p>
<br />

<p>
<img src="https://i.imgur.com/tLwr6rq.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once logged into your domain controller, navigate to the search bar in Windows and type "Windows Defender Firewall with Advanced Security", then click "Inbound Rules", then find "Core Networking Diagnostics", then right click each of the highlighted selections, then click "Enable Rule"

Log back into Client-1 and you should see the pings succeeding. Connection has successfully been established so you can close command prompt on Client-1.
</p>
<br />

<h2>Installing Active Directory on DC-1</h2>

<p>
<img src="https://i.imgur.com/4QGGyGU.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Log into DC-1 through Remote Desktop, then go to "Server Manager", then click "Add roles and features", then click "Next", then click "Next", then click "Next", then select "Active Directory Domain Services", then click "Add Features", then click "Next", then click "Next", then click "Install", then after installing, click "Close"
</p>
<br />

<p>
<img src="https://i.imgur.com/4QGGyGU.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Click on the flag icon then click "Promote this serve to a domain controller", then select "Add a new forest", then type "mydomain.com" as the Root domain name, then click "Next", then enter "Password1" as the "DSRM" password, then click "Next", then click "Next", then click "Next", then click "Next", then click "Install", then let DC-1 restart.
</p>
<br />

<p>
<img src="https://i.imgur.com/qpzo4AD.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Remote Desktop into DC-1 again. This time we will use another user to sign-in. Enter "mydomain.com\labuser" as the username, then enter your password, then click "Yes" to the prompt.
</p>
<br />

<p>
<img src="https://i.imgur.com/47sQxHA.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once logged in, go to Windows search and search "Active Directory Users and Computers", then right click "mydomain.com", then hover over "New", then click "Organizational Unit", name it "_EMPLOYEES"

Repeat this process to create a new organizational unit named "_ADMINS"

</p>
<br />

<p>
<img src="https://i.imgur.com/2AtaSSW.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In the "_EMPLOYEES" tab, right click in the blank space on the right and create a new user. Name the user "Jane Doe" with the user logon name of "jane_admin", then click "Next"

Set password as "Password1", then uncheck the first box, and check the box beside "Password never expires", then click "Finish"


</p>
<br />

<p>
<img src="https://i.imgur.com/u06xl0m.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Right click "Jane Doe" and click "Add to a group...", then enter "domain" into the Enter the object names to select: field, then click "Check Names", then select "Domain Admins", then click "OK", then click "OK" again.
</p>
<br />

<p>
<img src="https://i.imgur.com/RhmIYtk.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now we will log into DC-1 with Jane Doe's account.

Log out of DC-1, then go back to Remote Desktop Connection and input DC-1's Public IP address, then when prompted for credientials, enter "mydomain.com\jane_admin", then enter "Password1" as the password, then click "Yes" to the prompt.
</p>
<br />

<h2>Joining Client-1 to mydomain.com</h2>

<p>
<img src="https://i.imgur.com/QXuADDn.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Navigate to Azure, then go to "Network Watcher", then click "Topology" from the options on the left hand side, then select the resource group "AD-Lab", then click on "client-1135_z1", which is Client-1's Network Interface Card.
</p>
<br />

<p>
<img src="https://i.imgur.com/FTlxwYz.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once in Client-1's NIC, click on "DNS Servers" from the left hand side options, then enter the Private IP address of DC-1, then click "Save"
  
Navigate to Client-1 Virtual Machine in Azure then click "Restart"
</p>
<br />

<p>
<img src="https://i.imgur.com/NAfCNGz.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once Client-1 has restarted, remote desktop into Client-1.
  
Right click the Windows icon on the bottom left of the desktop then click "System", then click "Rename this PC (advanced) from the right hand side options, then click "Change...", then select the box beside "Domain", then input our domain we created on DC-1 (mydomain.com), then click "OK"
  
Then enter the username we created in Active Directory in DC-1 (jane_admin), then enter the password (Password1), then click "OK", then click "OK" again, then allow Client-1 to restart.
</p>
<br />

<p>
<img src="https://i.imgur.com/eNAFncA.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<p>
Remote desktop back into DC-1 and open "Active Directory Users and Computers", then expand "mydomain.com", then click "Computers", then verify that we see Client-1 in the "Computers" container
</p>
<br />

<p>
<img src="https://i.imgur.com/0JIKK2u.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Create a new Organizational Unit (OU) named "_CLIENTS" within the "domain.com" root, then drag Client-1 from the "Computers" container into the new container we just created (_CLIENTS), then say "Yes" to the popup
</p>
<br />

<h2>Setting Up Remote Desktop for Non-Administrative Users on Client-1</h2>

<p>
<img src="https://i.imgur.com/N9Qap1o.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Remote desktop into Client-1 using the username "mydomain.com\jane_admin", then input the password "Password1"
  
Right-click the Windows icon on the bottom left of the screen, then click "System", then click "Remote Desktop" from the right-hand side options, then click "Select users that can remotely access this PC", then click "Add...", then enter "domain" into the Enter the object names to select: field, then click "Check Names", then select "Domain Users", then click "OK" on all the prompts
</p>
<br />

<h2>Creating Additional Users and Logging Into Client-1 with New User</h2>

<p>
<img src="https://i.imgur.com/nm87W6H.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Remote desktop back into DC-1 as jane_admin, then open "PowerShell_ISE" as an administrator from the Windows search bar, then open this link https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1, then go back to PowerShell, then click "New Script", then paste the contents into the space, then click "Run Script"
  
Once the script has finished running, we will have all these new users in Active Directory
</p>
<br />

<p>
<img src="https://i.imgur.com/lAsJIgQ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Open Active Directory Users and Computers, then click the "_EMPLOYEES" container and verify the script made new users. 
  
We will use one of these new users to attempt to log into Client-1. We will use the employee "max.muko"
  
From your physical machine, remote desktop into Client-1 and log in using "mydomain.com\max.muko" as the username and "Password1" as the password, then click "Yes" to the prompt.
  
Make sure to delete the resource groups in Azure when you are finished with the lab.
  
Congratulations! You have successfully learned to install and use Active Directory using virtual machines in Microsoft Azure.
  
  

</p>
<br />
