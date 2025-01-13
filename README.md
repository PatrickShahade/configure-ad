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

- Create a Virtual Network
- Create Virtual Machines to operate as Domain Controller and Client
- Configure Domain Controller DNS
- Link Client machine to Domain Controller
- Install Active Directory Domain Services on the Domain Controller
- Create Orginazational Units to contain Users and Admins
- Link Client to the Domain
- Generate Users with Powershell
- Create a Group Policy for Users
- Test Account Lockouts and Enable/Disable Accounts
- Review Event Logs

<h2>Deployment and Configuration Steps</h2>

<p>
1) To begin I'm going to set up a Virtual Network for the Active Directory within Azure. 
</p>
<p>
<img src=https://i.imgur.com/i80ySIk.png/>
  <img src=https://i.imgur.com/lfqRWnY.png/>
</p>
<br />

<p>
2) Now I want a Virtual Machine (VM) to act as our Domain Controller (DC-1). I'll use these settings and then review + create. 
</p>
<p>
<img src=https://i.imgur.com/ykJITsb.png/>
  <img src=https://i.imgur.com/asmnYbT.png/>
</p>
<br />

<p>
3) There needs to be at least one Client (Client-1), so I'll make another VM to fill that role. I'll use these settings and then review + create.
</p>
<p>
<img src=https://i.imgur.com/2Z3fYZT.png/>
  <img src=https://i.imgur.com/3mGoXhj.png/>
</p>
<br />

<p>
4) With both VMs up and running, I need to set up DC-1 to have a static NIC private IP address. This can be done in the Network settings for DC-1. You can see the Network Interface and IP Configuration and set it to static. Once saved, it should remain static. 
</p>
<p>
<img src=https://i.imgur.com/ovdpShn.png/>
  <img src=https://i.imgur.com/XJo6Jc3.png/>
  <img src=https://i.imgur.com/7vss0Zs.png/>
  <img src=https://i.imgur.com/Kk61DYE.png/>
</p>
<br />

<p>
5) With that done, DC-1's private IP address can be used. The Client-1's DNS settings can be set to DC-1's private IP address.  In Client-1's IP configuration, under DNS servers, it can be set to DC-1's private IP and saved as a custom DNS. 
</p>
<p>
<img src=https://i.imgur.com/dGaKIl8.png/>
  <img src=https://i.imgur.com/ZgxwtKq.png/>
  <img src=https://i.imgur.com/Pztp0Io.png/>
  <img src=https://i.imgur.com/KHhgclZ.png/>
</p>
<br />

<p>
6) After restarting and logging into Client-1, I'm going to ping DC-1's private IP address through PowerShell. From the search bar we can open Windows Powershell and then ping DC-1's private IP. I forgot to turn off the private firewall for this test originally, so I had to go back and do that. With that fixed, Client-1 is pinging the private IP of the Domain Controller.
</p>
<p>
<img src=https://i.imgur.com/KQLiN3A.png/>
  <img src=https://i.imgur.com/YsoVHK2.png/>
</p>
<br />

<p>
7) On DC-1, you can turn off the firewalls to do our test ping by searching "wf.msc". Within this window you can change the firewall properties and turn off the private firewall. Apply and Ok. This should allow Client-1 to ping the DC-1.
</p>
<p>
<img src=https://i.imgur.com/rmI70ND.png/>
  <img src=https://i.imgur.com/dpB32xW.png/>
  <img src=https://i.imgur.com/vwWotAl.png/>
</p>
<br />
 
<p>
  8) Within DC-1, Active Directory Domain Services (ADDS) can be installed. From the Server Manager (if it isn't already open for you, just locate it via the search bar), I'm going to add roles and features. There is only one server option for me here, so I just continue and select ADDS and then "add features". I continue up to this point where it asks if I want to restart if required. I'll check that box, say yes and install.
</p>
<p>
<img src=https://i.imgur.com/W2mmocE.png/>
  <img src=https://i.imgur.com/qpJJ8nr.png/>
  <img src=https://i.imgur.com/bbw3Wgy.png/>
  <img src=https://i.imgur.com/BnsBeHh.png/>
  <img src=https://i.imgur.com/8z1AZtD.png/>
  <img src=https://i.imgur.com/fbRBBZo.png/>
  <img src=https://i.imgur.com/tgGhdIT.png/>
</p>

<p>
9) At this point, the DC-1 VM needs to be properly configured to become a Domain Controller. From the Server Manager, this flag is prompting me. I'll "promote this server to a Domain Controller" and then opt to "add a new forest". I'll use "mydomain.com" as the root domain name. For the DSRM password I shouldn't ever need this, but I'll use something memorable. I'll uncheck this "specify DNS delegation options" box and then continue until prompted to install. After this finishes, it should automatically restart. 
</p>
<p>
<img src=https://i.imgur.com/JYSelxV.png/>
  <img src=https://i.imgur.com/L1WQdXO.png/>
  <img src=https://i.imgur.com/YK2ZmLk.png/>
  <img src=https://i.imgur.com/R5o9bF4.png/>
  <img src=https://i.imgur.com/1mAFYqd.png/>
