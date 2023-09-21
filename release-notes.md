---

copyright:
  years: 2022, 2023

lastupdated: "2023-09-21"

keywords: IBM Storage Scale release notes

subcollection: storage-scale

content-type: release-note

---

{{site.data.keyword.attribute-definition-list}}
{:external: target="_blank" .external}
{:release-note: data-hd-content-type='release-note'}

# Release notes for IBM Storage Scale
{: #release-notes}

Use these release notes to learn about the latest updates to {{site.data.keyword.scale_full}} that are grouped by date.
{: shortdesc}

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
:   The solution now supports deployment of compute and storage nodes on virtual server instance profiles that make use of 4th Generation Intel&reg; Xeon&reg; Scalable processors (code named Sapphire Rapids). Those profiles can be used with RHEL 7.9 and RHEL 8.6 VPC stock images and with the RHEL 8.6 custom images that are included with the solution. Use of the included RHEL 7.9 custom images is not supported by those profiles.

## August 2023
{: #hpc-scale-aug23}

### 22 August 2023
{: #hpc-scale-aug2223}
{: release-note}

Updated RHEL stock image version
:   The RHEL stock image version was updated from RHEL 8.4 to RHEL 8.6.

Updated {{site.data.keyword.scale_short}} software version
:   The {{site.data.keyword.scale_short}} version used for configuration of the compute and storage clusters has been updated from version 5.1.4.1 to 5.1.8.1.

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
:   The {{site.data.keyword.scale_short}} version used for configuration of the compute and storage clusters has been updated to version 5.1.4.1.

## June 2022
{: #storage-scale-june22}

### 10 June 2022
{: #storage-scale-june}
{: release-note}

Introducing {{site.data.keyword.scale_full_notm}} storage for HPC solutions
:   You now have the option for automated deployment of compute and storage clusters that use {{site.data.keyword.scale_full_notm}} for high-performance, shared storage for your HPC solutions. The clusters use the compute and storage node configurations that you select.

