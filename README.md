<img src="https://github.com/AnderMendoza/AnderMendoza/raw/main/assets/line-neon.gif" width="100%">

![Github Banner 4 - Active Directory Deployment and Configuration](https://github.com/user-attachments/assets/883dd7bb-8ec2-4b12-93c2-a9a4dc5cfd2d)

<img src="https://github.com/AnderMendoza/AnderMendoza/raw/main/assets/line-neon.gif" width="100%">

<h1>Implementing Active Directory on Azure Virtual Machines</h1>

This guide details deploying and configuring an on-premises Active Directory environment within Azure Virtual Machines, building on previously established infrastructure.<br />

<h2>🚨 Prerequisites</h2>

- [Creating Virtual Machines in the Cloud](https://github.com/mxwllslvr/creating-virtual-machines-in-the-cloud)
  
- [Establishing Active Directory Infrastructure in Microsoft Azure](https://github.com/mxwllslvr/establishing-active-directory-infrastructure-in-microsoft-azure)

<h2>Technologies Utilized</h2>

- Microsoft Azure (Compute/Virtual Machines)
- Remote Desktop Protocol (RDP) via mstsc
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems</h2>

- Windows 11 Home (24H2)
- Windows Server 2022
- Windows 10 (21H2)

<h2>Procedure Overview</h2>

- Step 1: Install Active Directory
- Step 2: Create a Domain Admin User within the Domain
- Step 3: Join Client-1 to the Domain
- Step 4: Setup Remote Desktop for non-admin users on Client-1

## Step 1: Installing Active Directory Domain Services</h3>

### Connect to DC-1

<p>
<img width="800" alt="DAD1" src="https://github.com/user-attachments/assets/48bd2d15-04df-4330-88bc-c38034ce5276"/>
</p>


<p>On your local Windows device, open the Run dialog (Windows + R), type mstsc, and press Enter to launch the Remote Desktop Connection client.

Enter the Public IP address of DC-1, connect, and authenticate using the original admin credentials.</p>
<br />

### Initiate Role Installation:

<p>
<img width="800" alt="DAD2" src="https://github.com/user-attachments/assets/ce6d8d5e-7fd5-4617-bc95-ebc5cca2df71"/>
<img width="800" alt="DAD2" src="https://github.com/user-attachments/assets/c800aa1a-d183-452a-afbe-230e334dbb11"/>
<img width="800" alt="DAD2" src="https://github.com/user-attachments/assets/2abfb636-b832-4a67-a95d-a9944a7b24bd"/>
</p>

<p>
In Server Manager, select Add roles and features and click Next.

Choose Role-based or feature-based installation, select DC-1 as the destination server, and click Next.
</p>

<br/>

### Enable Active Directory:

<p>
<img width="800" alt="DAD3" src="https://github.com/user-attachments/assets/1f0466d5-b976-40fc-939d-26a1559e6576"/>
</p>

<p>Under Server Roles, check Active Directory Domain Services (AD DS). When prompted, click Add Features to include required components. 

Confirm the AD DS checkbox is selected and click Next through subsequent screens until the confirmation page.</p>

<br/>

### Install AD DS:

<p>
<img width="800" alt="DAD4" src="https://github.com/user-attachments/assets/efb102a4-1dd7-4bf8-94a8-6d12fabe795c"/>
</p>
<p>
Check Restart the destination server automatically if required, confirm the prompt, and click Install.

After installation completes, click Close.</p>
<br />

### Promote to Domain Controller:

<p>
<img width="800" alt="image" src="https://github.com/user-attachments/assets/fc7275bc-9ad5-47a2-a0b1-5e2d7807daa4"/>
</p>

<p>
In Server Manager, locate the yellow triangle with a flag in the top-right corner and click it.

Select Promote this server to a domain controller.

In the deployment configuration, choose Add a new forest and enter mydomain.com as the domain name. Click Next.</p>
<br/>

<p>
<img width="800" alt="image" src="https://github.com/user-attachments/assets/cb5dfdbf-04c5-46fc-859f-1fb85eebec0e"/>
</p>

<p>
Set the Directory Services Restore Mode (DSRM) password to Password1 (for this project only) and click Next.</p>
<br/>

<p>
<img width="800" alt="image" src="https://github.com/user-attachments/assets/6ea526dc-8177-41a7-9e83-ed6a882a837b"/>
</p>

<p>
Uncheck Create DNS delegation and proceed through remaining options by clicking Next until the installation prompt.

Click Install. DC-1 will reboot upon completion, indicating it is now a domain controller.</p>
<br/>

