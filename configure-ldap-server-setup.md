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


# Configuration for LDAP Server setup
{: #configure-ldap-server}

Make sure that the LDAP server is properly configured with the required schemas that are installed to handle authentication and ID-mapping requests. If SMB data access is required, the LDAP schema must be extended to store additional attributes such as SID and password hash.

## Configuring an OpenLDAP Server
{: #configure-openldap-server}

OpenLDAP is an open source implementation of the Lightweight Directory Access Protocol (LDAP) that can be used to store and manage information about users, groups, and other objects in a network. This document provides step-by-step instructions for configuring an OpenLDAP server on a Linux system.

## Before you begin
{: #beforeyoubegin-openldap}

You should have access to a Linux system with root privileges. You should also have a basic understanding of the command-line interface and how to use a text editor.

## Procedure
{: #proc-config-openldap}


Use these steps to configure an OpenLDAP server:

1.	Install the OpenLDAP server and client packages

   ```shell
    Code:
    yum -y install openldap-servers openldap-clients 

   ```
2.	Copy the DB_CONFIG.example file to the /var/lib/ldap directory and change its ownership to the ldap user:

    ```shell
    Code:
    cp /usr/share/openldap-servers/DB_CONFIG.example /var/lib/ldap/DB_CONFIG chown ldap. /var/lib/ldap/DB_CONFIG 
    ```

3.	Start the slapd service and enable it to start automatically at boot time by running the following commands:

    ```shell
    Code:
    systemctl start slapd systemctl enable slapd 
    ```

4.	Generate an admin password by running the slappasswd command. You are prompted to enter a password. For example:

    ```shell
    Code:
    slappasswd 
    ```
    
    You see an output that looks something like this:

    ```shell
    Code:
    {SSHA}FUMV8TZ9lZQxABxCBE5UZ+oU/dlwf/d4 
    ```
    The password hash that is generated (in this case,   {SSHA}         FUMV8TZ9lZQxABxCBE5UZ+oU/dlwf/d4) as you need it later.
    {: note} 

5.	Create a file that is named `chrootpw.ldif` and add the following lines to it:

    ```shell
    Code:
    dn: olcDatabase={0}config,cn=config
    changetype: modify
    add: olcRootPW
    olcRootPW: {SSHA}FUMV8TZ9lZQxABxCBE5UZ+oU/dlwf/d4]
    ```

    Replace the `olcRootPW`` value with the password hash that you generated in the step 4.

6.	Import the basic schema by running the following commands:

    ```shell
    Code:
    ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif 
    ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif 
    ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif 
    ```

7.	Generate a manager password by running the slappasswd command again.  For example:

    ```shell
    Code:
    slappasswd 
    ```

    You see an output that looks something like this:


    ```shell
    Code:
    {SSHA}TVW9z6WLIBC3EXtFHFWnb2EVlK7EZQ3b 
    ``````
    
    Make note of the password hash that is generated (in this case, {SSHA}TVW9z6WLIBC3EXtFHFWnb2EVlK7EZQ3b) as you need it in the next step.

8.	Add the manager password and enable the manager account by creating a file that is named `chdomain.ldif` and adding these lines to it:

    ```shell
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

    ```shell
    Code: 
    ldapmodify -Y EXTERNAL -H ldapi:/// -f chdomain.ldif
    ```

10.	Create a file that is named `basedomain.ldif` and add the following lines to it:

    ```shell
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

    ```shell
    Code: 
    ldapadd -x -D cn=Manager,dc= ibmscale,dc=com -W -f basedomain.ldif
    ```
12.	Create a file named `ldapuser.ldif` using vi editor and add these lines to it and replace to required own domain name for "dc=***,dc=***" section.

    ```shell
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

    ```shell
    Code: 
    ldapadd -x -D cn=Manager,dc= ibmscale,dc=com -W -f ldapuser.ldif
    ```

14.	Verify whether users have been created as mentioned in the above steps: 

    ```shell
    ldapsearch -x -LLL -b "ou=People,dc=ibmscale,dc=com" "(objectClass=posixAccount)" uid cn
    ```

    The OpenLDAP server is now configured and ready to use. You can add more users and groups by creating additional LDIF files and by using the ldapadd command to import them into the directory.

