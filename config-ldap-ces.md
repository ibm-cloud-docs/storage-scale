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

# Configuring CES with LDAP deployment values
{: #config-ldap-ces}

The CES feature offers two authentication options, the default user-defined method and the use of directory services like AD, LDAP, or NIS.

The directory services enable access to files through the NFS protocol.

LDAP is an optional component for CES, allowing users to either use an existing LDAP server or set up a new LDAP node specifically for the CES cluster.

By setting up the `ldap_basedns` deployment value to the required domain name during the deployment, the LDAP feature is integrated along with the Scale CES.

## Before you begin
{: #before-you-begin}

Before you begin, review the following task:

Make sure to complete the steps for [Getting started with IBM Storage Scale](/docs/storage-scale?topic=storage-scale-getting-started-tutorial).

|CES Variable	|Description	|Example value |
|----------|----------|----------|
|`ldap_basedns`|Base DNS of LDAP Server. If none is given the LDAP feature is not enabled.|`ldapscale.com` |
|`ldap_server`	|IP of an existing LDAP server. If none is given a new ldap server is created	|| 
|`ldap_admin_password`	|Password that is used for performing administrative operations for LDAP. The password must contain at least 8 characters and at most 20 characters. For a strong password, at least three alphabetic characters are required, with at least one uppercase and one lowercase letter. Two numbers, and at least one special character from this set (~@_+:). Make sure that the password doesn't include the username.	|`xxxxxx`  |
|`ldap_user_name`	|Custom LDAP User for performing cluster operations. Note: Username must be at least 4 character, (any combination of lowercase and uppercase letters).	|`scaleuser`|
|`ldap_user_password`	|LDAP User Password that is used for performing operations on the cluster. The password must contain at least 8 characters and at most 20 characters. For a strong password, at least three alphabetic characters are required, with at least one uppercase and one lowercase letter. Two numbers, and at least one special character from this set (~@_+:). Make sure that the password doesn't include the username.|`xxxxxx`|
{: caption='LDAP variables'}

## Verifying authentication
{: #verify}

Use the `mmuserauth` command to view the details on the type of authentication used for CES:

```
mmuserauth service check
```

The command that is mentioned indicates that the authentication process is being carried out through LDAP.

To know more about CES authentication click [CES User Authentication](/docs/en/storage-scale/5.1.9?topic=protocols-authentication-considerations).

