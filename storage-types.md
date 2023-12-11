---

copyright:
  years: 2022, 2023
lastupdated: "2023-09-12"

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

# Storage types
{: #storage-types}

The {{site.data.keyword.scale_short}} solution offers three different storage types: scratch, persistent, and evaluation. 
{: shortdesc}

## Scratch storage
{: #scratch-storage}

A scratch configuration uses virtual server instances with instance storage. If a virtual server instance with instance storage is powered off, all data stored on the instance storage volumes is rendered inaccessible after a subsequent power up of the virtual server instance. Therefore, use of scratch storage is not recommended for long running or mission-critical workloads.

## Persistent storage
{: #persistent-storage}

A persistent configuration uses bare metal servers with locally attached NVMe storage. In addition to higher resilience, persistent storage provides higher performance and capacity than scratch storage.

## Evaluation storage
{: #evaluation-storage}

Evaluation storage is based on {{site.data.keyword.scale_short}} Developer Edition. This option supports all advanced features of {{site.data.keyword.scale_short}} Data Management Edition but is limited to 12TB of storage.

With evaluation storage, you can try out the {{site.data.keyword.scale_short}} solution on {{site.data.keyword.cloud}} without a license. The automated deployment is done on virtual server instances with instance storage, with {{site.data.keyword.scale_short}} Developer Edition packages, and is available only for prototyping and testing purposes.

## Storage type comparison
{: #storage-type-comparison-table}

|      | Scratch (default) | Persistent | Evaluation |
| ---- | ----------------- | ---------- | ---------- |
| **Storage cluster nodes** | Virtual server instances | Bare metal servers | Virtual server instances |
| **Compute cluster nodes** | Virtual server instances | Virtual server instances | Virtual server instances |
| **Storage cluster node count** | Min 3  \n Max 18 | Min 3  \n Max 10 | Min 3  \n Max 18 |
| **Compute cluster node count** | Min 3  \n Max 64 | Min 3  \n Max 64 | Min 3  \n Max 64 |
| **Storage cluster OS support** | RHEL 8.8  \n (custom or stock images) | RHEL 8.8  \n (stock image) | RHEL 8.8  \n (custom image) |
| **Compute cluster OS support** | RHEL 7.9, 8.8  \n (custom or stock images) | RHEL 7.9, 8.8  \n (custom or stock images) | RHEL 7.9, 8.8  \n (custom or stock images) |
| **Storage Scale edition and version** | Storage Scale Data Management Edition v5.1.9.0 | Storage Scale Data Management Edition v5.1.9.0 | Storage Scale Developer Edition v5.1.9.0 |
| **IBM Customer Number required?** | Yes | Yes | No |
| **Customer support available?** | Yes | Yes | No |
{: row-headers}
{: caption="Table 1. Storage Scale storage types comparison" caption-side="bottom"}
{: summary="The first row of the table describes a Storage Scale feature, and the first column describes the specifics of that feature as it pertains to scratch storage. The second column describes the specifics of persistent storage, and the third column describes the specifics of evaluation storage, which map to the Storage Scale feature in each row."}

For more information about {{site.data.keyword.scale_short}} editions, see [{{site.data.keyword.scale_full_notm}} product editions](https://www.ibm.com/docs/en/spectrum-scale/5.1.5?topic=overview-spectrum-scale-product-editions){: external}.
