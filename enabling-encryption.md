---

copyright:
  years: 2023, 2024
lastupdated: "2024-08-20"

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
{:beta: .beta}
{:row-headers: .row-headers}
{:table: .aria-labeledby="caption"}

# Enabling encryption
{: #enable-encryption}

The {{site.data.keyword.scale_short}} cluster file system can be encrypted by using the IBM Security® Guardium® Key Lifecycle Manager (GKLM) or the IBM KeyProtect service. You can enable encryption features during deployment. The encryption provides highly available key servers for cryptographic operations on the GPFS file system.
{: shortdesc}

## Enabling encryption by using GKLM
{: #enable-encryption-gklm}

The following diagram is an example of a {{site.data.keyword.scale_short}} deployment with encryption enabled. The deployment includes key servers that use the GKLM application.

![Architecture diagram](images/Scale-With-SGKLM5.svg){: caption="Figure 1. {{site.data.keyword.scale_short}} deployment with encryption" caption-side="bottom"}

By setting the `scale_encryption_type` deployment value to **gklm**, the key servers are automatically deployed and configured for encryption during the deployment.

### Before you begin
{: #before-you-begin-gklm}

Before you begin, review the following tasks:

1. Make sure to complete the steps for [Getting started with {{site.data.keyword.scale_full_notm}}](/docs/storage-scale?topic=storage-scale-getting-started-tutorial).
2. Learn more about file system encryption by using [GKLM](https://www.ibm.com/docs/en/storage-scale/5.1.8?topic=environment-simplified-setup-using-sklm-self-signed-certificate#bl1adv_encryptionenv_simplified){: external}.

A minimum of two key servers are added to the cluster to avoid a single point of failure.
{: note}

### Configuring encryption deployment values
{: #configure-encryption-values-gklm}

To enable encryption on a Storage Scale cluster, the following variables need to be defined in [your workspace](/docs/storage-scale?topic=storage-scale-creating-workspace):

| Encryption variable | Description | Example value |
| ------------------- | ----------- | ------------- |
|`scale_encryption_type` | To enable filesystem encryption, choose `key_protect` or 'gklm'. The default value is 'null', which disables encryption. | `gklm` |
| `scale_encryption_vsi_osimage_name` | Specify the image name to create the GKLM server when `scale_encryption_type` is set to 'gklm'. Only RHEL 8.8 stock images are supported. | `gklm-custom-image-name` |
| `scale_encryption_vsi_profile` | Specify the virtual server instance profile type to create storage nodes when `scale_encryption_type` is set to 'gklm'. For more information, see [Instance profiles](/docs/vpc?topic=vpc-profiles). | `bx2-2x8` |
| `scale_encryption_server_count` | Specify the number of servers for a high-availability encryption setup when `scale_encryption_type` is set to 'gklm'. The minimum is 2, and the maximum is 5. | `3` |
| `scale_encryption_admin_password` | Password used for administrative operations in KeyProtect or GKLM. The password must be between 8 and 20 characters, and include at least three alphabetic characters (one uppercase and one lowercase), two numbers, and one special character from (~@_+:). It should not contain the username. For more information, see [GKLM password policy](https://www.ibm.com/docs/en/sgklm/4.2){: external}. | `xxxxxxx` |
| `scale_encryption_dns_domain` | Specify the IBM Cloud DNS Services domain name for the GKLM cluster when `scale_encryption_type` is set to 'gklm'. Note: If an existing DNS domain is in use, a new domain must be provided, as existing domains are not supported. | `gklmscale.com` |
| `scale_encryption_instance_key_pair` | Specify the name of the SSH key in your IBM Cloud account for connecting to the Scale Encryption keyserver nodes when 'scale_encryption_type' is set to 'gklm'. Ensure the SSH key is in the same resource group and region as the keyservers. Only one SSH key is supported for the keyserver nodes. If you do not have an SSH key in your {{site.data.keyword.cloud_notm}} account, create one by using the [SSH keys](/docs/vpc?topic=vpc-ssh-keys&interface=ui) instructions. | `my-ssh-key` |
{: caption="Table 1. Encryption variables and example values" caption-side="bottom"}

After a successful cluster creation, the following resources are automatically configured to encrypt the file system: 

* The key servers are deployed along with the Storage Scale cluster.
* The admin password is updated for the GKLM application.
* An SSL certificate is created on the key server. 
* Replication occurs between the primary and clone key servers.
* The key servers are added to each cluster (storage and compute).
* Tenants and clients are created on each cluster.
* The master key is created for encrypting the file system.
* An encryption policy is created and applied to the file system.

The storage and compute clusters have access to the encrypted file system, and any files that are created on the file system are encrypted.

### Verifying encryption on the file system
{: #verify-encryption-gklm}

1. Log in to any of the clusters (storage or compute nodes) by running the following SSH command: 

    ```ssh
    ssh -J root@BASTION_SERVER vpcuser@STORAGE_NODE
    ```
    {: codeblock}

2. List the key server that was added to the cluster by running the following command:

    ```text
    mmkeyserv server show
    ```
    {: codeblock}

3. List the key components, for example tenant or client, by running the following commands:

    ```text
    mmkeyserv tenant show
    ```
    {: codeblock}

    ```text
    mmkeyserv client show
    ```
    {: codeblock}

4. Validate the policy that was applied to the cluster by running the following command:

    ```text
    mmlspolicy FILESYSTEM_NAME -L
    ```
    {: codeblock}

5. Check the encrypted file by running the following command:

    ```text
    mmlsattr -n gpfs.Encryption FILE_NAME
    ```
    {: codeblock}


To learn more about encryption for {{site.data.keyword.scale_short}} or different encryption use cases, see [Encryption](https://www.ibm.com/docs/en/storage-scale/5.1.8?topic=administering-encryption){: external}.

### Accessing the GKLM dashboard
{: #access-gklm}

1. Open a new command line terminal.
2. Run the following commands from the local machine:

    ```
    ssh -L 9443:localhost:9443 -J ubuntu@<bastion_host_IP> vpcuser@<first_GKLM_server>
    ```
    {: codeblock}

3. Open the browser on the local machine, and run https://localhost:9443/.
4. Enter the username **SKLMAdmin** and the password that you set up when you created your workspace to access the GKLM GUI.

## Enabling encryption by using IBM KeyProtect
{: #enable-encryption-keyprotect}

The Storage Scale cluster file system can be encrypted using IBM® Key Protect. The IBM Key Protect on IBM Cloud service helps you provision and store encrypted keys for apps across IBM Cloud services, so you can see and manage data encryption and the entire key lifecycle from one central location.

You can enable encryption features during deployment by setting the `scale_encryption_type` deployment value to `key_protect`.

### Before you begin
{: #before-you-begin-keyprotect}

Before you begin, review the following tasks:

1. Make sure to complete the steps for [Getting started with {{site.data.keyword.scale_full_notm}}](/docs/storage-scale?topic=storage-scale-getting-started-tutorial).
2. Learn more about file system encryption by using [IBM Key Protect](/docs/key-protect?topic=key-protect-getting-started-tutorial).

### Configuring encryption deployment values
{: #configure-encryption-values-keyprotect}

To enable encryption on a Storage Scale cluster, the following variables need to be defined in [your workspace](/docs/storage-scale?topic=storage-scale-creating-workspace):

| Encryption variable | Description | Example value |
| ------------------- | ----------- | ------------- |
|`scale_encryption_type` | To enable filesystem encryption, choose either 'key_protect' or 'gklm'. The default value is 'null', which disables encryption. | `key_protect` |
| `scale_encryption_admin_password` | Password used for administrative operations in KeyProtect or GKLM. The password must be between 8 and 20 characters, and include at least three alphabetic characters (one uppercase and one lowercase), two numbers, and one special character from (~@_+:). It should not contain the username. For more information, see [GKLM password policy](https://www.ibm.com/docs/en/sgklm/4.2). | `xxxxxxx` |

### Verifying encryption on the file system
{: #verify-encryption-keyprotect}

1. Log in to any of the clusters (storage or compute nodes) by running the following SSH command and switch to the `root` user: 

    ```ssh
    ssh -J root@BASTION_SERVER vpcuser@STORAGE_NODE
    sudo -i
    ```
    {: codeblock}

2. Validate the policy that was applied to the cluster by running the following command:

    ```text
    mmlspolicy FILESYSTEM_NAME -L
    ```
    {: codeblock}

3. Check the encrypted file by running the following command:

    ```text
    mmlsattr -n gpfs.Encryption FILE_NAME
    ```
    {: codeblock}