</p>

<p>
10) Now that DC-1 is an actual Domain Controller, when I log back in, I need to specify the context in which I want to log back in. So to do this, you specify that you want to log into "mydomain.com" then using a "\" with the username. 
</p>
<p>
<img src=https://i.imgur.com/WSRJDhy.png/>
</p>

<p>
11) Next I'll create an Organizational Unit (OU) to contain the users and admins so I can do some testing later on. I need to be specific in how I name these now, because of a script I use later. I'll open up Active Directory Users and Computers (ADUC) from the start menu. I can take a look at mydomain.com's users and computers here, naturally, so we see our "labuser" and DC-1. Right clicking on mydomain.com I can add an OU for _EMPLOYEES and _ADMINS. I also want to add an admin, so I'll rightclick and create a new user in our _ADMINS folder. I'll keep it simple and add Jane Doe - password can be whatever as long as you remember it. I unticked the box for the prompt to change the password at next logon. 
</p>
<p>
<img src=https://i.imgur.com/8Rw4WOI.png/>
  <img src=https://i.imgur.com/P3I7q4o.png/>
  <img src=https://i.imgur.com/XyTXKQU.png/>
  <img src=https://i.imgur.com/38G7LYj.png/>
  <img src=https://i.imgur.com/N2A2CPC.png/>
  <img src=https://i.imgur.com/yLuopkX.png/>
  <img src=https://i.imgur.com/OU3XUj6.png/>
  <img src=https://i.imgur.com/mC2H2zu.png/>
  <img src=https://i.imgur.com/ykdcdhZ.png/>
  <img src=https://i.imgur.com/rDCLFpY.png/>
  <img src=https://i.imgur.com/2yQe9H9.png/>
</p>

<p>
12) This account isn't an admin yet, despite us naming it admin and putting it in our admin group. I'll change the properties by finding the "member of" tab and adding it to the "domain admins" built in group (click the check names button to make sure it locates it). Apply, OK and then log out and back in as our "jane_admin" user. 
</p>
<p>
<img src=https://i.imgur.com/cjqMSrd.png/>
  <img src=https://imgur.com/a/3TeHvvo/>
  <img src=https://i.imgur.com/PLtrXS3.png/>
  <img src=https://i.imgur.com/qs3dNRe.png/>
</p>

<p>
13) Within Client-1, I'm going to connect to the new domain. This can be done by rightclicking the start menu and selecting system, then choosing to "rename this PC (advanced)". Select "Change" under the Computer Name tab, select Domain and put "mydomain.com" or your selected domain name. Using the new admin, jane_admin's credentials we can OK this. We get a sound notification of approval and a small popup in the back to let us know it worked. We'll be prompted to close our windows and restart. 
</p>
<p>
<img src=https://i.imgur.com/uV3rIb5.png/>
  <img src=https://i.imgur.com/GOakVYe.png/>
  <img src=https://i.imgur.com/JvmWmPq.png/>
  <img src=https://i.imgur.com/kHgJuYR.png/>
  <img src=https://i.imgur.com/gVTSKUE.png/>
</p>

<p>
14) Back in DC-1 as jane_admin, I'll check our Active Directory Users and Computers and verify that Client-1 is part of the domain now. I'm going to put it in a new OU and name it _CLIENTS and put Client-1 in there. This isn't absolutely necessary, but just to keep it organized, I will. 
</p>
<p>
<img src=https://i.imgur.com/0LToeMq.png/>
  <img src=https://i.imgur.com/UcWVWfv.png/>
</p>

<p>
15) Returning to Client-1, we're going to log in as our jane_admin. This may take a few minutes. Once logged in, right click start and go to system. We're looking for Remote Desktop. Under User Accounts, we can select users to remotely access this PC, and add Domain Users. We can, and normally would make a Group Policy to do this and change many systems at once.
</p>
<p>
<img src=https://i.imgur.com/8Gszr8X.png/>
  <img src=https://i.imgur.com/OouSHFP.png/>
</p>

