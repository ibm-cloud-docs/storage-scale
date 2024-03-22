---

copyright:
  years: 2022, 2023, 2024
lastupdated: "2024-03-19"

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
{:step: data-tutorial-type='step'}
{:table: .aria-labeledby="caption"}

# Getting started with IBM Storage Scale
{: #getting-started-tutorial}

With {{site.data.keyword.scale_full}}, you can deploy HPC clusters that use {{site.data.keyword.scale_full_notm}} as a storage solution. The deployment is performed by using Terraform and {{site.data.keyword.bplong_notm}} as automation frameworks. The following steps outline the high-level flow of events:

1. **Create a workspace** on {{site.data.keyword.bplong_notm}} that uses the [Terraform code](https://github.com/IBM/ibm-spectrum-scale-ibm-cloud-schematics){: external} that is developed for this offering. This step defines the set of configuration properties that are used to perform the automation.
2. **Generate a plan** to confirm whether the configuration properties are valid, so that when you run the Terraform code, all of the resources are provisioned correctly. If the validation fails, fix the configuration properties and try again.
3. **Apply a plan** triggers the actual deployment of the {{site.data.keyword.cloud_notm}} resources to have an HPC cluster up and running by the time the deployment completes. If the deployment fails, identify the reason for failure, fix the problem, and try again. If a change is needed to the configuration properties, it might be better to generate a plan again.

If instead of using {{site.data.keyword.bplong_notm}} you decide to deploy your {{site.data.keyword.scale_full_notm}} cluster through the {{site.data.keyword.cloud_notm}} catalog, when you click **Install**, the **Generate plan** action is skipped, and the steps go from **Create workspace** to **Apply plan** directly. You need to enter values in the catalog that work for your permissions and {{site.data.keyword.cloud_notm}} account. If the deployment fails, the {{site.data.keyword.bpshort}} UI can be used to fix the errors, and you can retry the **Apply Plan** step.
{: note}

Before you can deploy your {{site.data.keyword.scale_short}} cluster, you need to create or gather some information. To get started, complete the following steps.

## Create SSH key
{: #create-ssh-key}
{: step}

Create SSH keys in your {{site.data.keyword.cloud_notm}} account. You might need multiple SSH keys if you want to use different keys to access the bastion host, compute cluster, and storage cluster. Ensure that the SSH keys are present in the same resource group and region where the cluster is provisioned. The offering supports passing multiple, comma-separated SSH keys, if the cluster needs multiple SSH keys. For more information, see [Managing SSH keys](/docs/vpc?topic=vpc-managing-ssh-keys).

## Create custom image
{: #create-custom-image}
{: step}

You can create a custom image for your compute nodes or use the default image. But for bootstrap and storage nodes, only the default image is supported. For more information, see [Planning for custom images](/docs/vpc?topic=vpc-planning-custom-images). 

{{site.data.keyword.cloud_notm}} provides pre-built images with RHEL to help you get started quickly. Depending on your choice of RHEL 7.9 or RHEL 8.8, you can select `hpcc-scale5192-rhel79` or `hpcc-scale5192-rhel88`. See the `compute_vsi_osimage_name` parameter in [Deployment values](/docs/storage-scale?topic=storage-scale-deployment-values). In addition to the base operating system, the image includes the {{site.data.keyword.scale_short}} software packages that allow for the {{site.data.keyword.scale_short}} shared file system to be automatically mounted and ready for use after the creation and configuration of the cluster is complete.

## Gather public IP address
{: #gather-public-ip-address}
{: step}

You need to provide your public IP addresses from where you want to access the environment after it's provisioned. You provide these public IP addresses in the `remote_cidr_blocks` deployment value. For more information, see [Deployment values](/docs/storage-scale?topic=storage-scale-deployment-values).

## Identify cluster deployment location
{: #identify-location}
{: step}

You need to decide where you want your cluster deployed by choosing an {{site.data.keyword.cloud_notm}} region and availability zone. You provide this location information when you configure your workspace. For more information, see [Region and data center locations for resource deployment](/docs/overview?topic=overview-locations).

## Enable encryption (Optional)
{: #enable-encryption}
{: step}

You need to decide whether you want to enable encryption for your file system. The {{site.data.keyword.scale_short}} cluster file system can be encrypted by using the IBM Security® Guardium® Key Lifecycle Manager (GKLM). If you want to enable encryption, you need to define the `scale_encryption_xxx` deployment values when you configure your workspace. For more information about enabling encryption and configuring these deployment values, see [Enabling encryption by using GKLM](/docs/storage-scale?topic=storage-scale-enable-encryption).

## Next steps
{: #getting-started-next-steps}
{: step}

After you've gathered the necessary input values to define your cluster configuration, you are ready to deploy your {{site.data.keyword.scale_full_notm}} cluster. The {{site.data.keyword.scale_short}} cluster can be deployed on {{site.data.keyword.cloud_notm}} by using the {{site.data.keyword.cloud_notm}} catalog tile, {{site.data.keyword.bpshort}} UI, {{site.data.keyword.bpshort}} CLI, or the {{site.data.keyword.bpshort}} APIs. If you want to deploy your cluster by using the CLI or API, review the prerequisites for your interface of choice:

* [Setting up the {{site.data.keyword.bplong_notm}} CLI](/docs/storage-scale?topic=storage-scale-setting-up-cli)
* [Setting up the {{site.data.keyword.bplong_notm}} API](/docs/storage-scale?topic=storage-scale-setting-up-api)

After you've created and gathered your information and reviewed any additional prerequisites for your interface of choice, you are ready to begin [Creating a workspace](/docs/storage-scale?topic=storage-scale-creating-workspace).
