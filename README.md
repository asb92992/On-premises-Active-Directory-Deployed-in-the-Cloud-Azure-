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


<h2>Deployment and Configuration Steps</h2>

<p>

![AD part 1](https://user-images.githubusercontent.com/58159183/210908723-6038ebfd-a05a-4e1d-a48e-a040b65e306b.gif)

</p>
<p>

</p>
<br />

- I created the domain controller VM (windows server 2022) named DC-1
- Created a Client vm (windows 10) named Client-1 
- Both VM's have the same resource group and Virtual network

<p>

![AD part 2](https://user-images.githubusercontent.com/58159183/210909239-42eeb700-1958-4660-92e2-321fef7d3c93.gif)

</p>
<p>

- NIC is network interface
- Proceeded to DC-1 VM NIC then went to the IP configs to set the IP address to static
- Since its been set to static the IP adress will not change for DC-1
- Login to remote desktop with client-1 public IP address and my username and passoword. 
- My username is labuser
- Copied DC-1 private IP address
- Went to CMD in CLient-1 and ping -t DC-1 private ip adress. ping -t 10.0.0.4
</p>
<br />

<p>

![AD part 3](https://user-images.githubusercontent.com/58159183/210910713-5ec0560a-1f38-4df7-bf94-08ba1fa31bb0.gif)

</p>
<p>

- Login to DC-1 with it's public IP adress in remote desktop
- Then went to wf.msc which stands for firewall and network protection in windows security
- Then went to inbound rules 
- As you can see the the ping request is still time out in the CMD

</p>
<br />

<p>

![AD part 4](https://user-images.githubusercontent.com/58159183/210911345-9b60044b-35cb-4a3b-86b2-1e0b6eb5aa7a.gif)

- In inbound rules I enabled two ICMPv4 called Core networking Diagnostics- ICMP Echo Request in the DC-1 Vm
- Proceeded to check back at the CLient-1 VM to see the ping succeed 
- I stop the ping from continuing by using ctrl-c
- Went back to DC-1 VM and in the server manager I click add roles and features 
- Proceeded to install active directory domain services
  
<p>

![AD part 5](https://user-images.githubusercontent.com/58159183/210912864-a5685983-48e8-411e-adee-0415a7a439ce.gif)
  
- Click on promote this server to domain controller in server manger to finsih installing active directory
- Added a new forest and name my root domain mydomain.com
- Active directory is installed
  
<p>
 
![AD part 6](https://user-images.githubusercontent.com/58159183/210913704-b62396b7-0311-416a-9b60-e6930dc2ec8e.gif)

- DC- 1 needed to restart since I install the active drectory domain services
- Login back into DC-1 with the public IP address
- I had to login with the context of the domain since DC-1 became a domain controller now 
- Instead of logging in with labuser I now login with mydomain.com\labuser
- Proceeded to Active Directory Users and Computers  and created an Organizational Unit called _EMPLOYEES AND _ADMINS
- In the _ADMINS section I proceeded to create a new USER named jessica doe with a username jessica_admin and a               password for the made up name
- I also Unchecked user must change password at next login since it is just practice and then I click password never         expires
- I now have a User in the _ADMINS section, but jessica_admin is not a admin yet
- To make jessica_admin an actual admin account I have to assign it to the domain admin group
- To do that I right click on jessica and went to properties and then member of
- I then added a domain admin group in member of
- I proceeded to type domain and check groups and then click on domain admin
- That then made jessica doe a administrator 
  
<p>
  
  
![AD part 7](https://user-images.githubusercontent.com/58159183/210915004-dca04342-adf8-4508-85bd-0a3d7f6a7cad.gif)

- I then logg off of DC-1
- Log back in to DC-1 with it's public IP address in remote deskop
- Login as mydomain.com\jessica_admin with the password I made for Jessica
- I proceeded to CMD to show which VM that I was in and I was in DC-1 and as jessica_admin as the user
- I then join Client-1 to the domain so I can login to any domain accounts 
- Now in Client-1 VM I went to settings - systems- rename this pc
- Clicked on change and made CLient-1 to a member of domain.com and it did not work because it reached out to the DNS       server to get whats the domain controller and I have not made one yet
- I then had to set my DNS server for Client-1 in Azure portal
- In Azuure portal I went to Clinet-1 VM. In Azure portal I will set Client-1 DNS settings to the DC private IP address
- I went to DC-1 networking to get its NIC private IP adress which was 10.0.0.4
- Note: I'm setting client 1 virtual network interface card. Im setting the DNS to DC-1 ip address is.
- Went to Client-1 networking -> DNS servers and then change the DNS server of Client-1 to DC-1 private ip address which     is 10.0.0.4
- I then restarted client-1 in azure which flushed the DNS cache
- I login to Client-1 as the user labuser since its not join to the domain yet. 

 


