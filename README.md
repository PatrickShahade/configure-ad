# configure-ad
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

- Step 1
- Step 2
- Step 3
- Step 4

<h2>Deployment and Configuration Steps</h2>

<p>
First, we're going to set up a Virtual Network for the Active Directory. 
</p>
<p>
<img src=https://i.imgur.com/i80ySIk.png/>
  <img src=https://i.imgur.com/lfqRWnY.png/>
</p>
<br />

<p>
Then we'll set up a VM to act as our Domain Controller. We'll use these settings and then review + create. 
</p>
<p>
<img src=https://i.imgur.com/ykJITsb.png/>
  <img src=https://i.imgur.com/asmnYbT.png/>
</p>
<br />

<p>
We need at least one client machine, so we'll make another VM to fill that role. We'll use these settings and
</p>
<p>
<img src=https://i.imgur.com/2Z3fYZT.png/>
  <img src=https://i.imgur.com/3mGoXhj.png/>
</p>
<br />

<p>
Now that we have both VMs up and running, we need to set up DC-1 to have a static NIC private IP address. We can do this by going to into the Network settings for DC-1. From there we can see the Network Interface and IP Configuration and set it to static. Once we save it, it should remain static. 
</p>
<p>
<img src=https://i.imgur.com/ovdpShn.png/>
  <img src=https://i.imgur.com/XJo6Jc3.png/>
  <img src=https://i.imgur.com/7vss0Zs.png/>
  <img src=https://i.imgur.com/Kk61DYE.png/>
</p>
<br />

<p>
Now we're going to set our Client's DNS settings to DC-1's private IP address.  We'll get DC-1's private IP, and then in our Client's IP configuration under DNS servers, we can set it to DC-1's private IP and save it as a custom DNS. 
</p>
<p>
<img src=https://i.imgur.com/dGaKIl8.png/>
  <img src=https://i.imgur.com/ZgxwtKq.png/>
  <img src=https://i.imgur.com/Pztp0Io.png/>
  <img src=https://i.imgur.com/KHhgclZ.png/>
</p>
<br />

<p>
I've restarted Client-1, and now I'm going to log in and try to ping our DC-1's private IP address through PowerShell. From the search bar we can just search for Windows Powershell. Then, we'll just test by pinging our DC's private IP. I forgot to turn off the private firewall for this test originally, so I had to go back and do that. But as you can see, the Client is pinging the private IP of our DC.
</p>
<p>
<img src=https://i.imgur.com/KQLiN3A.png/>
  <img src=https://i.imgur.com/YsoVHK2.png/>
</p>
<br />

<p>
On the Domain Controller, we can turn off the firewalls to do our test ping by searching wf.msc. Within this window we can change the firewall properties, and just turn off the private firewall. Apply and Ok. Then we should be able to ping our DC unit from our Client unit. 
</p>
<p>
<img src=https://i.imgur.com/rmI70ND.png/>
  <img src=https://i.imgur.com/dpB32xW.png/>
  <img src=https://i.imgur.com/vwWotAl.png/>
  
</p>
<br />
Within DC we can now install Active Directory Domain Services. From our Server Manager (if it isn't already open for you, just locate it via the search bar), we're going to add roles and features. We only have one server option here, so we just continue and select Active Directory Domain Services and then add features. We can continue up to this point where it asks us if we want to restart if required. We'll check that box, say yes and install. 
<p>
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
At this point, we're going to configure our DC to become a Domain Controller proper. From the Server Manager, we can see this flag prompting us. We'll promote this server to a domain controller and then opt to "add a new forest". We will just use "mydomain.com" as our root domain name. For the DSRM password we shouldn't ever need this, but still use something you can remember. We'll uncheck this "specify DNS delegation options" box and we can continue until we are prompted to install. After this finishes, it should automatically restart. 
</p>
<p>
<img src=https://i.imgur.com/JYSelxV.png/>
  <img src=https://i.imgur.com/L1WQdXO.png/>
  <img src=https://i.imgur.com/YK2ZmLk.png/>
  <img src=https://i.imgur.com/R5o9bF4.png/>
  <img src=https://i.imgur.com/1mAFYqd.png/>
</p>

<p>
Now that DC is an actual Domain Controller, when we log back in, we need to specify the context in which we want to log back in. So to do this, we specify that we want to log into "mydomain.com" then using a "\" with the username. 
</p>
<p>
<img src=https://i.imgur.com/WSRJDhy.png/>
</p>

<p>
Next we'll create an Organizational Unit (OU) to contain our users and admins so we can do some testing later on. We do need to be specific in how we name these now, because it will impact us later. So we're going to use our start menu to open up Active Directory Users and Computers. We can take a look at mydomain.com's users, and computers here naturally so we see our "labuser" and DC-1. We're going to right click mydomain.com and add our OU for _EMPLOYEES and _ADMINS. We also want to add an admin, so we'll rightclick and create a new user in our _ADMINS folder. We'll keep it simple and add Jane Doe, password can be whatever we want as long as we remember it. I unticked the box for the prompt to change the password at next logon. 
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
This account isn't an admin yet, despite us naming it admin and putting it in our admin group. We need to make a few changes. We'll change the properties by finding the "member of" tab and adding it to the "domain admins" built in group (click the check names button to make sure it locates it). Apply, OK and we can log out and then back in as our "jane_admin" user. 
</p>
<p>
<img src=https://i.imgur.com/cjqMSrd.png/>
  <img src=https://imgur.com/a/3TeHvvo/>
  <img src=https://i.imgur.com/PLtrXS3.png/>
  <img src=https://i.imgur.com/qs3dNRe.png/>
</p>

<p>
Back on our Client-1, we're going to connect to our new domain. We can do this by rightclicking the start menu and selecting system. From here we can choose to "rename this PC (advanced)". We want to select change under the Computer Name tab, select Domain and put our "mydomain.com" or your selected domain name. Using our new admin, jane_admin's credentials we can OK this, get a sound notification of approval and a small popup in the back to let us know it worked. We'll be prompted to close our windows and restart. 
</p>
<p>
<img src=https://i.imgur.com/uV3rIb5.png/>
  <img src=https://i.imgur.com/GOakVYe.png/>
  <img src=https://i.imgur.com/JvmWmPq.png/>
  <img src=https://i.imgur.com/kHgJuYR.png/>
  <img src=https://i.imgur.com/gVTSKUE.png/>
</p>

<p>
Back in DC as jane_admin, we can check our Active Directory Users and Computers and verify that our Client-1 is part of our domain now. We're going to put it in a new OU and name it _CLIENTS and put our Client-1 in there. This isn't absolutely necessary, but just to keep it organized, we will. 
</p>
<p>
<img src=https://i.imgur.com/0LToeMq.png/>
  <img src=https://i.imgur.com/UcWVWfv.png/>
</p>

<p>
text
</p>
<p>
<img src=/>
</p>

<p>
text
</p>
<p>
<img src=/>
</p>

<p>
text
</p>
<p>
<img src=/>
</p>

<p>
text
</p>
<p>
<img src=/>
</p>
<br />



