---

copyright:
  years: 2024
lastupdated: "2024-08-16"

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

# Enabling Active File Management to Cloud Object Storage
{: #config-afm-integration-cos}

Active File Management (AFM) to Cloud Object Storage (COS) is a feature that integrates {{site.data.keyword.scale_full_notm}} with {{site.data.keyword.cos_full_notm}} (COS). Active File Management (AFM) provides a hybrid cloud storage solution by allowing {{site.data.keyword.scale_full_notm}} to interact with IBM Cloud Object Storage as a storage target.

Active File Management (AFM) enables data sharing across clusters even if the networks are unreliable or have high latency.

You can use the cloud object storage to run workloads such as mobile applications, backup and restore, enterprise applications, big data analytics, and file server. These workloads can be cached on AFM to cloud object storage filesets for faster computation and synchronize back to the cloud object storage server.

![Architecture diagram.](images/afm_cos_architecture.svg){: caption="Figure 1.  AFM to Cloud Object Storage" caption-side="bottom"}

An AFM to cloud object storage fileset is supported on all existing AFM fileset modes that includes Read-Only (RO), Single Writer (SW), Local Updates (LU), and Independent Writer (IW).

{{site.data.keyword.scale_full_notm}} AFM gateway nodes can be a set of BMS or VSIs. 
{: note}

## Key features and benefits of COS-AFM
{: #keybenefits-cos-afm}

* Cloud Object Storage Integration: COS-AFM allows {{site.data.keyword.scale_full_notm}} to use IBM Cloud Object Storage as a storage target. This integration facilitates the movement of data between on-premises storage and cloud storage, enabling hybrid cloud solutions.

* Data Caching and Tiering: COS-AFM supports caching frequently accessed data locally within the IBM Storage Scale system while storing less frequently accessed data in IBM COS. This tiering approach optimizes data access performance and storage costs.

* Disaster Recovery and Data Protection: COS-AFM enables disaster recovery and data protection by replicating data to IBM COS. This ensures that a copy of the data is stored in a geographically separated location, providing resilience against data loss or system failures.

* Secure Data Management: COS-AFM supports secure data transmission and storage by using IBM COS built-in security features, such as encryption and access controls, to protect sensitive data.

## Before you begin
{: #beforeyoubegin-afm}

Before you begin, review the following information:

1. Make sure to complete the steps for [Getting started with IBM Storage Scale](/docs/storage-scale?topic=storage-scale-getting-started-tutorial).

2. Learn more about active file management by using the link, [Introduction to AFM to cloud object storage](https://www.ibm.com/docs/en/storage-scale/5.2.0?topic=overview-introduction-afm-cloud-object-storage).

## Configuring AFM deployment
{: #configure-afm-deployment}

To enable the AFM feature on a Storage Scale cluster, the following variables need to be defined in [your workspace](/docs/storage-scale?topic=storage-scale-creating-workspace&interface=ui):

|AFM Variable|	Description|	Example value|
|-------------|------------|--------------|
| `total_afm_cluster_instances`  | Total number of instances that you need to provision AFM nodes and enable AFM.	| 0  |
| `afm_vsi_profile` | The virtual instance or bare metal server instance profile type name used to create the AFM gateway nodes. For more information, see [Instance Profiles](https://cloud.ibm.com/docs/vpc?topic=vpc-profiles&interface=ui) and [bare metal server profiles](https://cloud.ibm.com/docs/vpc?topic=vpc-bare-metal-servers-profile&interface=ui). | bx2-32x128 |
| `afm_cos_config` | Provide details for the Cloud Object Storage (COS) instance, including information about the COS bucket, service credentials (HMAC key), AFM fileset, mode (such as Read-only (RO), Single writer (SW), Local updates (LU), and Independent writer (IW)), storage class (standard, vault, cold, or smart), and bucket type (single_site_location, region_location, cross_region_location). Note: The `afm_cos_config` can contain up to 5 entries. For more details on COS bucket locations, refer to [Endpoints and storage locations](https://cloud.ibm.com/docs/cloud-object-storage/basics?topic=cloud-object-storage-endpoints) documentation. | [{cos_instance="", bucket_name="", bucket_region="us-south", cos_service_cred_key="", afm_fileset="indwriter", mode="iw", bucket_storage_class="smart", bucket_type="region_location"}] |

A successful scale deployment with the AFM feature enabled involves the following components:

* Storage Cluster: This includes designated storage and a set of AFM gateway node.

* Filesets: Created on the storage cluster with the appropriate fileset mode specified in the configuration variables.

* Cloud Object Storage (COS): COS buckets that are defined with specified bucket types and storage classes with HMAC keys for secure access.

## Verifying AFM on the Storage Cluster
{: #verify-afm-storage-cluster}

Verify Active File Management on the Storage Cluster by using the following steps:

1. Login to one of the storage nodes by using SSH with a jump host by running the command:
  ` ssh -J root@BASTION_SERVER vpcuser@STORAGE_NODE`

2. To get details about a specific fileset and its mode (including AFM specifics), run the `mmlsfileset fs1 indwriter --afm -L` command.
  *  fs1: The name of the file system.
  *  indwriter: The name of the fileset.
  *  --afm: Option to include AFM-specific details.
  *  -L: Option to list all details about the fileset.

3. To check the synchronization status from an AFM fileset to the home cluster, run the `mmafmctl  fs1 getstate -j indwriter` command.
  *  fs1: The name of the file system.
  *  getstate: The operation to retrieve the state.
  *  -j indwriter: Specifies the fileset for which to check the synchronization state.

4. To check the HMAC for a bucket, run the `mmafmcoskeys storage-scale-bucket get` command.
  *  storage-scale-bucket: The name of the Cloud Object Storage bucket.
  *  get: Operation to retrieve the HMAC key associated with the specified bucket.

## LDAP User Access to COS Filesets
{: #ldap-user-access-cos}

It is recommended to adjust the ownership, group, and permissions of the fileset to meet the specific needs of the users. While our automation process creates the fileset and establishes the initial relationship, it is the responsibility of the system administrator or user to make sure that these settings are correctly configured afterward. This approach ensures that the LDAP users can access the data with the necessary permissions, maintaining a secure and manageable environment. Properly managing ownership and permissions provides a controlled way for users to interact with the fileset while preserving the integrity and security of the system.
