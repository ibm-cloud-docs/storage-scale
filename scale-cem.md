---

copyright:
  years: 2023
lastupdated: "2023-10-05"

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
{:faq: data-hd-con
{:step: data-tutorial-type='step'}
{:table: .aria-labeledby="caption"}

# Integrate Scale with CES and enable LDAP for NFS on Scale nodes 
{: #scale-cem-intro}

You can integrate Scale with CES services and enable LDAP-based authentication for NFS services on CES Scale Nodes. The information includes assumptions, a step-by-step guide, and detailed explanations for each configuration. By using LDAP-based authentication with an external LDAP server, organizations can enhance security and centralize user management for NFS shares and configuration steps. This enables the LDAP-based authentication and provide instructions for setting up an OpenLDAP server on a Linux system.

## Step 1 - Configuration for OpenLDAP Server setup
{: #configure-ldap-server}

Make sure that the LDAP server is properly configured with the required schemas that are installed to handle authentication and ID-mapping requests. If SMB data access is required, the LDAP schema must be extended to store additional attributes such as SID and password hash.

### Configuring an OpenLDAP Server
{: #configure-openldap-server}

OpenLDAP is an open source implementation of the Lightweight Directory Access Protocol (LDAP) that can be used to store and manage information about users, groups, and other objects in a network. This document provides step-by-step instructions for configuring an OpenLDAP server on a Linux system.

### Before you begin - OpenLDAP Server
{: #beforeyoubegin-openldap}

You need to have access to a Linux system with root privileges. You need to have a basic understanding of the command-line interface and how to use a text editor.

### Configuring an OpenLDAP Server
{: #proc-config-openldap}


Use these steps to configure an OpenLDAP server:

1.	Install the OpenLDAP server and client packages

    ```
    Code:
    yum -y install openldap-servers openldap-clients 

    ```
2.	Copy the DB_CONFIG.example file to the /var/lib/ldap directory and change its ownership to the ldap user:

    ```
    Code:
    cp /usr/share/openldap-servers/DB_CONFIG.example /var/lib/ldap/DB_CONFIG chown ldap. /var/lib/ldap/DB_CONFIG 
    ```

3.	Start the slapd service and enable it to start automatically at boot time by running the following commands:

    ```
    Code:
    systemctl start slapd systemctl enable slapd 
    ```

4.	Generate an admin password by running the slappasswd command. You are prompted to enter a password. For example:

    ```
    Code:
    slappasswd 
    ```
    
    You see an output that looks something like this:

    ```Code:
    {SSHA}FUMV8TZ9lZQxABxCBE5UZ+oU/dlwf/d4 
    ```
    The password hash that is generated (in this case, {SSHA}         FUMV8TZ9lZQxABxCBE5UZ+oU/dlwf/d4) as you need it later.
    {: note} 

5.	Create a file that is named `chrootpw.ldif` and add the following lines to it:

    ```
    Code:
    dn: olcDatabase={0}config,cn=config
    changetype: modify
    add: olcRootPW
    olcRootPW: {SSHA}FUMV8TZ9lZQxABxCBE5UZ+oU/dlwf/d4]
    ```

    Replace the `olcRootPW`` value with the password hash that you generated in the step 4.

6.	Import the basic schema by running the following commands:

    ```
    Code:
    ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif 
    ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif 
    ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif 
    ```

7.	Generate a manager password by running the slappasswd command again.  For example:

    ```
    Code:
    slappasswd 
    ```

    You see an output that looks something like this:


    ```
    Code:
    {SSHA}TVW9z6WLIBC3EXtFHFWnb2EVlK7EZQ3b 
    ``````
    
    Make note of the password hash that is generated (in this case, {SSHA}TVW9z6WLIBC3EXtFHFWnb2EVlK7EZQ3b) as you need it in the next step.

8.	Add the manager password and enable the manager account by creating a file that is named `chdomain.ldif` and adding these lines to it:

    ```
    # DC should be your domain
    # specify the password generated above for "olcRootPW" section
    dn: olcDatabase={1}monitor,cn=config
    changetype: modify
    replace: olcAccess
    olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth"
     read by dn.base="cn=Manager,dc=ibmscale,dc=com" read by * none
    dn: olcDatabase={2}hdb,cn=config
    changetype: modify
    replace: olcSuffix
    olcSuffix: dc=ibmscale,dc=com
    dn: olcDatabase={2}hdb,cn=config
    hangetype: modify
    replace: olcRootDN
    olcRootDN: cn=Manager,dc=ibmscale,dc=com
    dn: olcDatabase={2}hdb,cn=config
    changetype: modify
    add: olcRootPW
    olcRootPW: {SSHA}TVW9z6WLIBC3EXtFHFWnb2EVlK7EZQ3b)
    dn: olcDatabase={2}hdb,cn=config
    changetype: modify
    add: olcAccess
    olcAccess: {0}to attrs=userPassword,shadowLastChange by
      dn="cn=Manager,dc=ibmscale,dc=com" write by anonymous auth by self write by * none
    olcAccess: {1}to dn.base="" by * read
    olcAccess: {2}to * by dn="cn=Manager,dc=ibmscale,dc=com" write by * read
    ```
    
    Replace the {SSHA} value with the password hash that you generated in the previous step.
    
    
9.	Apply the changes:

    ```
    Code: 
    ldapmodify -Y EXTERNAL -H ldapi:/// -f chdomain.ldif
    ```

10.	Create a file that is named `basedomain.ldif` and add the following lines to it:

    ```
    Code: 
    dn: dc=ibmscale,dc=com
    objectClass: top
    objectClass: dcObject
    objectclass: organization
    o: WES Migration
    dc: ibmscale
    dn: cn=Manager,dc=ibmscale,dc=com
    bjectClass: organizationalRole
    cn: Manager
    description: Directory Manager
    dn: ou=People,dc=ibmscale,dc=com
    objectClass: organizationalUnit
    ou: People
    dn: ou=Group,dc=ibmscale,dc=com
    objectClass: organizationalUnit
    ou: Group
    ```

11.	Apply the `baseddomain.ldif`` changes:

    ```
    Code: 
    ldapadd -x -D cn=Manager,dc= ibmscale,dc=com -W -f basedomain.ldif
    ``````
12.	Create a file that is named ldapuser.ldif using vi editor and add these lines to it and replace to required own domain name for "dc=***,dc=***" section.

    ```
    dn: uid=Scaleusr01,ou=People,dc=ibmscale,dc=com
    objectClass: inetOrgPerson
    objectClass: posixAccount
    objectClass: shadowAccount
    cn: Scaleusr01
    sn: Linux
    userPassword: {SSHA}+A6gC87JU5ugW6qthWL2HGYzsQIdN1EN
    loginShell: /bin/bash
    uidNumber: 1003
    gidNumber: 1003
    homeDirectory: /home/Scaleusr01
    dn: cn=Scaleusr01,ou=Group,dc=ibmscale,dc=com
    objectClass: posixGroup
    cn: Scaleusr01
    gidNumber: 1003
    memberUid: Scaleusr01
    ```

    Replace the {SSHA} value with the password hash that you generated earlier.

13.	Apply the changes:

    ```
    Code: 
    ldapadd -x -D cn=Manager,dc= ibmscale,dc=com -W -f ldapuser.ldif
    ```

14.	Verify whether users are created as mentioned in the preceding steps:

    ```
    ldapsearch -x -LLL -b "ou=People,dc=ibmscale,dc=com" "(objectClass=posixAccount)" uid cn
    ```

    The OpenLDAP server is now configured and ready to use. You can add more users and groups by creating additional LDIF files and by using the ldapadd command to import them into the directory.

## Step 2 -  Creating a User group in the OLDAP directory for users accessing the Scale cluster
{: #creating-user-group-oldap-dir}

Create a user group in the OLDAP directory, which consist of users who are supposed to get access to the Scale cluster.
You use the `ldapadd` command to add an LDIF entry for the group 
to the LDAP directory. 

1.  For example, to create groups that are called `developers` and `testers`, you create an LDIF file with these contents:

    ```
    # create an organizational unit for groups
    dn: ou=groups,dc=ibmscale,dc=com
    objectClass: organizationalUnit
    ou: groups

    # create a group called "ScaleAdmin"
    dn: cn=ScaleAdmin,ou=groups,dc=ibmscale,dc=com
    objectClass: top
    objectClass: posixGroup
    gidNumber: 1001
    cn: ScaleAdmin
    description: ScaleAdmin group

    # create a group called "Scaleconsumer"
    dn: cn=Scaleconsumer,ou=groups,dc=ibmscale,dc=com
    objectClass: top
    objectClass: posixGroup
    gidNumber: 1002
    cn: Scaleconsumer
    description: Scaleconsumer group
    ```

    In this example, the first entry creates an organizational unit called "groups". The next two entries create groups that are called "developers" and "testers" respectively.

    Each group entry specifies the objectClass "top" and "posixGroup" to define the group's schema. The gidNumber attribute specifies the group's unique identifier, while the cn attribute specifies the group's common name. Finally, the description attribute provides a brief description of the group.

2.  Save the preceding content in a file with a ".ldif" extension, such as "groups.ldif", 

3.  Use the `ldapadd` command to add these group entries to your LDAP directory. For example:

    ```
    ldapadd -x -D cn=Manager,dc=ibmscale,dc=com -W -f groups.ldif=

    [root@oldapserverlsfcl ~]# ldapsearch -x -D cn=Manager,dc=ibmscale,dc=com -W -b "ou=groups,dc=ibmscale,dc=com" "(objectClass=posixGroup)"

    Enter LDAP Password:

    # extended LDIF
    #
    # LDAPv3
    # base <ou=groups,dc=ibmscale,dc=com> with scope subtree
    # filter: (objectClass=posixGroup)
    # requesting: ALL
    #

    # ScaleAdmin, groups, ibmscale.com
    dn: cn=ScaleAdmin,ou=groups,dc=ibmscale,dc=com
    objectClass: top
    objectClass: posixGroup
    gidNumber: 1001
    cn: ScaleAdmin
    description: ScaleAdmin group
    memberUid: Scaleuser02
    memberUid: Scaleuser01

    # Scaleconsumer, groups, ibmscale.com
    dn: cn=Scaleconsumer,ou=groups,dc=ibmscale,dc=com
    objectClass: top
    objectClass: posixGroup
    gidNumber: 1002
    cn: Scaleconsumer
    description: Scaleconsumer group
    memberUid: Scaleuser03
    memberUid: Scaleuser04

    # search result
    earch: 2
    result: 0 Success
    ```

This output shows that there are two group entries, " ScaleAdmin" and " Scaleconsumer ", both under the ou=groups,dc=ibmscale,dc=com organizational unit.

Later, one can use the `ldapmodify` command to add exiting users to these groups.  In the above `ldapsearch` output, two users are seen as the member of each of the groups.

## Step 3 -  Configuration for CES integration and LDAP Authentication
{: #config-ces-integration}

Before configuring IBM Spectrum Scale with Cluster Export Services (CES) integration and enabling LDAP authentication for NFS services, it's essential to follow a series step. The following are the summarized high-level actions to guide you through this process:

### Before you begin
{: #beforeyoubegin-ces-integration}

Before proceeding, several assumptions are made:

*   Compute Subnet: The IP address range for compute resources is assumed to be 10.241.0.0/24.
*   Storage Subnet: The IP address range for storage resources is assumed to be 10.241.1.0/24.
*   Protocol Subnet: The IP address range for CES protocol nodes is assumed to be 10.241.2.0/24.
*   Supported Protocol Nodes: Only Virtual Server Instances (VSIs) are used as CES protocol nodes.

Additional Input includes:
•	total_protocol_node_count: The total number of CES protocol nodes to be used.
•	custom_file_shares: Custom file shares, such as /mnt/lsf:100, /mnt/data:100, etc., are expected.

### Procedure - Infrastructure Configuration 
{: #infrastructure-config}

1.  Infrastructure Configuration:

    a.  Spin up a Storage-Only Cluster
        Create a dedicated storage cluster to host IBM Spectrum Scale.
    b.  Create the Protocol Subnet
        Set up the network segment for CES protocol nodes.
    c.  Attach Secondary Interface to Protocol Nodes (Minimum 2)
        Add a secondary network interface to at least two Scale storage nodes for CES protocol communication.
    d.  Enable IP Spoofing
        Enable IP spoofing to allow CES protocol nodes to communicate by using private IPs.
    e.  Configure Security Group
        Create a security group named storage-sg for managing access control.
    f.  Reserve Private IPs in Protocol Subnet (Minimum 2)
        Reserve private IPs in the protocol subnet, matching the count of protocol nodes.

2.  DNS Configuration
    a.  Create DNS Zone (for example, scaleces.com)
        Need to set up a DNS zone to manage DNS records for CES.
    b.  Add VPC as Permitted Network
        Allow the VPC to access DNS records within the configured zone.
    c.  Add A and PTR Records
        Create A and PTR records to resolve CES node names to their corresponding IP addresses.

## Step 4 - CES Configuration (From Any Scale Node)
{: #ces-config-any-scale-node}

1.  Setting up Cluster Export Services Shared root file system:
    In this step we created a CesSharedRoot using the following command
		`mmchconfig cesSharedRoot=/gpfs/fs1`
    The CES shared root (cesSharedRoot) is needed for storing CES shared configuration data, for protocol recovery, and for other protocol-specific purposes. It is part of the cluster export configuration and is shared between the protocols. Every CES node requires access to the path configured as a shared root. The “mmchconfig” command is used to configure this directory as part of setting up a CES cluster as mentioned in the preceding example.	
2.  Verify CES Shared Root Configuration:
    Confirm the CES shared root configuration by using the mmlsconfig command.
3.  Configure the cluster Export Services on each of the Scale Nodes, which are going to handle protocol exports:	

    `mmchnode --ces-enable -N storage-scale-storage-5[,storage-scale-storage-6]`

    In this step, configuration of CES nodes must be done before you configure any protocols.  Nodes that participate in the handling of protocol exports must be configured as CES nodes.

4.  Check the status of CES nodes by using the mmces node list command.
5.  Assign CES IP addresses to protocol nodes by using the mmces address add command.

    Protocol services are made available through Cluster Export Services (CES) protocol service IP addresses. These addresses are separate from the IP addresses that are used internally by the cluster. To configure the CES protocol IP addresses, the following commands need to be run:

    `mmces address add --ces-node storage-scale-storage-5 --ces-ip 10.241.2.6 mmces address add --ces-node storage-scale-storage-6 --ces-ip 10.241.2.7`

    In this scenario the CES IP address is an alias IP address, this IP address can failover across to the other nodes in the event of failure of the owner node. 
    {: note}

6.  Verify the CES cluster status need to use the following command:
    `mmlscluster --ces command`
    
7.  Enable the NFS service for CES use the following command:
    `mmces service enable NFS`
8.  List the CES services in verbose mode use the following command:
    `mmces service list --verbose -a`
    Two directories (ces and ha) are automatically created in the fs1 file system; do not delete them.
    {: note}

9.  Route Addition at OS & RT Level:
    Explanation: Configure routes on all protocol nodes to reach the CES interface base IP addresses.
    Example: ip route add 10.241.0.0/24 through 10.241.2.1 dev eth1
    Add Routes to Reach CES Endpoints:
    Explanation: Set up routes to reach CES endpoints from base IPs using {{site.data.keyword.cloud_notm}} CLI commands.
    Example: ibmcloud is vpc-routing-table-route-create <vpc_id> <rt_id> --zone <zone> --destination <ces_ip> --next-hop <base_ip_of_ces_interface> --action deliver --name <ces-ip> -q

10. NFS Mount (From Any Scale Node)
    Configure the NFS mount:
    a.  Create an independent file set named lsf within fs1 for CES data.
         `mmcrfileset fs1 lsf --inode-space new``
    b.  Link the lsf fileset to the /gpfs/fs1/lsf directory.
         `mmlinkfileset fs1 lsf -J /gpfs/fs1/lsf``
    c.  Set Fileset-Level Quota (TODO) - Define quotas for the fileset as required.
    d.  Configure user authentication for file access. Create the authentication service: 
        'mmuserauth services create --type ldap --data-access-method file --servers 149.81.12.196 --base-dn dc=ibmscale,dc=com --user-name cn=manager,dc=ibmscale,dc=com --netbios-name ess'

        To verify if the LDAP authentication is setup correctly, following command needs to be run, below examples also displays the output:

        ```
        [root@jl-scale-encrypt-compute-3 vpcuser]# mmuserauth service list
        FILE access configuration : LDAP
        PARAMETERS               VALUES
        -------------------------------------------------
        ENABLE_SERVER_TLS        false
        ENABLE_KERBEROS          false
        SER_NAME                cn=manager,dc=ibmscale,dc=com
        SERVERS                  149.81.12.196
        NETBIOS_NAME             ess
        BASE_DN                  dc=ibmscale,dc=com
        SER_DN                  none
        GROUP_DN                 none
        NETGROUP_DN              none
        USER_OBJECTCLASS         posixAccount
        GROUP_OBJECTCLASS        posixGroup
        USER_NAME_ATTRIB         cn
        USER_ID_ATTRIB           uid
        KERBEROS_SERVER          none
        KERBEROS_REALM           none

        OBJECT access not configured
        PARAMETERS               VALUES
        The above command of mmuserauth service list is used to verify the LDAP authentication configuration. The output shows the configuration details of the LDAP authentication service for file access:
        •	“FILE access configuration : LDAP” Indicates that LDAP is configured for file access.
        •	ENABLE_SERVER_TLS: TLS encryption for server communication is disabled (false).
        •	ENABLE_KERBEROS: Kerberos authentication is disabled (false).
        •	USER_NAME: Specifies the LDAP user name used for authentication (cn=manager,dc=ibmscale,dc=com).
        •	SERVERS: Lists the LDAP server(s) used for authentication (149.81.12.196).
        •	NETBIOS_NAME: Specifies the NetBIOS name (ess).
        •	BASE_DN: Specifies the base distinguished name (DN) for LDAP queries (dc=ibmscale,dc=com).
        •	USER_DN, GROUP_DN, and NETGROUP_DN: These parameters are not configured (none).
        •	USER_OBJECTCLASS: Specifies the LDAP object class for user entries (posixAccount).
        •	GROUP_OBJECTCLASS: Specifies the LDAP object class for group entries (posixGroup).
        •	USER_NAME_ATTRIB: Specifies the LDAP attribute for user names (cn).
    	USER_ID_ATTRIB: Specifies the LDAP attribute for user IDs (uid).
        •	KERBEROS_SERVER and KERBEROS_REALM: Kerberos authentication is not configured (none).
        ```
11. Create NFS Export to allow specified IP ranges to access the fileset.
    `mmnfs export add /gpfs/fs1/lsf --client "10.241.0.0/24(Access_Type=RW,SQUASH=no_root_squash)"``
    `mmnfs export list``
    Scale Client (LSF) Node

12. Create NFS Mount Point to Prepare the Scale client (LSF) node for NFS mounting.
    
    `mkdir /mnt/lsf`

13.  Mount the NFS share from the CES server to the LSF node.
    `mount -t nfs4 -o sec=sys storage-scale-ces-1.cesscale.com:/gpfs/fs1/lsf /mnt/lsf`

## Step 5 - CES Failover Setup
{: #ces-failover-setup}

### Before you begin
{: #step5-before-begin}

•	Make sure a healthy Scale cluster with the ability to move CES IP addresses across protocol nodes.

1.  Installing the IBMCloud CLI tool for managing {{site.data.keyword.cloud_notm}} resources.
    `curl -fsSL https://clis.cloud.ibm.com/install/linux | sh`

2.  Install VPC Infrastructure Plugin for {{site.data.keyword.cloud_notm}} CLI.
    `ibmcloud plugin install is`

3.  Update mmcesExtendedIpMgmt script with the required {{site.data.keyword.cloud_notm}} environment variables for IP management.
    •	export IC_API_KEY=<ibmcloud_apikey>
    •	export IC_REGION=<region>
    •	export IC_ZONE=<zone>
    •	export IC_RG=<resource_group>
    •	export IC_VPC=<vpc_id>
    •	export IC_RT=<route_table_id>
    The script code is mentioned:

    Copy mmcesExtendedIpMgmt:
    Copy the mmcesExtendedIpMgmt script to /var/mmfs/etc/mmcesExtendedIpMgmt.
    
4.  Ensure that the script has executable permissions.

    `chmod +x /var/mmfs/etc/mmcesExtendedIpMgmt`
