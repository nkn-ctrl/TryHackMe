# Active Directory

The main idea behind a domain is to centralise the administration of common components of a Windows computer network in a single repository called Active Directory (AD). The server that runs the Active Directory services is known as a Domain Controller (DC).

Security Groups  
|Security Group|	Description|
|:------------:|---------------|
|Domain Admins|	Users of this group have administrative privileges over the entire domain. By default, they can administer any computer on the domain, including the DCs.|
|Server Operators|	Users in this group can administer Domain Controllers. They cannot change any administrative group memberships.|
|Backup Operators|	Users in this group are allowed to access any file, ignoring their permissions. They are used to perform backups of data on computers.|
|Account Operators|	Users in this group can create or modify other accounts in the domain.|
|Domain Users|	Includes all existing user accounts in the domain.|
|Domain Computers|	Includes all existing computers in the domain.|
|Domain Controllers|	Includes all existing DCs on the domain.|
