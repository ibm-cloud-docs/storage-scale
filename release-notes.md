---

copyright:
  years: 2022, 2024

lastupdated: "2024-10-08"

keywords: IBM Storage Scale release notes

subcollection: storage-scale

content-type: release-note

---

{{site.data.keyword.attribute-definition-list}}
{:external: target="_blank" .external}
{:release-note: data-hd-content-type='release-note'}

# Release notes
{: #release-notes}

This release notes for {{site.data.keyword.scale_full}} describes the new changes added to the release.
{: shortdesc}

## October 2024
{: #storage-scale-oct24}

### 15 October 2024
{: #storage-scale-oct1015}
{: release-note}

Updated RHEL stock image version
:   The RHEL stock image version has been updated from RHEL 8.8 to RHEL 8.10.

Updated Storage Scale software version
:   The Storage Scale version used to configure the compute and storage clusters has been updated from version 5.2.0.1 to 5.2.1.1.

Bare metal support for protocol nodes
:   Dedicated protocol nodes can be deployed on bare metal servers.

## September 2024
{: #storage-scale-sep24}

### 12 September 2024
{: #storage-scale-sep0912}
{: release-note}

Bug Fixes
:  Updated the SGKLM images.

### 4 September 2024
{: #storage-scale-sep0904}
{: release-note}

Enabling AFM feature
:  Enabling AFM feature allows you to connect {{site.data.keyword.scale_full_notm}} filesets to Cloud Object Storage (COS).

Enabling Encryption with IBM Key Protect
:  You can now encrypt the Storage Scale cluster file systems using IBM Key Protect. To enable encryption, specify the `scale_encryption_xxx` deployment values when setting up your workspace. For detailed instructions on enabling encryption and configuring these deployment values, refer to the [Enabling Encryption](/docs/storage-scale?topic=storage-scale-enable-encryption) documentation.

## June 2024
{: #storage-scale-june24}

### 13 June 2024
{: #storage-scale-june0613}
{: release-note}

Following are the feature changes made for this release:

Enabling colocation feature
:  Enabling colocation designates the subset of Storage server as protocol nodes. If disabled, the protocol nodes are created on a dedicated virtual server irrespective of storage type.

Enabling boot drive encryption feature for Bare Metal
:  Enable the boot drive encryption feature for IBM Bare Metal servers on VPC to use the local disk drive for boot. Boot drive encryption encrypts the drive by using LUKS and leverages the local Trusted Platform Module (TPM) for key management.

Updated Storage Scale software version
:  The Storage Scale version that is used for the configuration of the compute and storage clusters is updated from version 5.1.9.2 to 5.2.0.1.

## April 2024
{: #storage-scale-apr24}

### 26 April 2024
{: #storage-scale-apr0426}
{: release-note}

**Enhancements**
* Introducing a single CES hostname that is mapped to all the CES IP addresses to distribute the workload.
* LDAP user integration with password-based authentication.
* Minor encryption, NFS, and security fixes.

## March 2024
{: #storage-scale-mar24}

### 22 March 2024
{: #storage-scale-mar0324}
{: release-note}

Updated Storage Scale software version
:  The Storage Scale version that is used for configuration of the compute and storage clusters is updated from version 5.1.9.0 to 5.1.9.2

## December 2023
{: #storage-scale-sept23}

### 11 December 2023
{: #storage-scale-sept0423}
{: release-note}

Enabling CES Feature
:  You can now enable the CES feature to enable access to data stored in Storage Scale. By providing a nonzero value to the `total_protocol_cluster_instances` variable, you can enable the CES feature. For more information about enabling CES and configuring these deployment values, see Enabling CES Feature.

User Management
:   LDAP is an optional component for CES, allowing users to either use an existing LDAP server or set up a new LDAP node specifically for the CES cluster. By setting up the ldap_basedns deployment value to the required domain name during the deployment, the LDAP feature is integrated along with the Scale CES. For more information about LDAP integration and configuring these deployment values, see Configuring CES with LDAP.

Updated RHEL stock image version
:   The RHEL stock image version was updated from RHEL 8.6 to RHEL 8.8.

Updated Storage Scale software version
:   The Storage Scale version that is used for configuration of the compute and storage clusters has been updated from version 5.1.8.1 to 5.1.9.0

## September 2023
{: #storage-scale-sept23}

### 21 September 2023
{: #storage-scale-sept2123}
{: release-note}

Encryption with GKLM
:   You can now encrypt your {{site.data.keyword.scale_short}} cluster file system by using the IBM Security® Guardium® Key Lifecycle Manager (GKLM). If you want to enable encryption, you need to define the `scale_encryption_xxx` deployment values when you configure your workspace. For more information about enabling encryption and configuring these deployment values, see [Enabling encryption by using GKLM](/docs/storage-scale?topic=storage-scale-enable-encryption). 

Parallel vNIC (Virtual Network Interface Controller)
:   Create a secondary vNIC for higher VSI profiles to separate the data and storage traffic across different vNICs for higher bandwidth and better performance.

Sapphire Rapids-based virtual server instance profile support
:   The solution now supports deployment of compute nodes on virtual server instance profiles that use 4th Generation Intel&reg; Xeon&reg; Scalable processors (code named Sapphire Rapids). Those profiles can be used only with the RHEL 8.8 custom image.

## August 2023
{: #hpc-scale-aug23}

### 22 August 2023
{: #hpc-scale-aug2223}
{: release-note}

Updated RHEL stock image version
:   The RHEL stock image version was updated from RHEL 8.4 to RHEL 8.8.

Updated {{site.data.keyword.scale_short}} software version
:   The {{site.data.keyword.scale_short}} version that is used for configuration of the compute and storage clusters has been updated from version 5.1.4.1 to 5.1.8.1.

## March 2023
{: #storage-scale-mar23}

### 10 March 2023
{: #storage-scale-mar1023}
{: release-note}

Persistent {{site.data.keyword.scale_short}} storage (GA)
:   The persistent {{site.data.keyword.scale_short}} storage feature is now generally available. Users can deploy a persistent storage configuration that uses bare metal servers with locally attached NVMe storage.

## November 2022
{: #storage-scale-nov22}

### 10 November 2022
{: #storage-scale-nov1022}
{: release-note}

{{site.data.keyword.scale_short}} evaluation (Developer Edition) storage
:   With the evaluation storage feature, users can try out the {{site.data.keyword.scale_short}} solution on {{site.data.keyword.cloud}} without a license. The automated deployment is done on virtual server instances with instance storage, with {{site.data.keyword.scale_short}} Developer Edition packages, and is available only for prototyping and testing purposes. For more information, see [Storage types](/docs/storage-scale?topic=storage-scale-storage-types).

## September 2022
{: #storage-scale-sept22}

### 22 September 2022
{: #storage-scale-sept2222}
{: release-note}

Persistent {{site.data.keyword.scale_short}} storage (Beta)
:   With the persistent {{site.data.keyword.scale_short}} storage beta feature, users can now deploy a persistent storage configuration that uses bare metal servers with locally attached NVMe storage. This is a beta feature that is available for prototyping and testing purposes. There are no warranties, SLAs, or support provided for persistent storage and it is not intended for production use.

Updated {{site.data.keyword.scale_short}} software version
:   The {{site.data.keyword.scale_short}} version that is used for configuration of the compute and storage clusters has been updated to version 5.1.4.1.

## June 2022
{: #storage-scale-june22}

### 10 June 2022
{: #storage-scale-june}
{: release-note}

Introducing {{site.data.keyword.scale_full_notm}} storage for HPC solutions
:   You now have the option for automated deployment of compute and storage clusters that use {{site.data.keyword.scale_full_notm}} for high-performance, shared storage for your HPC solutions. The clusters use the compute and storage node configurations that you select.
