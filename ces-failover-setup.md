---

copyright:
  years: 2023
lastupdated: "2023-11-14"

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


# Setting up CES Failover 
{: #ces-failover-setup}

## Before you begin
{: #before-you-begin-ces-failover}

*   Ensure that you have a healthy Scale cluster with the ability to move CES IP addresses across protocol nodes.

## Procedure
{: #procedure-ces-failover}


1.  Install the IBMCloud CLI tool for managing {{site.data.keyword.cloud_notm}} resources.

    ```
    curl -fsSL https://clis.cloud.ibm.com/install/linux | sh
    ```

2.  Install the VPC Infrastructure plug-in for {{site.data.keyword.cloud_notm}} CLI.

    ```ibmcloud plugin install is
    ```

3.  Update the mmcesExtendedIpMgmt script with the required {{site.data.keyword.cloud_notm}} environment variables for IP management.
    •	export IC_API_KEY=<ibmcloud_apikey>
    •	export IC_REGION=<region>
    •	export IC_ZONE=<zone>
    •	export IC_RG=<resource_group>
    •	export IC_VPC=<vpc_id>
    •	export IC_RT=<route_table_id>
    
    Example script code:

    ```
    1. #!/usr/lpp/mmfs/bin/mmksh
    2. # sample file for standard gpfs cluster.
    3. # If you move this file to /var/mmfs/etc and rename it to mmcesExtendedIpMgmt the commands to enable
    4. # and disable IP assignement are working identically as before but are executed from this script
    5. # instead from internal ces code.
    6. # On cloud systems you may need some pre- and post processing when you add or remove an ip from an
    7. # adapter.
    8. # Apply your changes inside the functions only. You can add new functions called by the existing
    9. # functions if desired.
    10.  
    11. typeset -i rc=1
    12. typeset funcName=$1
    13. shift
    14. typeset cmd="$*"
    15.  
    16. # enable ip by: /sbin/ifconfig <interface> <ip> netmask <interfaceMask>
    17. function enableIP
    18. {
    19.   typeset cmd="$*"
    20.   # in case the following information is needed, remove the #
    21.   # typeset interface=$2
    22.   # typeset ip=$3
    ```

4.  Copy the mmcesExtendedIpMgmt script to /var/mmfs/etc/mmcesExtendedIpMgmt.

5.  Ensure that the script has executable permissions.

    ```
    chmod +x /var/mmfs/etc/mmcesExtendedIpMgmt
    ```

