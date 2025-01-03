# Active Directory Basics
## Windows Domain
A **Windows domain** is a group of users and computers under the administration of a given business.

**Active Directory** is a directory service. It stores information about network objects such as computers users and groups. 
Credentials are stored in it.

The server that runs the Active Directory services is known as a **Domain Controller (DC)**.

All computers in the windows domain are controlled by Domain Controller with the AD service.
## Active Directory
### Network Objects
**Active Directory Domain Service (AD DS)**, the core of windows.
#### Users

Users, are the security principals, can be authenticated to act upon resources in the network.

Users can be used to represent two types of entities.
- **People**
- **Services**: Services also need assigned user to offer service, and will be given permission.
#### Machines

Machines can also be considered as security principal.

And themselves have a built-in system account, but the password is automatically generated, very complex (120 random characters).
 
A machine named `DC01` will have a machine account called `DC01$`

#### Security Groups

Security principal.

A group can be comprised of users and machines.

Default groups:

|                    |                                                                                                                                                           |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Security Group** | **Description**                                                                                                                                           |
| Domain Admins      | Users of this group have administrative privileges over the entire domain. By default, they can administer any computer on the domain, including the DCs. |
| Server Operators   | Users in this group can administer Domain Controllers. They cannot change any administrative group memberships.                                           |
| Backup Operators   | Users in this group are allowed to access any file, ignoring their permissions. They are used to perform backups of data on computers.                    |
| Account Operators  | Users in this group can create or modify other accounts in the domain.                                                                                    |
| Domain Users       | Includes all existing user accounts in the domain.                                                                                                        |
| Domain Computers   | Includes all existing computers in the domain.                                                                                                            |
| Domain Controllers | Includes all existing DCs on the domain.                                                                                                                  |

### Real Example

Open the Active Directory.

![[Pasted image 20241224094554.png]]

As you can see, there are many folders, maybe groups and something.

Do you notice that, `THM` folder looks different, because it is an OU.

In Windows domains, Organizational Unit (OU) refers to containers that hold users, groups and computers to which similar policies should apply.

Other containers (or folder):
- **Builtin:** Contains default groups available to any Windows host.
- **Computers:** Any machine joining the network will be put here by default. You can move them if needed.
- **Domain Controllers:** Default OU that contains the DCs in your network.
- **Users:** Default users and groups that apply to a domain-wide context.
- **Managed Service Accounts:** Holds accounts used by services in your Windows domain.

- **OUs:** **applying policies** 
- **Security Groups:** **grant permissions over resources** 

## Managing Users in AD
### Delete OUs
In general case, the OU will be protected to avoid incidental deletion.

`View` > `Advanced Features` 
You can see some changes occurs.

And go to `Research and Development` > `Properties` > `Object`, uncheck the checkbox.

### Delegation

You can set a delegation who will fully control the OU without a domain admin.

Give IT Support Phillip the permission to reset password in Sales Department.

`Sale` > `right click` > `Delegate control` > `add name (using check name)` > `check the "reset password and force..."` > `finish`

Then connect to phillip.
`Install remmina on kali` > `Configure`

![[Pasted image 20241224103740.png]]

When you connect, you may wanna open Active Directory, but phillip doesn't have the permission. You should run the `powershell`.

`Change password`:
```sh
Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose
```

`Claire2008`

`Change password at LogOn`
```sh
Set-ADUser -ChangePasswordAtLogon $true -Identity sophie -Verbose
```

## Managing Computers in AD

All the computer can be divided into at least three categories:
- Workstations: For work, shouldn't be assigned a privileged user.
- Servers: Offer service like database, web application and soon.
- Domain Controllers: DCs

## Group Policies

Windows manages such policies through **Group Policy Objects (GPO)**.
GPOs are simply a collection of settings that can be applied to OUs.

To configure GPO, use the `Group Policy Management`.
![[Pasted image 20241224111719.png]]

![[Pasted image 20241228170234.png]]

Any GPOs are in the `Group Policy Objects` folder.
You can create a GPO there and link them to domain.

Editor: 
![[Pasted image 20241228170717.png]]

Two example:
- Restrict control panel
- Auto lock

Note:
- Any subdomain will inherit its parent domain's policy
- Users will ignore the computer configuration. 
- GPOs are distributed to the network via a network share called `SYSVOL`, which is stored in the DC.
Once a change has been made to any GPOs, it might take up to 2 hours for computers to catch up.
This command will force user to update their policy (on DC):
```cmd
PS C:\> gpupdate /force
```
## Authentication Methods

All the credentials are stored in DC.
### Kerberos Authentication (Common)

In an nutshell: First request a primary ticket, and then use this ticket (TGT) to request specific ticket (TGS) for specific service, finally complete the authentication to a service

![[Pasted image 20241228175221.png]]
![[Pasted image 20241228175238.png]]
![[Pasted image 20241228175312.png]]

### NetNTLM Authentication

When you request a service, it turn to DC to authenticate it.
If you are a local user, you don't need to do that.

![[Pasted image 20241228175614.png]]
Challenge: just a random number.

## Trees, Forests and Trusts

Tree: domains share the same namespace.
![[Pasted image 20241228181437.png]]

Forests: different namespace
![[Pasted image 20241228181524.png]]

Trust relationship:
- one-way
- two-way
A trust relationship means you have the chance to authorize users, but it's up to you what is actually authorized or not.

