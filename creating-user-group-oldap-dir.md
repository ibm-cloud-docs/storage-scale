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

# Creating a user group in the OLDAP directory for accessing the Scale cluster
{: #creating-user-group-oldap-dir}

Create a user group in the OLDAP directory, which consist of users who are supposed to get access to the Scale cluster.
You use the `ldapadd` command to add an LDIF entry for the group 
to the LDAP directory. 

## Procedure
{: #procedure-create-user-group}

1.  Create your groups.  For example, to create groups that are called `developers` and `testers`, you create an LDIF file with these contents:

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

2.  Save the file contents in a file with a ".ldif" extension, such as "groups.ldif", 

3.  Use the `ldapadd` command to add these group entries to your LDAP directory. For example:

    ```
    ldapadd -x -D cn=Manager,dc=ibmscale,dc=com -W -f groups.ldif=

    [root@oldapserverlsfcl ~]# ``ldapsearch -x -D cn=Manager,dc=ibmscale,dc=com -W -b "ou=groups,dc=ibmscale,dc=com" "(objectClass=posixGroup)"``

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

Later, one can use he `ldapmodify` command to add exiting users to these groups.  In the above `ldapsearch` output, two users are seen as the member of each of the groups.

