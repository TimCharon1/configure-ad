![image](https://github.com/user-attachments/assets/2ba01b32-aab9-4e2c-9ba2-6164a3522984)


<h1>Active Directory - Prerequisites and Installation</h1>
This tutorial outlines the prerequisites and installation of Active Directory as well as connecting a main client to a Domain Controller.<br />


- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Internet Information Services (IIS)

<h2>Operating Systems Used </h2>

- Windows 10</b> (21H2)

<h2>Installation Steps</h2>

![image](https://github.com/user-attachments/assets/9f2d79a3-dc0a-42d7-8846-3844881c6ea3)
![image](https://github.com/user-attachments/assets/f0265b52-c7e8-404c-b46b-7fe476c78f35)

![image](https://github.com/user-attachments/assets/808056c6-9687-44c4-b531-0bfdde0d13c7)

<p>
🏗️ Domain Controller Setup (Azure)

* Create a **Resource Group**
* Create a **Virtual Network** and **Subnet**
* Deploy a **Windows Server 2022 VM** named `DC-1`

  * Username: `labuser`
  * Password: `Cyberlab123!`
* Set **Private IP** on `DC-1` NIC to **Static**
* Log into `DC-1` via RDP and **disable Windows Firewall** (for testing)

</p>
<br />

![image](https://github.com/user-attachments/assets/0f826d04-29a9-4b0e-ae3c-fcc2cd07cab6)
<img width="648" alt="image" src="https://github.com/user-attachments/assets/370ea6b3-f1be-44e8-8611-cbf77e8d442f" />



<p>

🖥️ Client-1 Setup (Azure)

* Create a **Windows 10 VM** named `Client-1`

  * Username: `labuser`
  * Password: `Cyberlab123!`
* Attach to the **same region and Virtual Network** as `DC-1`
* Set **DNS settings** to `DC-1`'s **Private IP**
* Restart `Client-1` from the Azure Portal
* Log in and **ping DC-1’s Private IP** to confirm connectivity
* In PowerShell, run: `ipconfig /all`

  * Verify DNS shows `DC-1`'s Private IP
<br />

![image](https://github.com/user-attachments/assets/8bb89543-6385-48e2-8d39-89e5e9c4ddb2)

<p>
🏛️ Install Active Directory on DC-1

* Log into `DC-1`
* Install **Active Directory Domain Services (AD DS)**
* Promote the server to a **Domain Controller**

  * Create a **new forest**: `mydomain.com` *(you can choose any domain name)*
* Restart the VM
* Log back in as: `mydomain.com\labuser`


</p>
<br />

![image](https://github.com/user-attachments/assets/cb136002-9ae8-43bc-9aa2-9b2d925db7b4)
![image](https://github.com/user-attachments/assets/26aed8b0-fd25-4aef-812d-7259c76e418e)


<p>
👩‍💼 Create Domain Admin User

* On `DC-1`, open **Active Directory Users and Computers (ADUC)**
* Create two OUs:

  * `_EMPLOYEES`
  * `_ADMINS`
* Create user:

  * **Name:** Jane Doe
  * **Username:** `jane_admin`
  * **Password:** `Cyberlab123!`
* Move `jane_admin` to `_ADMINS` OU
* Add `jane_admin` to the **Domain Admins** security group
* Log out and back in as: `mydomain.com\jane_admin`
* Use `jane_admin` as your admin account going forward

</p>
<br />

<img width="584" alt="image" src="https://github.com/user-attachments/assets/aed4acf1-cf4b-4f02-8140-c84f8c0983e5" />
<img width="262" alt="image" src="https://github.com/user-attachments/assets/9a19bd13-55e9-4909-b054-88164f343f53" />
<img width="518" alt="image" src="https://github.com/user-attachments/assets/f5777d46-a9a9-453d-a6ad-6d8f6b75e262" />


<p>👥 Bulk Create Users & Test Domain Login

* Log into `DC-1` as `jane_admin`
* Open **PowerShell ISE** as Administrator
* Create a new script file and **paste the user creation script**
* Run the script and observe users being created in the `_EMPLOYEES` OU
* Open **Active Directory Users and Computers (ADUC)** to verify accounts

🔑 Test Login
On Client-1, attempt to log in with one of the new user accounts

Use the password defined in the script

Confirms successful domain replication and authentication across the network.

</p>

<img width="954" alt="image" src="https://github.com/user-attachments/assets/a287905e-9278-4bd0-9e14-e478b8c23b6f" />

<p>
 Dealing with Account Lockouts
Get logged into dc-1
Pick a random user account you created previously
Attempt to log in with it 10 times with a bad password

Configure Group Policy to Lockout the account after 5 attempts


🔒 Configure Account Lockout Policy via Group Policy

1. **Open Group Policy Management Console (GPMC)**

   * Log into `DC-1`
   * Run: `gpmc.msc`

2. **Create or Edit a GPO**

   * Navigate to: `Group Policy Objects`
   * Create a new GPO (e.g., **"Account Lockout Policy"**) or edit an existing one

3. **Locate Account Lockout Policy Settings**

   * Path:
     `Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Account Lockout Policy`

4. **Configure These Settings:**

   * **Account Lockout Duration:** 10 minutes
   * **Account Lockout Threshold:** 5 invalid logon attempts
   * **Reset Counter After:** 10 minutes
</p>

<img width="908" alt="image" src="https://github.com/user-attachments/assets/cebef8c5-b84a-46a0-a7d6-8410996f9419" />

<p>
 🚫 Enable/Disable Accounts & Observe Logs

🔒 Disable & Re-enable User Account

* In **Active Directory Users and Computers (ADUC)** on `DC-1`:

  * Right-click a user and select **Disable Account**
* Attempt to log in on `Client-1` with the disabled account
  → 🛑 **Expected:** "Your account has been disabled" error
* Back in ADUC, **Re-enable** the account
* Attempt login again → ✅ **Expected:** Successful login

📋 Observe Security Logs

* On `DC-1` (Domain Controller):

  * Open **Event Viewer → Windows Logs → Security**
  * Look for events related to account logon attempts or lockouts
* On `Client-1`:

  * Open **Event Viewer** and check local security logs for failed/successful logon attempts


</p>





