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

- Step 1 Create resources in Azure
- Step 2 Establish and confirm connectivity between Domain Controller and Client
- Step 3 Install Active Directory
- Step 4 Create Admin and User accounts in Active Directory
- Step 5 Join Client to the Domain Controller
- Step 6 Setup Remote Desktop for non- admin users to Client
- Step 7 Use script in Powershell to create users in Active Directory

<h2>Deployment and Configuration Steps</h2>

- Step 1 Create resources in Azure
  - Create VM domain controller (DC-1) with Windows Server 2022
  - Create VM client (Client-1) with Windows 10.
  - Ensure that Client-1 is on the same virtual network as DC-1.


<p>
<img src="https://i.imgur.com/8tREJ9b.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>


<p>
<img src="https://i.imgur.com/iGp477C.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  
 - Next ensure that the private IP address in DC-1 is changed from dynamic to static so that it does not change
    - In DC-1 go to Networking ->IP configurations ->Click on the IP address
    - Under Assignment select Static
  
</p>
<br />

<p>
<img src="https://i.imgur.com/8stU3Lw.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

- Step 2 Establish and confirm connectivity between Domain Controller and Client
  - From Client-1 open Command line and type ping private static ip.
  - Due to the firewall blocking incoming (ICMP) traffic, the request will time out
  - In order to allow inbound ICMP traffic we need to login to DC-1 open Windows Defender Firewall with advanced Security.
  - Sort the list by protocol and scroll to ICMPv4 then enable the 2 inbound rules
</p>
<br />

<p>
<img src="https://i.imgur.com/WBJwHSB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  
- Go back to Client-1 and you should now be able to successfully ping the static IP
</p>
<br />

<p>
<img src="https://i.imgur.com/KHdaRgg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  
- Step 3 Install Active Directory
  - Go to DC-1 Open Server Manager
    - Select Add Roles and features
    - Follow prompts to "Server Roles"
    - Check Active Directory Domain Sevices
    - Click on "Add Features"
</p>
<br />

<p>
<img src="https://i.imgur.com/1yTZDkR.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  
- Continue to follow prompts until Active Directory is installed
</p>
<br />

<p>
<img src="https://i.imgur.com/gnVCIUH.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  
- Once Installed go back to the Server Manager Dashboard and click the flag with the hazarss sign under it. Then click "Promote this server to a domain controller
</p>
<br />

<p>
<img src="https://i.imgur.com/ADu7LWI.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  
- Select "Add a new forest" and type in the root domain ie (mydomain.com)
</p>
<br />

<p>
<img src="https://i.imgur.com/RQjEWtQ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

- Follow the prompts to the end of the installation. DC-1 will restart and you will have to log back in.
</p>
<br />

<p>
<img src="https://i.imgur.com/EJASKsi.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  
- Step 4 Create Admin and User accounts in Active Directory
  - In Server Manager, click on "Tools" in the upper right corner and select "Active Directory users and Computers"
</p>
<br />

<p>
<img src="https://i.imgur.com/CQdJ5R8.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

- In Active Directory Users and Computers, right click the doomain (mydomain.com)-> "New" -> Originizational Unit"
- Create 2 orginizational units for administrators (_ADMINS) and employees (_EMPLOYEES)
</p>
<br />

<p>
<img src="https://i.imgur.com/oj4gW8J.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>


<p>
<img src="https://i.imgur.com/oguJria.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

- Select _ADMINS organizational unit and right click. In th eright window  go to "New" and click "User"
- Fill in the boxes to create new user "Jane Doe" username "jane_admin"
</p>
<br />

<p>
<img src="https://i.imgur.com/AX0NGkg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

</p>
<br />

<p>
<img src="https://i.imgur.com/dA60MU4.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

- Once the account has been created, right click it and go to "Properties"-> click "Member of" tab->click the "Add.." button, type in domain and click the "Check Names" button, type in domain and click the "Check Names" button.->Click "Domain Admins" and press "OK" and "Apply"
- Log out of DC-1 as "labuser" and log back in as the administrator (jane_admin)
</p>
<br />

<p>
<img src="https://i.imgur.com/u9bka6U.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

- Step 5 Join Client to Domain Controller
  - Go to the Azure portal. Go to the Client-1 virtual machine and click on the "Networking" section underneath "Settings" on the left.
</p>
<br />

<p>
<img src="https://i.imgur.com/wD7wqHm.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

- Click on "DNS Servers"  then click "Custom". Type in the private IP for DC-1 (10.0.0.4)
- Restart Client-1 and log back in. Once logged in right clickon the start menu and click on "System"
- On the right had side click "Rename this PC (advanced). " Then click on the "Change" button and type in the name (mydomain.com)
</p>
<br />

<p>
<img src="https://i.imgur.com/oa8vFf2" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

- Step 6 Setup Remote Desktop for non-administrative users to Client-1
  -Login to Client-1 using the domain and admin account (mydomain.com\jane-admin)
  -Right click the Start menu and click "System" Then "Remote desktop" on the right side.
  -Click "Add.." and type in "Domain users"
  -Click Check Names" and click OK
</p>
<br />

<p>
<img src="https://i.imgur.com/CKQt7Ua.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

</p>
<br />

<p>
<img src="https://i.imgur.com/3hRynTF.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  
- Step 7 Create users in Active Directory using Powershell script
  - Go To DC-1 and open Windows Powershell from the start menu. Tight click and "Run as administrator"
</p>
<br />

<p>
<img src="https://i.imgur.com/2YQXliP.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

- Copy and paste script into new Powershell screen. 
- Once the users have been created, go back to Active Directory Users and Computers. All the users were placed in the _EMPLOYEES organizational unit.
</p>
<br />

<p>
<img src="https://i.imgur.com/gf83XMe.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  
- Active Directory has now been installed and configured!
</p>
<br />


