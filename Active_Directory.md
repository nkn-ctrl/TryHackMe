# Active Directory

The main idea behind a domain is to centralise the administration of common components of a Windows computer network in a single repository called Active Directory (AD). The server that runs the Active Directory services is known as a Domain Controller (DC).
<br>

*Users*  
Users can be used to represent two types of entities:
- People: users will generally represent persons in your organisation that need to access the network, like employees.
- Services: you can also define users to be used by services like IIS or MSSQL. Every single service requires a user to run, but service users are different from regular users as they will only have the privileges needed to run their specific service.  
<br>

*Machines*  
The machine account name is the computer's name followed by a dollar sign. For example, a machine named `DC01` will have a machine account called `DC01$`.  
<br>

*Security Groups*    
|Security Group|	Description|
|:------------:|---------------|
|Domain Admins|	Users of this group have administrative privileges over the entire domain. By default, they can administer any computer on the domain, including the DCs.|
|Server Operators|	Users in this group can administer Domain Controllers. They cannot change any administrative group memberships.|
|Backup Operators|	Users in this group are allowed to access any file, ignoring their permissions. They are used to perform backups of data on computers.|
|Account Operators|	Users in this group can create or modify other accounts in the domain.|
|Domain Users|	Includes all existing user accounts in the domain.|
|Domain Computers|	Includes all existing computers in the domain.|
|Domain Controllers|	Includes all existing DCs on the domain.|

## Active Directory Users and Computers
**Organizational Units (OUs)** which are container objects that allow you to classify users and machines.   
<img src="https://github.com/nkn-ctrl/pushtest/blob/main/AD_OUs.png" width="600">

- Builtin: Contains default groups available to any Windows host.
- Computers: Any machine joining the network will be put here by default. You can move them if needed.
- Domain Controllers: Default OU that contains the DCs in your network.
- Users: Default users and groups that apply to a domain-wide context.
- Managed Service Accounts: Holds accounts used by services in your Windows domain.