<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" height="40%" width="70%"alt="Microsoft Active Directory Logo"/>
</p>

<h1>Configuring On-premises Active Directory within Azure VMs</h1>
This walkthrough involves setting up a domain controller and a client on Azure. We will then connect the client to the domain controller so the client can be aware of all the different user accounts that exist on the domain.<br />

<h2>Languages</h2>

- PowerShell 

<h2>Environments Used</h2>

- Microsoft Azure 
- Windows 10
- Windows Server 2022
- Windows App (Remote Desktop)
- PowerShell

<h2>Technology/Applications/Services </h2>

- osTicket
- Azure Virtual Machines
- Active Directory Domain Services
- PowerShell

<h2>Walkthrough</h2>

</br>
<h3 align="center">
  Setting up the Virtual Machines
</h3>
</br>

First, a resource group needs to be created so our VM’s can be put in it.
  
![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic1.png)
</p>
<br />
<p>
Now, create a domain controller.
  


![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic2.png)
</p>
<br />
<p>
In the same Resource Group and Virtual Network, create the client.
</p>



![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic3.png)
</p>
<br />
<p>
Need to set Domain Controller’s NIC Private IP address to be static. It needs to stay consistent since we are connecting the client.

</p>



![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic4.png)
</p>
<br />
<p>
Log into the Domain Controller with Windows App(Remote Desktop) and disable the Windows Firewall for all profiles.
</p>



![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic5.png)
</p>
<br />
<p>
Set Client’s DNS settings to Domain Controller’s Private IP address(10.0.0.4).
</p>



![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic6.png)
</p>
<br />
<p>
Log in to Client and ping Domain Controller’s private IP address(must get reply from 10.0.0.4).

</p>



![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic7.png)
 
</p>
</br>
<h3 align="center">
  Installing Active Directory
</h3>
</br>

Go to Domain Controller and install Active Directory Domain Services.</br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Server Manager -> Add Roles and Features -> Add Active Directory Domain Services ->Next and Install




![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic8.png)
</p>
<br />
<p>
Promote the server to become a Domain Controller.</br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Setup a new forest as mydomain.com


</p>



![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic9.png)
</p>
<br />




![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic10.png)
</p>
<br />
<p>
Now when logging in the Domain Controller on Windows App(Remote Desktop) you have to specify the context</br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Ex. As a domain user - mydomain.com\labuser

</p>



![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic11.png)
</p>
</br>
<h3 align="center">
  Create a Domain Admin User
</h3>
</br>

In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called “_EMPLOYEES”</br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Active Directory Users and Computers -> Expand mydomain.com -> Right Click Users -> Expand New -> create an Organizational Unit 




![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic12.png)
</p>
<br />
<p>
And another for “_ADMINS”

</p>



![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic13.png)
</p>
<br />
<p>
Create a new employee named “Jane Doe” with the username of “jane_admin”

</p>



![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic14.png)
</p>
<br />




![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic15.png)
</p>
<br />
<p>
Add jane_admin to the “Domain Admins” Security Group<br />
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Right click the account -> Properties -> Member Of -> Add -> Enter “Domain Admins” -> apply

</p>



![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic16.png)
</p>
<br />
<p>
Log out / close the connection to Domain Controller and log back in as “mydomain.com\jane_admin”

</p>



![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic17.png)
</p>
</br>
<h3 align="center">
  Join the Client to the Domain (mydomain.com)
</h3>
</br>

As the original local admin (labuser), join it to the domain (computer will restart)</br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Settings -> About -> Rename the PC -> Change(under Computer Name tab) -> Member of-check Domain -> type mydomain.com -> OK -> login with jane_admin credentials 


![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic18.png)
</p>
<br />




![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic19.png)
</p>
<br />
<p>
Login to the Domain Controller and verify Client shows up in ADUC
</p>



![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic20.png)
</p>
<br />
<p>
Create a new OU named “_CLIENTS” and drag Client into there
</p>



![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic21.png)
</p>
</br>
<h3 align="center">
Setup Remote Desktop for non-administrative users on Client
</h3>
</br>

As mydomain.com\jane_admin on Client
</br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Settings -> System -> Remote Desktop -> Click Select users that can remotely access the PC 



![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic22.png)
</p>
<br />
<p>
Allow “domain users” access to remote desktop<br />
   *can now log into Client as a non-administrative user

</p>



![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic23.png)
</p>
</br>
<h3 align="center">
Create multiple users with a script and attempt to log into Client with one of the users
</h3>
</br>

As jane_admin on Domain controller, open PowerShell_ise as an administrator





![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic24.png)
</p>
<br />
<p>
Create a new File(called “create-users”) and paste the contents of the script into it
</p>



![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic25.png)
</p>
<br />
<p>
Run the script and observe the accounts being created


</p>



![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic26.png)
</p>
<br />
<p>
After script finishes, open ADUC and observe the accounts in the OU(_EMPLOYEES)


</p>



![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic27.png)
</p>
<br />
<p>
Now we can try to log in with one of the accounts using our client.<br />
Username: mydomain.com\<username><br />
Password: Password1

</p>



![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic28.png)
</p>
<br />



![image alt](https://github.com/GursimarJ/configure-ad/blob/a2f1b0ca441e9d9929c08de0b9499d740b196577/assets/Part4Pic29.png)
