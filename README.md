<img src="https://github.com/AnderMendoza/AnderMendoza/raw/main/assets/line-neon.gif" width="100%">

![Github Banner 4 - Active Directory Deployment and Configuration](https://github.com/user-attachments/assets/883dd7bb-8ec2-4b12-93c2-a9a4dc5cfd2d)

<img src="https://github.com/AnderMendoza/AnderMendoza/raw/main/assets/line-neon.gif" width="100%">

# Implementing Active Directory on Azure Virtual Machines

This guide details deploying and configuring an on-premises Active Directory environment within Azure Virtual Machines, building on previously established infrastructure.

## 🚨 Prerequisites

- [Creating Virtual Machines in the Cloud](https://github.com/mxwllslvr/creating-virtual-machines-in-the-cloud)
- [Establishing Active Directory Infrastructure in Microsoft Azure](https://github.com/mxwllslvr/establishing-active-directory-infrastructure-in-microsoft-azure)

## Technologies Utilized

- Microsoft Azure (Compute/Virtual Machines)
- Remote Desktop Protocol (RDP) via mstsc
- Active Directory Domain Services
- PowerShell

## Operating Systems

- Windows 11 Home (24H2)
- Windows Server 2022
- Windows 10 (21H2)

## Procedure Overview

1. Install Active Directory
2. Create a Domain Admin User within the Domain
3. Join Client-1 to the Domain
4. Setup Remote Desktop for non-admin users on Client-1

---

## Step 1: Installing Active Directory Domain Services

### Connect to DC-1

![DC-1 Connection](https://github.com/user-attachments/assets/48bd2d15-04df-4330-88bc-c38034ce5276)

On your local Windows device, open the Run dialog (**Windows + R**), type `mstsc`, and press **Enter** to launch the Remote Desktop Connection client.

Enter the Public IP address of **DC-1**, connect, and authenticate using the original admin credentials.

### Initiate Role Installation

![Server Manager Add Roles](https://github.com/user-attachments/assets/ce6d8d5e-7fd5-4617-bc95-ebc5cca2df71)

![Role Installation Type](https://github.com/user-attachments/assets/c800aa1a-d183-452a-afbe-230e334dbb11)

![Server Selection](https://github.com/user-attachments/assets/2abfb636-b832-4a67-a95d-a9944a7b24bd)

1. In Server Manager, select **Add roles and features** and click **Next**
2. Choose **Role-based or feature-based installation**, select **DC-1** as the destination server, and click **Next**

### Enable Active Directory

![Active Directory Selection](https://github.com/user-attachments/assets/1f0466d5-b976-40fc-939d-26a1559e6576)

1. Under **Server Roles**, check **Active Directory Domain Services (AD DS)**
2. When prompted, click **Add Features** to include required components
3. Confirm the AD DS checkbox is selected and click **Next** through subsequent screens until the confirmation page

### Install AD DS

![AD DS Installation](https://github.com/user-attachments/assets/efb102a4-1dd7-4bf8-94a8-6d12fabe795c)

1. Check **Restart the destination server automatically if required**, confirm the prompt, and click **Install**
2. After installation completes, click **Close**

### Promote to Domain Controller

![Domain Controller Promotion](https://github.com/user-attachments/assets/fc7275bc-9ad5-47a2-a0b1-5e2d7807daa4)

1. In Server Manager, locate the yellow triangle with a flag in the top-right corner and click it
2. Select **Promote this server to a domain controller**
3. In the deployment configuration, choose **Add a new forest** and enter `mydomain.com` as the domain name. Click **Next**

![DSRM Password](https://github.com/user-attachments/assets/cb5dfdbf-04c5-46fc-859f-1fb85eebec0e)

Set the Directory Services Restore Mode (DSRM) password to `Password1` (for this project only) and click **Next**.

![DNS Delegation](https://github.com/user-attachments/assets/6ea526dc-8177-41a7-9e83-ed6a882a837b)

1. Uncheck **Create DNS delegation** and proceed through remaining options by clicking **Next** until the installation prompt
2. Click **Install**. **DC-1** will reboot upon completion, indicating it is now a domain controller

### Reconnect to DC-1

![Domain Login](https://github.com/user-attachments/assets/cb315c48-3a78-4b24-bcb7-21c31910b6bf)

Using `mstsc`, reconnect to **DC-1's** Public IP. Log in as a domain user by entering `mydomain.com\username` with the original password.

**Post-login observation**: Active Directory Domain Services is installed, and **DC-1** is promoted to a domain controller for `mydomain.com`, ready for user and client management.

---

## Step 2: Establishing a Domain Admin User

### Access Active Directory Tools

![ADUC Access](https://github.com/user-attachments/assets/a4dcb5e1-d81b-4221-92c3-03aade9a2f21)

On **DC-1**, from the Start menu, navigate to **Windows Administrative Tools** and open **Active Directory Users and Computers (ADUC)**.

### Create Organizational Units

![Create OUs](https://github.com/user-attachments/assets/00a56f2a-d06f-464c-89c4-4a9e8b3356f0)

1. Right-click `mydomain.com`, select **New > Organizational Unit**, name it `_EMPLOYEES` (with underscore), and click **OK**
2. Repeat the process to create another OU named `_ADMINS` (with underscore)
3. Right-click `mydomain.com` and select **Refresh** to reposition the new OUs at the top of the list

### Add a New User

![Create User](https://github.com/user-attachments/assets/cb0afda7-4147-483c-b885-4169c5c06c83)

1. In the **_ADMINS** OU, right-click, select **New > User**
2. Enter **Full name**: `Jane Doe`, **User logon name**: `jane_admin`, and click **Next**

![User Password](https://github.com/user-attachments/assets/c09f4ba1-1e2b-4a73-9aed-8f16a7127449)

1. Set the password to match **DC-1's** admin password for simplicity
2. Uncheck **User must change password at next logon** and check **Password never expires** (for this project only)
3. Click **Next** and **Finish**

### Assign Domain Admin Rights

![Domain Admin Assignment](https://github.com/user-attachments/assets/c09f4ba1-1e2b-4a73-9aed-8f16a7127449)

1. In the **_ADMINS** OU, right-click the **Jane Doe** user and select **Properties**
2. Go to the **Member Of** tab, click **Add**, type `Domain Admins`, and click **Check Names**
3. Verify **Domain Admins** is capitalized and underlined, then click **OK**
4. Confirm the group membership and click **OK** to close the properties

### Switch to Jane Doe

![Jane Admin Login](https://github.com/user-attachments/assets/d9a05311-c7ab-4140-9a15-d004690baf34)

1. Log out of **DC-1**
2. Reconnect via `mstsc` using `mydomain.com\jane_admin` and the assigned password
3. Use this account for subsequent steps

**Observation**: The `jane_admin` user is created within the **_ADMINS** OU and granted Domain Admin privileges, enabling administrative tasks across the domain.

---

## Step 3: Integrating Client-1 into the Domain

### Connect to Client-1

Using `mstsc`, connect to **Client-1's** Public IP with the original local admin account.

### Modify System Settings

![System Properties](https://github.com/user-attachments/assets/69c4f36d-37b8-4a58-a986-76343f747fa3)

1. Right-click the Start menu, select **System**, and scroll to **Rename this PC (advanced)** (expand the window if not visible)
2. In the System Properties window, go to the **Computer Name** tab and click **Change**
3. Under **Member of**, select **Domain**, enter `mydomain.com`, and click **OK**

### Authenticate Domain Join

![Domain Authentication](https://github.com/user-attachments/assets/f6c1526a-c752-456b-a43b-3800166b7dee)

1. When prompted, enter `mydomain.com\jane_admin` and the corresponding password, then click **OK**
2. A confirmation window (possibly obscured behind the System screen) will appear. Close the System Properties window to reveal the "Welcome to the mydomain.com domain" message, then click **OK**
3. Click **Restart Now** to reboot **Client-1** and apply the domain join

### Verify Domain Membership

![Verify Domain Join](https://github.com/user-attachments/assets/58e10cf1-4512-441d-bbdc-7464ff779cc9)

1. Reconnect to **DC-1** via `mstsc` using `mydomain.com\jane_admin`
2. Open **Active Directory Users and Computers**, expand `mydomain.com`, and select the **Computers** container
3. Confirm **Client-1** is listed

### Organize Client-1

![Create Clients OU](https://github.com/user-attachments/assets/d62a2a34-5b34-4807-9c5d-bcc9d04ab03d)

1. Right-click `mydomain.com`, select **New > Organizational Unit**, and name it `_CLIENTS` (with underscore). Click **OK**
2. In the **Computers** container, drag **Client-1** to the **_CLIENTS** OU. Confirm the move by clicking **Yes**
3. Optionally, refresh `mydomain.com` to reposition **_CLIENTS** alongside **_ADMINS** and **_EMPLOYEES**

![Client Organization Complete](https://github.com/user-attachments/assets/017139b2-8685-4fd7-8b07-f8449cdbd68f)

**Observation**: **Client-1** is successfully joined to `mydomain.com` and organized within the **_CLIENTS** OU, ensuring proper domain integration.

---

## Step 4: Configuring Remote Desktop Access for Non-Admin Users on Client-1

![Remote Desktop Configuration](https://github.com/user-attachments/assets/4026df74-37bd-4b6d-bff7-2a2fa64ad81a)

### Access Client-1 as Admin

Using `mstsc`, connect to **Client-1** with `mydomain.com\jane_admin`.

### Enable Remote Desktop

1. Right-click the Start menu, select **System**, and click **Remote Desktop**
2. Under **Remote Desktop Users**, click **Add**

### Grant Domain Users Access

1. Type `domain users`, click **Check Names**, and verify **Domain Users** is capitalized and underlined. Click **OK**
2. Confirm **MYDOMAIN\Domain Users** appears in the Remote Desktop Users list, then click **OK**

**Observation**: Non-admin users in the **Domain Users** group can now access **Client-1** via Remote Desktop, enhancing domain-wide accessibility.

---

<br/><br/>

<div align="center"> <img src="https://media2.giphy.com/media/v1.Y2lkPTc5MGI3NjExY2IwMGpsbDB1dXgwMHk4bTdwb3FqZm9udWJqMXpvcXBidWpsMGl3ZCZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/xUNda6syjHI5da4TsY/giphy.gif" width="100%"> </div>

<br/><br/>

<img src="https://github.com/AnderMendoza/AnderMendoza/raw/main/assets/line-neon.gif" width="100%">

## Conclusion

This procedure successfully deployed and configured Active Directory within Azure Virtual Machines, establishing **DC-1** as a domain controller for `mydomain.com`, integrating **Client-1** into the domain, and enabling Remote Desktop access for non-admin users.

**Key Achievements:**
- Installed and configured Active Directory Domain Services on **DC-1**
- Created organizational units for better user and computer management
- Established a domain administrator account (`jane_admin`)
- Successfully joined **Client-1** to the domain
- Configured Remote Desktop access for domain users

This setup prepares the environment for adding domain users in future projects, showcasing the power of cloud-based Active Directory. To manage costs, stop both VMs in Azure when not in use. This concludes the deployment process, equipping you with essential Active Directory administration skills.

<img src="https://github.com/AnderMendoza/AnderMendoza/raw/main/assets/line-neon.gif" width="100%">

<br/><br/>

<div align="center">

<a href="HTTP://www.github.com/mxwllslvr">- BACK TO MAIN PAGE -</a>

</div>
