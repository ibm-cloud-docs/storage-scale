---

copyright:
  years: 2023
lastupdated: "2023-09-05"

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

# Enabling encryption by using GKLM
{: #enable-encryption}

The {{site.data.keyword.scale_short}} cluster file system can be encrypted by using the IBM Security® Guardium® Key Lifecycle Manager (GKLM). You can enable encryption features during deployment. The encryption provides highly available key servers for cryptographic operations on the GPFS file system. 
{: shortdesc}

The following diagram is an example of a {{site.data.keyword.scale_short}} deployment with encryption enabled. The deployment includes key servers that use the GKLM application.

![Architecture diagram](images/Scale-With-SGKLM5.svg){: caption="Figure 1. {{site.data.keyword.scale_short}} deployment with encryption" caption-side="bottom"}

By setting the `scale_encryption_enabled` deployment value to "true" during the deployment, the key servers are automatically deployed and configured with encryption. 

## Before you begin
{: #before-you-begin}

Before you begin, review the following tasks:

1. Make sure to complete the steps for [Getting started with {{site.data.keyword.scale_full_notm}}](/docs/storage-scale?topic=storage-scale-getting-started-tutorial).
2. Learn more about file system encryption by using [GKLM](https://www.ibm.com/docs/en/storage-scale/5.1.8?topic=environment-simplified-setup-using-sklm-self-signed-certificate#bl1adv_encryptionenv_simplified){: external}.

A minimum of two key servers are added to the cluster to avoid a single point of failure.
{: note}

## Configuring encryption deployment values
{: #configure-encryption-values}

To enable encryption on a Storage Scale cluster, the following variables need to be defined in [your workspace](/docs/storage-scale?topic=storage-scale-creating-workspace):

| Encryption variable | Description | Example value |
| ------------------- | ----------- | ------------- |
| `scale_encryption_enabled` | Set to "true" to enable encryption for the file system. | `true` |
| `scale_encryption_vsi_osimage_name` | Name of the image that you would like to use to create the GKLM server for encryption. The solution supports only a RHEL 8.8 stock image. | `gklm-custom-image-name` |
| `scale_encryption_vsi_profile` | Specify the virtual server instance profile type name used to create the storage nodes. For more information, see [Instance profiles](/docs/vpc?topic=vpc-profiles). | `bx2-2x8` |
| `scale_encryption_server_count` | The number of highly available encryption servers that are set up. You can choose between a minimum of two servers and a maximum of five servers. | `3` |
| `scale_encryption_admin_password` | The admin password for the GKLM application, which needs to be configured by using specific guidelines and [policies](https://www.ibm.com/docs/en/sgklm/4.1.1?topic=manager-password-policy){: external}. | `xxxxxxx` |
| `scale_encryption_dns_domain` | {{site.data.keyword.dns_full_notm}} domain name to be used for the GKLM cluster. | `gklmscale.com` |
| `scale_encryption_instance_key_pair` | Name of the SSH key configured in your {{site.data.keyword.cloud_notm}} account that is used to establish a connection to the encrypted Scale key server nodes. Make sure that the SSH key is present in the same resource group and region where the key servers are provisioned. The solution supports only one SSH key that can be attached to key server nodes. If you do not have an SSH key in your {{site.data.keyword.cloud_notm}} account, create one by using the [SSH keys](/docs/vpc?topic=vpc-ssh-keys) instructions. | `my-ssh-key` |
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

## Verifying encryption on the file system
{: #verify-encryption}

1. Log in to any of the clusters (storage or compute nodes) by running the following SSH command: 

    ```ssh
    ssh -J root@BASTION_SERVER vpcuser@STORAGE_NODE
    ```
    {: codeblock}

2. List the key server that was added to the cluster by running the following command:

    ```text
    mmkeyserver server show
    ```
    {: codeblock}

3. List the key components, for example tenant or client, by running the following commands:

    ```text
    mmkeyserver tenant show
    ```
    {: codeblock}

    ```text
    mmmkeyserver client show
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



