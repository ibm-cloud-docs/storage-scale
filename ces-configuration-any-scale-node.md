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


# Configuring CES from any scale node
{: #ces-config-any-node}

You can integrate Scale with CES services and enable LDAP-based authentication for NFS services on CES Scale Nodes. The information includes assumptions, a step-by-step guide, and detailed explanations for each configuration. By using LDAP-based authentication with an external LDAP server, organizations can enhance security and centralize user management for NFS shares and configuration steps to enable LDAP-based authentication and provides instructions for setting up an OpenLDAP server on a Linux system.

## Procedure
{: #procedure-config-ces-any-node}

1.  Setting up Cluster Export Services Shared root file system.
    For example, create CesSharedRoot by using this command:

	`mmchconfig cesSharedRoot=/gpfsfs1`

    The CES shared root (cesSharedRoot) is needed to store CES shared configuration data, for protocol recovery, and for other protocol-specific purposes. It is part of the cluster export configuration and is shared between the protocols. Every CES node requires access to the path configured as a shared root. The `mmchconfig` command is used to configure this directory as part of setting up a CES cluster as mentioned in the example.	
2.  Confirm the CES shared root configuration:

     `mmlsconfig`

3.  Configure the cluster Export Services on each of the Scale Nodes, which are going to handle protocol exports. For example:	

    `mmchnode --ces-enable -N storage-scale-storage-5[,storage-scale-storage-6]`
    
    In this step, configuration of CES nodes must be done before you configure any protocols. Nodes that participate in the handling of protocol exports must be configured as CES nodes
4.  Check the status of CES nodes:

    `mces` 

5.  Assign CES IP addresses to protocol nodes by using the `mmces` address add command.

    Protocol services are made available through Cluster Export Services (CES) protocol service IP addresses. These addresses are separate from the IP addresses that are used internally by the cluster. Configure the CES protocol IP addresses by using the following command:

    `mmces address add --ces-node storage-scale-storage-5 --ces-ip 10.241.2.6 mmces address add --ces-node storage-scale-storage-6 --ces-ip 10.241.2.7`

    In this scenario, the CES IP address is an alias IP address. This IP address can failover across other nodes if failure of the owner node. 
    {: note}

6.  Verify the CES cluster status:

    `mmlscluster --ces command.`

7.  Enable the NFS service for CES: 

    `mmces service enable NFS 
    `
8.  List the CES services in verbose mode:
    `mmces service list --verbose -a`
    Two directories (ces and ha) are automatically created in the fs1 file system. Do not delete them.
    {: note}

9.  Add Routes to Reach CES Interface Base IP:
    a.  Configure routes on all protocol nodes to reach the CES interface base IP addresses.
        `ip route add 10.241.0.0/24 via 10.241.2.1 dev eth1`
    b.  Set up routes to reach CES endpoints from base IPs by using {{site.data.keyword.cloud_notm}} CLI commands.
        `ibmcloud is vpc-routing-table-route-create <vpc_id> <rt_id> --zone <zone> --destination <ces_ip> --next-hop <base_ip_of_ces_interface> --action deliver --name <ces-ip> -qa`

10. NFS Mount (From Any Scale Node)

    Configure the NFS mount:
    1.  Create an independent file set named lsf within fs1 for CES data.
        `mmcrfileset fs1 lsf --inode-space new`
    2.  Link the lsf file set to the /gpfs/fs1/lsf directory:
        `mmlinkfileset fs1 lsf -J /gpfs/fs1/lsf`
    3.  Define quotas for the file set as required.
    4.  Configure user authentication for file access:
         
        `mmuserauth service create --type ldap --data-access-method file --servers 149.81.12.196 --base-dn dc=ibmscale,dc=com --user-name cn=manager,dc=ibmscale,dc=com --netbios-name ess`

        To verify whether the LDAP authentication is setup correctly, use this command: 

        ```shell
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
11. Create an NFS export to allow specified IP ranges to access the file set:
    *  `mmnfs export add /gpfs/fs1/lsf --client "10.241.0.0/24(Access_Type=RW,SQUASH=no_root_squash)`
    *  `mmnfs export list`
   
12. Create an NFS mount point to prepare the Scale client (LSF) node for NFS mounting.
    
    `mkdir /mnt/lsf`

13.  Mount the NFS share from the CES server to the LSF node.
    `mount -t nfs4 -o sec=sys storage-scale-ces-1.cesscale.com:/gpfs/fs1/lsf /mnt/lsf`

