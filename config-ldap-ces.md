---

copyright:
  years: 2023, 2024
lastupdated: "2024-11-06"

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

OpenLDAP server can be installed and configured on a Linux&reg; system; for example, an Ubuntu 22.04 is supported as the host.
{: shortdesc}

## Before you begin
{: #beforeyoubegin-openladap}

Before you begin, review the following information:

1. Make sure to complete the steps for [Getting started with IBM Storage Scale](/docs/storage-scale?topic=storage-scale-getting-started-tutorial).
2. Learn more about [OpenLDAP](https://www.openldap.org/doc/admin26/) configuration.

## Integrating the OpenLDAP server with your IBM Storage Scale cluster
{: #integrating-openldap}

You can enable OpenLDAP with your {{site.data.keyword.scale_full_notm}} cluster [during deployment](/docs/storage-scale?topic=storage-scale-deployment-values) by setting the `enable_ldap`,`ldap_basedns`, `ldap_admin_password`, `ldap_user_name`, `ldap_user_password`, and `ldap_instance_key_pair` deployment input values. If you do not have an existing LDAP server, the deployment process creates one for you and connects it to the {{site.data.keyword.scale_full_notm}} cluster.

|LDAP Variable	|Description	|Example value |
|----------|----------|----------|
|`enable_ldap`|Set this option to true to enable LDAP for IBM Cloud HPC, with the default value set to false.|true |
|`ldap_basedns`	|The dns domain name is used for configuring the LDAP server. If an LDAP server is already in existence, ensure to provide the associated DNS domain name.|`ldapscale.com`| 
|`ldap_admin_password`	|The LDAP administrative password should be 8 to 20 characters long, with a mix of at least three alphabetic characters, including one uppercase and one lowercase letter. It must also include two numerical digits and at least one special character from (~@_+:) are required. It is important to avoid including the username in the password for enhanced security.	|`xxxxxx`|
|`ldap_user_name`	|Custom LDAP User for performing cluster operations. Note: Username should be between 4 to 32 characters, (any combination of lowercase and uppercase letters).[This value is ignored for an existing LDAP server]	|`scaleuser`|
|`ldap_user_password`	|The LDAP user password should be 8 to 20 characters long, with a mix of at least three alphabetic characters, including one uppercase and one lowercase letter. It must also include two numerical digits and at least one special character from (~@_+:) are required.It is important to avoid including the username in the password for enhanced security.[This value is ignored for an existing LDAP server].|`xxxxxx`|
|`ldap_instance_key_pair`|Set this option to true to enable LDAP for IBM Cloud HPC, with the default value set to false.|your-ssh-key|
{: caption='LDAP variables'}

## Integrating an existing OpenLDAP server with your IBM Storage Scale cluster
{: #integrating-existing-openldap}

You can enable OpenLDAP with your {{site.data.keyword.scale_full_notm}} cluster [during deployment](/docs/storage-scale?topic=storage-scale-deployment-values) by setting the `enable_ldap`, `ldap_basedns`, `ldap_server`, and `ldap_server_cert` deployment input values. If you do not have an existing LDAP server and certificate, the deployment process creates one for you and connects it to the IBM Cloud HPC cluster.
{: shortdesc}

If you already have an existing LDAP server with a certificate, provide all the necessary LDAP information during the {{site.data.keyword.scale_full_notm}} cluster deployment. If your existing LDAP server does not have a certificate, follow the steps to create and configure.

Before you deploy the IBM Storage Scale cluster with the LDAP input values, complete the following LDAP requirements:

1. OpenLDAP version 2.4 or later is installed and configured.
2. The OpenLDAP server can communicate with the {{site.data.keyword.scale_full_notm}} cluster nodes over the network. Configure the network settings on both the OpenLDAP server and the {{site.data.keyword.scale_full_notm}} cluster nodes.
3. The OpenLDAP server and the {{site.data.keyword.scale_full_notm}} cluster nodes can communicate over port 389.
4. Create and configure an LDAP certificate if not present.

|LDAP Variable	|Description	|Example value |
|----------|----------|----------|
|`enable_ldap`|Set this option to true to enable LDAP for IBM Cloud HPC, with the default value set to false.|true |
|`ldap_basedns`	|The dns domain name is used for configuring the LDAP server. If an LDAP server is already in existence, ensure to provide the associated DNS domain name.|`ldapscale.com`| 
|`ldap_server`	|Provide the IP address for the existing LDAP server. If no address is given, a new LDAP server will be created.|null|
|`ldap_server_cert`|Provide the existing LDAP server certificate. This value is required if the `ldap_server` variable is not set to null. If the certificate is not provided or is invalid, the LDAP configuration may fail. For more information on how to create or obtain the certificate, refer [Enabling OpenLDAP service](https://cloud.ibm.com/docs/storage-scale?topic=storage-scale-enable-openldap).|null|
{: caption='LDAP variables'}

Also, always allow access to the CIDR ranges for the VPC that the {{site.data.keyword.scale_full_notm}} cluster deployment creates. Make sure that the security groups for the existing LDAP server are allowlisted with the VPC CIDR range of newly created VPC. This way, the new VPC can connect to the existing your existing OpenLDAP server and that all management and login nodes can access your LDAP server.

If the security groups of the LDAP server are updated with the VPC CIDR ranges, you see a message similar to:

```text
null_resource.validate_ldap_server_connection[0] (remote-exec): The connection to the existing LDAP server 10.241.0.5 was successfully established.
```
{: codeblock}


However, if the connection to the existing LDAP server is not established, you see a message similar to:

```text
│ Error: remote-exec provisioner error
│ with null_resource.validate_ldap_server_connection[0], on main.tf line 355, in resource "null_resource" "validate_ldap_server_connection":
│ 355:   provisioner "remote-exec" {
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

By setting up the `enable_ldap`, `ldap_admin_password`, `ldap_user_name`, `ldap_user_password`, `ldap_instance_key_pair`, and `ldap_basedns` deployment value to the required domain name during the deployment, the LDAP feature is integrated along with the Scale CES.

### Before you begin
{: #before-you-begin}

Before you begin, make sure to complete the steps for [Getting started with IBM Storage Scale](/docs/storage-scale?topic=storage-scale-getting-started-tutorial).

|CES Variable	|Description	|Example value |
|----------|----------|----------|
|`ldap_basedns`|Base DNS of LDAP Server. If **none** is given then LDAP feature is not enabled.|`ldapscale.com` |
|`ldap_server`	|IP of an existing LDAP server. If **none** is given, then a new ldap server is created.	|null| 
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

## Creating and Configuring an LDAP certificate with your LDAP server
{: #create-configure-ldap-certificate}

If you have an existing LDAP server configured without a certificate, follow these steps to create a certificate and configure it with the LDAP server.

1. Configure SSH key into your existing LDAP server.

2. Install the required software
   `apt install gnutls-bin ssl-cert -y`

3. Generate the SSL certificate and configure with the OpenLDAP server.
    ```text
    certtool --generate-privkey --sec-param High --outfile 
    /etc/ssl/private/ldap_cakey.pem
    ```
    {: codeblock}

4. Create a CA template file.
    ```text
    cat <<EOF > /etc/ssl/ca.info
    cn = IBM Research
    ca
    cert_signing_key
    expiration_days = 3650
    EOF
    ```
    {: codeblock}

5. Generate a self-signed CA certificate.
    ```text
    certtool --generate-self-signed \
    --load-privkey /etc/ssl/private/ldap_cakey.pem \
    --template /etc/ssl/ca.info \
    --outfile /usr/local/share/ca-certificates/ldap_cacert.pem
    ```
    {: codeblock}

6. Update the CA certificates and copy the generated CA certificate to `/etc/ssl/certs/` path.
    ```text
    update-ca-certificates
    cp /usr/local/share/ca-certificates/ldap_cacert.pem /etc/ssl/certs/
    ```
    {: codeblock}

7. Generate a private key for the LDAP server.
    ```text
    certtool --generate-privkey --sec-param High --outfile 
    /etc/ssl/private/ldapserver_slapd_key.pem
    ```
    {: codeblock}

8. Create an LDAP server certificate template.
    ```
    cat <<EOF > /etc/ssl/ldapserver.info
    organization = IBM Research
    cn = localhost
    tls_www_server
    encryption_key
    signing_key
    expiration_days = 3650
    EOF
    ```
    {: codeblock}

9. Generate a certificate for the LDAP server signed by the CA.
    ```
    certtool --generate-certificate \
    --load-privkey /etc/ssl/private/ldapserver_slapd_key.pem \
    --load-ca-certificate /etc/ssl/certs/ldap_cacert.pem \
    --load-ca-privkey /etc/ssl/private/ldap_cakey.pem \
    --template /etc/ssl/ldapserver.info \
    --outfile /etc/ssl/certs/ldapserver_slapd_cert.pem
    ```
    {: codeblock}

10. Set proper permissions for the LDAP server private key.
    ```
    chgrp openldap /etc/ssl/private/ldapserver_slapd_key.pem
    chmod 0640 /etc/ssl/private/ldapserver_slapd_key.pem
    gpasswd -a openldap ssl-cert
    ```
    {: codeblock}

11. Restart `slapd` service to apply the changes
    `systemctl restart slapd.service`

12. Create an LDIF file for configuring TLS in LDAP server.
    ```
    cat <<EOF > /etc/ssl/certinfo.ldif
    dn: cn=config
    add: olcTLSCACertificateFile
    olcTLSCACertificateFile: /etc/ssl/certs/ldap_cacert.pem
    -
    add: olcTLSCertificateFile
    olcTLSCertificateFile: /etc/ssl/certs/ldapserver_slapd_cert.pem
    -
    add: olcTLSCertificateKeyFile
    olcTLSCertificateKeyFile: /etc/ssl/private/ldapserver_slapd_key.pem
    EOF
    ```
    {: codeblock}

13. Apply TLS configuration using `ldapmodify` command.
    ```
    ldapmodify -Y EXTERNAL -H ldapi:/// -f /etc/ssl/certinfo.ldif
    ```

14. Configure the `slapd` service to listen on both ldap:// and ldaps://"
    ```
    sed -i 's\SLAPD_SERVICES="ldap:/// ldapi:///"\SLAPD_SERVICES="ldap:/// ldapi:/// ldaps:///"\g' /etc/default/slapd
    ```

15. Update `/etc/ldap/ldap.conf` file.
    ```
    cat <<EOF >> /etc/ldap/ldap.conf
    TLS_CACERT /etc/ssl/certs/ldap_cacert.pem
    TLS_REQCERT allow
    EOF
    ```
    {: codeblock}

16. Restart `slapd` service to apply the changes.
    `systemctl restart slapd.service`

17. Copy the LDAP certificate content from "/etc/ssl/certs/ldap_cacert.pem" path as a single line and provide as input to the "ldap_server_cert" variable.

    ` awk 'NF {sub(/\r/, ""); printf "%s\\n",$0;}' /etc/ssl/certs/ldap_cacert.pem`