<p>
16) In order to get some users to work with, I'll go back to DC-1 as jane_admin and start up Powershell ISE as an administrator. I'll create a new file and paste a <a href="https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1">script</a> into it that will generate some random users for me. I'll save the script as "CreateUsers" and just save it to the desktop. This will generate a bunch of new users when run, so we'll run it (F5 or the green arrow on the toolbar up top) and we can watch it go! In our ADUC we can see all the users already generated. 
</p>
<p>
<img src=https://i.imgur.com/wBfZRfS.png/>
  <img src=https://i.imgur.com/bxGxLka.png/>
  <img src=https://i.imgur.com/bFmyhIR.png/>
  <img src=https://i.imgur.com/pdPoeUj.png/>
  <img src=https://i.imgur.com/eakBXKN.png/>
</p>

<p>
17) I'm going to pick one of these users to log in to our Client-1 with. This script generates them all with "Password1" as the password. I'm just going to pick one and give it a go! My target is "dane.wafu". I still need to specify the context, so I'll login using "mydomain.con\dane.wafu". It's like magic to see things working, isn't it? I just opened Powershell to confirm I am indeed dane.wafu. Huzzah. 
</p>
<p>
<img src=https://i.imgur.com/kAnW4rV.png/>
  <img src=https://i.imgur.com/bZ5keOt.png/>
  <img src=https://i.imgur.com/6sm1JGo.png/>
</p>

<p>
18) For a bit of testing, let's get a feel for how account lockouts and password resets happen. Currently there is no threshold for this, so I could fail the password unlimited times until I correctly guessed. That's not safe! So I'll set a Group Policy for this. I'm going to open up ADUC and find our user from before. We'll need to navigate here later, so I just want to have our user located already. 
</p>
<p>
<img src=https://i.imgur.com/Q0vPgrn.png/>
  <img src=https://i.imgur.com/iolMH2X.png/>
</p>
<br />

<p>
19) So, to get the passwords to lock out after a few attempts, I'm going to use the search bar in DC-1 to look for "Group Policy Management". You can expand Forest:mydomain.com > Domains > mydomain.com and see "Default Domain Policy". I want to edit this with a right click. You may want to expand the window if you haven't already. I'm looking to expand Computer Configuration > Policies > Window Settings > Security Settings > Account Policies > Account Lockout Policy. Under the Account Lockout Policy, I'm going to rightclick the account lockout duration properties and set it to 15 minutes. It should default the logon attempts to 5 after this. It may take a moment for our policy to refresh, but it doesn't take long. 
</p>
<p>
<img src=https://i.imgur.com/qicdw5P.png/>
  <img src=https://i.imgur.com/icv4F1D.png/>
  <img src=https://i.imgur.com/LOA6rmV.png/>
</p>
<br />

<p>
20) If I go to our Client-1 and attempt to logon incorrectly six times, I should be notified that I am now locked out. If I look up dane.wafu on DC-1 within the ADUC, I can look at their account and see that it is currently locked out. A box to unlock their account is now available for to tick. I'll tick that box, Apply and OK and try to logon correctly now. I am now back in successfully as dane.wafu.
</p>
<p>
  <img src=https://i.imgur.com/b6f30dk.png/>
<img src=https://i.imgur.com/JoApDo0.png/>
  <img src=https://i.imgur.com/UYlWFUw.png/>
  <img src=https://i.imgur.com/HTqTmmR.png/>
  <img src=https://i.imgur.com/J1PYEed.png/>
</p>
<br />

<p>
21) Within DC-1, I'm going to manually disable the account. I'll locate the user account like before in the ADUC, rightclick it and disable it. I can also do this from the account options. There's a list of options under where I unlocked it for the bad login attempts. Your choice. I can see what this looks like as our user by trying to logon again. Then it can just be re-enabled by reversing this similar to how it was disabled.
</p>
<p>
<img src=https://i.imgur.com/dsTGSQ5.png/>
  <img src=https://i.imgur.com/LYMdfZZ.png/>
  <img src=https://i.imgur.com/NhWocLR.png/>
  <img src=https://i.imgur.com/TASDsn5.png/>
</p>
<br />

<p>
22) I'm going to take a look at the logs in Client-1 by opening Event Viewer. I'm opening it as an admin, so it will prompt me to use admin credentials. Even though we are logged into the Client-1 as dane.wafu, if you have admin credentials you can still open it as an admin user. You can look under the Windows Logs and check Security to see related logs. If you use the find option, you can search for the user and scroll through to see all of our "audit failures".
</p>
<p>
<img src=https://i.imgur.com/e0NEWnS.png/>
  <img src=https://i.imgur.com/AlF8zyu.png/>
  <img src=https://i.imgur.com/7bup8UG.png/>
  <img src=https://i.imgur.com/a3yBCXL.png/>
</p>
<br />
