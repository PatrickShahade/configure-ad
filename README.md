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
On the Domain Controller, we can turn off the firewalls to do our test ping by searching wf.msc. Within this window we can change the firewall properties, and just turn off the private firewall. Apply and Ok. Then we should be able to ping. 
</p>
<p>
<img src=https://i.imgur.com/rmI70ND.png/>
  <img src=https://i.imgur.com/dpB32xW.png/>
  <img src=https://i.imgur.com/vwWotAl.png/>
  
</p>
<br />

<p>
text
</p>
<p>
<img src=/>
</p>
<br />



