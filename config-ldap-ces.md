---

copyright:
  years: 2023, 2024
lastupdated: "2024-04-26"

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

# Enabling OpenLDAP service
{: #enable-openldap}

This document gives you the brief overview about the Open Lightweight Directory Access Protocol (OpenLDAP) and the integration of OpenLDAP with {{site.data.keyword.scale_full_notm}} cluster.

## About OpenLDAP with IBM Storage Scale
{: #about-openldap}

OpenLDAP is an open source implementation of the Lightweight Directory Access Protocol (LDAP) that provides centralized authentication and directory services.

Integrating OpenLDAP with your {{site.data.keyword.scale_full_notm}} cluster enables centralized user management, improved security, and simplified user authentication. The integration also allows you to use existing authentication credentials, reducing the need to remember multiple login credentials. Overall, the architecture provides a robust and efficient solution for user authentication and directory management in distributed computing environments.

OpenLDAP server can be installed and configured on a Linux&reg; system; for example, an Ubuntu 22.04 as the host is supported.
{: shortdesc}

## Integrating an existing OpenLDAP server with your IBM Storage Scale cluster
{: #integrating-openldap}

You can enable OpenLDAP with your {{site.data.keyword.scale_full_notm}} cluster [during deployment](/docs/storage-scale?topic=storage-scale-deployment-values) by setting the `enable_ldap`,`ldap_basedns`, `ldap_server`, `ldap_admin_password`, `ldap_user_name`, and `ldap_user_password` deployment input values. If you do not have an existing LDAP server, the deployment process creates one for you and connects it to {{site.data.keyword.scale_full_notm}} cluster. If you have an existing LDAP server, you can provide LDAP information during the {{site.data.keyword.scale_full_notm}} cluster deployment.
{: shortdesc}

If you have an existing OpenLDAP server, you can use that with your {{site.data.keyword.scale_full_notm}} cluster. Before you deploy the {{site.data.keyword.scale_full_notm}} cluster with the LDAP input values, complete the following LDAP requirements:
1. OpenLDAP version 2.4 or later is installed and configured.
2. The OpenLDAP server can communicate with the {{site.data.keyword.scale_full_notm}} cluster nodes over the network. Configure the network settings on both the OpenLDAP server and the {{site.data.keyword.scale_full_notm}} cluster nodes.
3. The OpenLDAP server and the {{site.data.keyword.scale_full_notm}} cluster nodes can communicate over port 389.

Also, always allow access to the CIDR ranges for the VPC that the {{site.data.keyword.scale_full_notm}} cluster deployment creates. Make sure that the security groups for the existing LDAP server are allowlisted with the VPC CIDR range of newly created VPC. This way, the new VPC can connect to the existing your existing OpenLDAP server and that all management and login nodes can access your LDAP server.

If the security groups of the LDAP server are updated with the VPC CIDR ranges, you see a message similar to:

```text
null_resource.validate_ldap_server_connection[0] (remote-exec): The connection to the existing LDAP server 10.241.0.5 was successfully established.
```
{: codeblock}


However, if the connection to the existing LDAP server is not established, you see a message similar to:

```text
╷
│ Error: remote-exec provisioner error
│ 
│   with null_resource.validate_ldap_server_connection[0],
│   on main.tf line 355, in resource "null_resource" "validate_ldap_server_connection":
│  355:   provisioner "remote-exec" {
│ 
│ error executing "/tmp/terraform_888134906.sh": Process exited with status 1
```
{: codeblock}

## Checking OpenLDAP status
{: #checking-openldap-status}

After {{site.data.keyword.scale_full_notm}} cluster deployment, Schematic logs show essential information in the output section. From here, you can check your LDAP server status:

1. Connect to your OpenLDAP server through SSH by using the `ssh_to_ldap_node` command from the {{site.data.keyword.bpshort}} log output.

    For example:

    ```text
    ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -o ServerAliveInterval=5 -o ServerAliveCountMax=1 -J vpcuser@<floatingg_IP_address> ubuntu@<LDAP_server_IP>
    ```
    {: codeblock}

    where `<floating_IP_address>` is the floating IP address for the bastion node and `<LDAP_server_IP>` is the IP address for the OpenLDAP node.

2. Verify the LDAP service status:

    ```text
    systemctl status slapd
    ```
    {: codeblock}

3. Verify the LDAP groups and users created:

    ```text
    ldapsearch -Q -LLL -Y EXTERNAL -H ldapi:///
    ```
    {: codeblock}

## Creating an LDAP user
{: #create-ldap-user}

If you do not have an existing LDAP server, the deployment process created one for you and connected it to {{site.data.keyword.scale_full_notm}} cluster during deployment. Create a LDAP user for your {{site.data.keyword.scale_full_notm}} cluster. Use it to run Storage Scale commands and submit the jobs with existing authentication credentials, reducing the need to remember multiple login credentials.

If you have an existing LDAP server with the LDAP information that is provided during the {{site.data.keyword.scale_full_notm}} cluster deployment, then you need not create a new LDAP user.
{: shortdesc}

### Before you begin
{: #prequisites}

Ensure that you have the following LDAP information:

* `BASE_DN`: The base domain name for your LDAP server (for example, **ldap.com**).
* `LDAP_ADMIN_PASSWORD`: The password for the LDAP administrator.
* `OU_NAME`: The organizational unit where users are located (for example, **People**).
* `EXISTING_LDAP_USER`: The username of an existing LDAP user to be verified.
* `LDAP_USER`: The username for the new LDAP user.
* `NEW_LDAP_USER_PASSWORD`: The password for the new LDAP user.

You can export this LDAP information from the LDAP server; for example:

```console
export BASE_DN="ldap.com"
export OU_NAME="People"
export LDAP_ADMIN_PASSWORD="Admin@123"
export LDAP_USER="testuser01"
export UNIQUE_USER_ID="10089"
export NEW_LDAP_USER_PASSWORD="Test@1234"
```
{: pre}

### Creating the user
{: #create-user}

1. Connect to your OpenLDAP server through SSH by using the `ssh_to_ldap_node` command from the {{site.data.keyword.bpshort}} log output.

    For example:

    ```text
    ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -o ServerAliveInterval=5 -o ServerAliveCountMax=1 -J vpcuser@<floating_IP_address> ubuntu@<LDAP_server_IP>
    ```
    {: codeblock}

    where `<floating_IP_address>` is the floating IP address for the bastion node and `<LDAP_server_IP>` is the IP address for the OpenLDAP node.

2.  See if the LDAP user is an existing one:
    ```text
    ldapsearch -x -D "cn=admin,dc=${BASE_DN%%.*},dc=${BASE_DN#*.}" -w "${LDAP_ADMIN_PASSWORD}" -b "dc=${BASE_DN%%.*},dc=${BASE_DN#*.}" "(uid=${EXISTING_LDAP_USER})"
    ```
    {: codeblock}

3.  Generate a hashed password for the user:
    ```text
    slappasswd -s "${NEW_LDAP_USER_PASSWORD}"
    ```
    {: codeblock}

    Copy this generated hash for the next step.

4.  Create a user configuration file called `${LDAP_USER}.ldif`, with the necessary attributes for the new LDAP user. Replace `<Unique User ID>` with a unique user ID and `<Hash password for the new user>`with the hash obtained from the previous step.

    Example `${LDAP_USER}.ldif` file:

    ```console
    echo "dn: uid=${LDAP_USER},ou=${OU_NAME},dc=${BASE_DN%%.*},dc=${BASE_DN#*.}
    objectClass: inetOrgPerson
    objectClass: posixAccount
    objectClass: shadowAccount
    uid: ${LDAP_USER}
    sn: ${LDAP_USER}
    givenName: ${LDAP_USER}
    cn: ${LDAP_USER}
    displayName: ${LDAP_USER}
    uidNumber: <Unique User ID>
    gidNumber: 5000
    userPassword: <Hash password for the new user>
    gecos: ${LDAP_USER}
    loginShell: /bin/bash
    homeDirectory: /home/${LDAP_USER}" > ${LDAP_USER}.ldif
    ```
    {: pre}

5.  Create the LDAP user, by using the details from the `${LDAP_USER}.ldif` file:

    ```text
    ldapadd -x -D "cn=admin,dc=${BASE_DN%%.*},dc=${BASE_DN#*.}" -w "${LDAP_ADMIN_PASSWORD}" -f "${LDAP_USER}.ldif"
    ```
    {: codeblock}

To accommodate many LDAP users (where each user has an individual IP address), update the security group for the {{site.data.keyword.scale_full_notm}} cluster systematically. Moreover, instead of manually adding each user's IP address, a more scalable approach involves allowing CIDR ranges for the respective users. This way, as new LDAP users are created, their entire IP range is authorized, simplifying the management of security configurations. Consider implementing automation to streamline the process and ensure the security group remains up-to-date with the dynamic nature of LDAP user IPs. Regular reviews and documentation maintenance are essential to adapt to changes in user access and uphold security protocols effectively.
{: note}

## Configuring CES with LDAP deployment values
{: #config-ldap-ces}

The CES feature offers two authentication options, the default user-defined method and the use of directory services like AD, LDAP, or NIS.

The directory services enable access to files through the NFS protocol.

LDAP is an optional component for CES, allowing users to either use an existing LDAP server or set up a new LDAP node specifically for the CES cluster.

By setting up the `ldap_basedns` deployment value to the required domain name during the deployment, the LDAP feature is integrated along with the Scale CES.

### Before you begin
{: #before-you-begin}

Before you begin, review the following task:

Make sure to complete the steps for [Getting started with IBM Storage Scale](/docs/storage-scale?topic=storage-scale-getting-started-tutorial).

|CES Variable	|Description	|Example value |
|----------|----------|----------|
|`ldap_basedns`|Base DNS of LDAP Server. If **none** is given then LDAP feature is not enabled.|`ldapscale.com` |
|`ldap_server`	|IP of an existing LDAP server. If **none** is given, then a new ldap server is created.	|| 
|`ldap_admin_password`	|Password that is used for performing administrative operations for LDAP. The password must contain at least 8 characters and at most 20 characters. For a strong password, at least three alphabetic characters are required, with at least one uppercase and one lowercase letter. Two numbers, and at least one special character from this set (~@_+:). Make sure that the password doesn't include the username.	|`xxxxxx`  |
|`ldap_user_name`	|Custom LDAP user for performing cluster operations. Note: Username must be at least 4 character, (any combination of lowercase and uppercase letters).	|`scaleuser`|
|`ldap_user_password`	|LDAP user password that is used for performing operations on the cluster. The password must contain at least 8 characters and at most 20 characters. For a strong password, at least three alphabetic characters are required, with at least one uppercase and one lowercase letter. Two numbers, and at least one special character from this set (~@_+:). Make sure that the password doesn't include the username.|`xxxxxx`|
|`enable_ldap`|Set this option to true to enable LDAP for {{site.data.keyword.cloud_notm}} HPC, with the default value set to false.|true|
{: caption='CES variables'}

### Verifying authentication
{: #verify}

Use the `mmuserauth` command to view the details on the type of authentication used for CES:

```
mmuserauth service check
```

The command that is mentioned indicates that the authentication process is being carried out through LDAP.

To know more about CES authentication click [CES User Authentication](/docs/storage-scale?topic=storage-scale-config-ces-integration-ldap-authentication&interface=cli#verify-ces).
