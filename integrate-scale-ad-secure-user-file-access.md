---

copyright:
  years: 2022, 2023
lastupdated: "2023-10-18"

keywords: 

subcollection: storage-scale

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:external: target="_blank" .external}
{:pre: .pre}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:ui: .ph data-hd-interface='ui'}
{:cli: .ph data-hd-interface='cli'}
{:api: .ph data-hd-interface='api'}
{:table: .aria-labeledby="caption"}
 
#  Integrating IBM Storage Scale with Active Directory for Secure User Authentication and NFS File Access 
{: #integrate-scale-ad-secure-user-nfs}

## Introduction
{: #scale-ad-nfs-secure-user-intro}

Establish a robust authentication system by using Active Directory ensuring seamless file access, particularly through the NFS protocol. 
The procedure covers how to:
* Configure {{site.data.keyword.scale_full_notm}} nodes to directly integrate with Active Directory, enabling user authentication and access control.
* Enabling RFC2307 attributes within Active Directory. This is crucial for mapping user and group identifiers between AD and the UNIX environment.

## General prerequisites
{: #gen-prerequisites}

Before the configuration process, make sure that these prerequisites are met:

1.	For installing and configuring Active Directory and DNS Server:
	*  A Windows Server 2019 system with administrative privileges.
	*  Basic familiarity with PowerShell commands and Windows Server management.
2.	For Connecting RHEL Systems Directly to AD using Samba Winbind:
	*  An RHEL system capable of running Samba Winbind.
	*  Familiarity with RHEL command-line interface and system configuration.
	*  Access to the Active Directory domain and appropriate permissions to join the domain.

## Network prerequisites
{: #network-prereq}

Before proceeding with the configuration of {{site.data.keyword.scale_full_notm}} to use the Active Directory and connecting RHEL systems to AD, make sure that the following network prerequisites are met:

1.	Network connectivity: You need stable and reliable network connectivity between the Windows Server 2019 system (where Active Directory and DNS are installed) and the RHEL systems that are joined to the domain. Verify that there are no network communication issues or firewalls blocking essential ports.

2.	Domain controller reachability: Confirm that the RHEL systems can reach the Active Directory domain controllers without any connectivity issues. Use tools like "ping" or "nslookup" to verify the ability to resolve the domain controller's hostname and IP address from the RHEL systems.

3.	Time synchronization: Make sure that all systems participating in the Active Directory domain, including the Windows Server 2019 system and RHEL systems, have their clocks that are synchronized with a reliable time source. Time synchronization is critical for proper authentication and Kerberos ticket validation.

4.	Domain DNS configuration: The Active Directory domain must have properly configured DNS settings. The domain controllers IP address should be set as the primary DNS server on all systems (including the Windows Server 2019 system and RHEL systems) that is part of the AD domain.

5.	DNS resolution: Verify that both forward and reverse DNS resolutions are functioning correctly. The domain controller's hostname must be resolvable from the Windows Server 2019 system and RHEL systems, and the Windows Server 2019 systems hostname must be resolvable from the RHEL systems.

6.	DNS domain name: Make sure that the DNS domain name of the Active Directory matches the domain name that is used during the configuration process. In this case, the domain name "POCDOMAIN.LOCAL" used for the Active Directory must be consistent throughout the configuration.

7.	Firewall rules: Review and update firewall rules to allow the necessary communication between the Windows Server 2019 system, RHEL systems, and the domain controllers. Key ports that are used for AD communication include TCP/UDP 53 (DNS), TCP/UDP 88 (Kerberos), TCP 135 (RPC), TCP/UDP 389 (LDAP), TCP/UDP 445 (SMB), and TCP/UDP 636 (LDAPS).

8.	Active Directory user account with administrative privileges: Make sure that an Active Directory user account with administrative privileges is available to be used during the configuration process. This account is used to promote the Windows Server 2019 system as a domain controller and to join the RHEL systems to the AD domain.

### Before you begin
{: #before-you-begin}

For this procedure you need:
* A Windows Server 2019 system with administrative privileges.
* Basic familiarity with PowerShell commands and Windows Server management.

## Procedure Installing and Configuring Active Directory and DNS Server on Windows Server 2019 using PowerShell
{: #procedure}

1. Open PowerShell with Administrative Privileges:
    a.  Press the "Windows + X" keys on your keyboard to access the Power User menu.
    b.  From the list, choose "Windows PowerShell (Admin)" to start an elevated PowerShell session with administrative privileges.

2. Install the Active Directory Domain Services Role and DNS Server Role by running these PowerShell commands:

   ```shell
   powershell code
   # Install the Active Directory Domain Services Role and DNS Server Role
Install-WindowsFeature -Name AD-Domain-Services, DNS -IncludeManagementTools
   ```

3. Promote the Server to a Domain Controller with Integrated DNS by executing these PowerShell commands:

   ```shell
   powershell code
   # Set the required parameters
   $DomainName = "POCDOMAIN.LOCAL"
   $SafeModePassword = ConvertTo-SecureString -AsPlainText "MySecureDSRMpwd2023!" -Force

   # Configure and promote the server as a domain controller with integrated DNS
   Install-ADDSForest -DomainName $DomainName -SafeModeAdministratorPassword  $SafeModePassword -DomainMode Win2019 -ForestMode Win2019 -InstallDNS
   ```
 
   Example values:
   *  Domain Name: POCDOMAIN.LOCAL
   *  DSRM Password: MySecureDSRMpwd2023!

   When you run the PowerShell commands, the Active Directory Domain Services and DNS Server roles are installed and configured on the server.

   The server automatically restarts to complete the domain controller promotion process.

4.  After the server restarts, log in using the domain administrator account that you created during the promotion process to verify Active Directory and DNS Configuration.

### Verify Active Directory and DNS Configuration
{: #verfy-dns-ad-config}

Verify that Active Directory and DNS are configured correctly by checking:

1. Verify Active Directory Management
   * Open "Server Manager," and in the "Dashboard," confirm the presence of "Active Directory Users and Computers" and "DNS" listed under "Tools." This indicates successful installation of the Active Directory and DNS management tools.
2. Start the Active Directory Users and Computers to manage user accounts, groups, and organizational units (OUs) within the domain.
3. Access DNS Manager to manage DNS zones and records for the domain.
4. On a client system within the same network, configure the DNS settings to point to the IP address of the newly promoted domain controller.
5.  Attempt to join the client system to the "POCDOMAIN.LOCAL" domain. A successful connection confirms proper DNS resolution and functional Active Directory domain services.

