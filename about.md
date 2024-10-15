---

copyright:
  years: 2022, 2023
lastupdated: "2023-09-20"

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
{:beta: .beta}
{:important: .important}

# About IBM Storage Scale
{: #about-storage-scale}

## How it works
{: #how-it-works}

With {{site.data.keyword.scale_full}}, you can deploy the High-Performance Computing (HPC) clusters by using {{site.data.keyword.scale_full_notm}} as the storage solution. This offering uses open source Terraform-based automation to provision and configure {{site.data.keyword.cloud}} resources. With simple steps to define configuration properties and the use of automated deployment, you can build your own storage-rich clusters in minutes. {{site.data.keyword.scale_full}} enables configuration for compute nodes and storage nodes to build a complete end to end working HPC cluster. The offering uses a bootstrap node where actual provisioning of compute and storage nodes and installation and configuration of {{site.data.keyword.scale_short}} takes place. The top-level Terraform code deploys the bootstrap node and starts subprocesses to trigger the secondary layer of Terraform code for actual deployment of cluster components.
{: shortdesc}

The bootstrap node (Ansible Controller Node in the [architecture diagram](/docs/storage-scale?topic=storage-scale-about-storage-scale#architecture-diagram)) performs the deployment and configuration of the compute and storage cluster resources. A custom image (see `bootstrap_osimage_name` in [Deployment values](/docs/storage-scale?topic=storage-scale-deployment-values)) is provided as part of this solution and it contains all of the automation scripts and packages that are needed for the bootstrap node. The bootstrap node is critical during the entire lifetime of this cluster. For example, you need this node for future actions like cleaning up resources. The bootstrap node should not be deleted until the cluster is no longer required.

The default VPC instance profile for the bootstrap node has been selected based on the performance of the Ansible scripts that are triggered to deploy the compute and storage cluster resources in parallel. If you choose a smaller VPC instance profile, the deployment time might be longer.

### Architecture diagrams
{: #architecture-diagram}

![Architecture diagram](images/hpccluster_scale_scratch_architecture.svg){: caption="Architecture diagram of a {{site.data.keyword.scale_full_notm}} cluster using scratch storage on {{site.data.keyword.cloud_notm}}" caption-side="bottom"}

![Architecture diagram persistent](images/hpccluster_scale_persistent_architecture_05_26_22.svg){: caption="Architecture diagram of a {{site.data.keyword.scale_full_notm}} cluster using persistent storage on {{site.data.keyword.cloud_notm}}" caption-side="bottom"}

## Storage types
{: #scale-storage-types}

The offering enables deployment of either scratch (or ephemeral) or persistent storage, depending on application requirements. A scratch configuration uses virtual server instances with instance storage, whereas a persistent configuration uses bare metal servers with locally attached NVMe storage. If a virtual server instance with instance storage is powered off, all data that is stored on the instance storage volumes is rendered inaccessible after a subsequent power up of the virtual server instance. Therefore, use of scratch storage is not recommended for long-running or mission-critical workloads. In addition to higher resilience, persistent storage provides higher performance and capacity than scratch storage. For more information, see [Storage types](/docs/storage-scale?topic=storage-scale-storage-types).

## BYOL license support
{: #license-support}

The offering supports the Bring-Your-Own-License (BYOL) model for {{site.data.keyword.scale_full_notm}} to deploy an HPC cluster on {{site.data.keyword.cloud_notm}}. Make sure that you have sufficient software licenses to deploy the required capacity on the {{site.data.keyword.cloud_notm}} cluster. Contact your {{site.data.keyword.cloud_notm}} sales or support team for evaluation licenses. Or, you can also try out the scratch storage capability of the offering on {{site.data.keyword.cloud_notm}} without a license by selecting the evaluation storage type.

## Deployment interfaces
{: #deployment-interfaces}

{{site.data.keyword.scale_short}} enables all three interfaces: UI, API, and CLI. To use the API and CLI interfaces, the Terraform-based automation code is available in this [public GitHub repository](https://github.com/IBM/ibm-spectrum-scale-ibm-cloud-schematics){: external}.

The offering enables the initial {{site.data.keyword.scale_short}}-based HPC cluster creation. Any updates that are needed post-deployment regarding {{site.data.keyword.scale_short}} configuration or setup should be performed by using {{site.data.keyword.scale_short}} tools and commands. If you use the {{site.data.keyword.bpshort}} interface to make changes to configuration properties and reapply those changes, you can cause disruptions to the running {{site.data.keyword.scale_short}} cluster. Restoring it back to a working state might not be easy.
{: important}

 
