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

# Creating a User group and users in Active Directory for accessing the Symphony cluster
{: #create-user-group-ad-symphony}

Create a user group named "Symphony-group" and a user named "Symphonyuser01" in the Active Directory domain "pocdomain.local" using the Active Directory Users and Computers (ADUC) management tool on a Windows Server:

1.  Open Active Directory Users and Computers:
    a.  Log in to the Windows Server with administrative privileges.
    b.  Click the Start button and search for "Active Directory Users and Computers."
    c.  Start the "Active Directory Users and Computers" management console.
2.  Create a User Group "Symphony-group":
    a.  In the ADUC console, navigate to the Organizational Unit (OU) within the "pocdomain.local" domain where you want to create the user group.
    b.  Right-click on the OU and select "New" and then "Group."
    c.  In the "New Object - Group" dialog box, enter "Symphony-group" as the Group name.
    4.  Choose the Group scope (for example, Global) and Group type (for example, Security).
    5.  Click "OK" to create the "Symphony-group" user group.
3.  Create a User "Symphonyuser01":
    a.  In the ADUC console, navigate to the same or a different Organizational Unit (OU) within the "pocdomain.local" domain where you want to create the user "Symphonyuser01."
    b.  Right-click on the OU and select "New" and then "User."
    c.  In the "New Object - User" dialog box, enter the required information for the new user "symphonyuser01":
    	*  Full name: Enter the user's full name (for example, LSF User 01).
    	*  User logon name: Enter the user's logon name (for example, Symphonyser01).
    	*  User Principal Name (UPN): The UPN is automatically generated based on the user logon name and the domain name (for example, Symphonyuser01@pocdomain.local).
    	*  Password: Set a secure password for the user account and choose whether the user must change the password on the first logon.
    	*  User cannot change password: Check this option if you want to prevent the user from changing their password.
    	*  Password never expires: Check this option if you want the user's password to never expire.
    	*  Account is disabled: By default, the account is enabled. If you want to create the user account in a disabled state, uncheck this option.
    *  Click "Next" to continue through any additional wizard steps.
    *  Review the information entered, and click "Finish" to create the new user "Symphonyuser01."
4.  Add "Symphonyuser01" to "Symphony-group" User Group:
    *  In the ADUC console, locate the "Symphony-group" user group that you created earlier.
    *  Right-click on the "Symphony-group" user group and select "Properties."
    *  In the "Properties" dialog box, go to the "Members" tab.
    *  Click "Add" and then enter "Symphonyuser01" in the "Enter the object names to select" field.
    *  Click "Check Names" to validate the username.
    *  Click "OK" to add "Symphonyuser01" to the "Symphony-group" user group.
    *  Click "Apply" and then "OK" to save the changes.

    Creating user groups and users in the "pocdomain.local" Active Directory domain requires administrative privileges within that domain. Ensure that you have the necessary permissions to create groups and users. Also, always set strong passwords and follow security best practices when creating user accounts and groups in Active Directory to maintain a secure network environment.
    {: note}
