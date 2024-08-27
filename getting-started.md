---

copyright:
  years: 2022, 2024
lastupdated: "2024-08-26"

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

With {{site.data.keyword.scale_full}}, you can deploy HPC clusters that use {{site.data.keyword.scale_full_notm}} as a storage solution. The deployment is performed by using Terraform and {{site.data.keyword.bplong_notm}} as automation frameworks.

## Prerequisites to deploy the cluster
{: #mandatory-steps}

Before you deploy your {{site.data.keyword.scale_short}} cluster, complete the following steps:

### Step 1: Create SSH key
{: #create-ssh-key}
{: step}

Create SSH keys in your {{site.data.keyword.cloud_notm}} account. You might need multiple SSH keys if you want to use different keys to access the bastion host, compute cluster, and storage cluster. Ensure that the SSH keys are present in the same resource group and region where the cluster is provisioned. The offering supports passing multiple, comma-separated SSH keys, if the cluster needs multiple SSH keys. For more information, see [Managing SSH keys](/docs/vpc?topic=vpc-managing-ssh-keys).

### Step 2: Create custom image
{: #create-custom-image}
{: step}

You can use the default image or create a custom image for compute, storage, and client nodes. But for bootstrap, GKLM (if encryption is enabled) and LDAP (if LDAP is enabled), only the default image is supported. For more information, see [Planning for custom images](/docs/vpc?topic=vpc-planning-custom-images).

For parallel vNIC and CES feature, only default images for storage are supported.
{: note}

{{site.data.keyword.cloud_notm}} provides pre-built images with RHEL to help you get started quickly. See the `storage_vsi_osimage_name`, `storage_bare_metal_osimage_name` and `compute_vsi_osimage_name` parameter in [Deployment values](/docs/storage-scale?topic=storage-scale-deployment-values). In addition to the base operating system, the image includes the {{site.data.keyword.scale_short}} software packages that allow for the {{site.data.keyword.scale_short}} shared file system to be automatically mounted and ready for use after the creation and configuration of the cluster is complete.

### Step 3: Gather public IP address
{: #gather-ip-address}
{: step}

You need to provide your public IP addresses from where you want to access the environment after it is provisioned. You provide these public IP addresses in the `remote_cidr_blocks` deployment value. For more information, see [Deployment values](/docs/storage-scale?topic=storage-scale-deployment-values).

### Step 4: Identify cluster deployment location
{: #identify-cluster}
{: step}

You need to decide where you want your cluster that is deployed by choosing an {{site.data.keyword.cloud_notm}} region and availability zone. You provide this location information when you configure your workspace. For more information, see [Region and data center locations for resource deployment](/docs/overview?topic=overview-locations).

## Enable optional features in the deployment values
{: #optional-steps}

After completing the mandatory steps, you can enable the optional parameters in deployment values in the {{site.data.keyword.scale_short}} cluster:

### Enable encryption
{: #enable-encryption}
{: step}

You need to decide whether you want to enable encryption for your file system. The {{site.data.keyword.scale_short}} cluster file system can be encrypted by using the IBM Security® Guardium® Key Lifecycle Manager (GKLM). If you want to enable encryption, you need to define the `scale_encryption_xxx` deployment values when you configure your workspace. For more information about enabling encryption and configuring these deployment values, see [Enabling encryption by using GKLM](/docs/storage-scale?topic=storage-scale-enable-encryption).

### Enable Parallel vNIC (MROT)
{: #enable-parallel-vnic}
{: step}

As per parallel vNIC support for each node of the compute and storage cluster, a secondary vNIC comes up based on the bandwidth of a profile. According to the parallel vNIC functionality, if a VSI profile has a Bandwidth Cap (Gbps) of 64 Gbps or more, then a secondary network interface is activated.

If CES is enabled, parallel vNIC functionality cannot be used.
{: note}

### Enable CES
{: #enable-ces}
{: ces}

To enable CES, set `total_protocol_cluster_instances` to a value greater than zero. Refer to [Deployment values](/docs/storage-scale?topic=storage-scale-deployment-values) topic for more details.

### Enable boot drive encryption for persistent storage
{: #enable-boot-encryption}
{: step}

To enable boot drive encryption for persistent storage, set `bms_boot_drive_encryption` parameter to true.

### Enable LDAP
{: #enable-ldap}
{: step}

To enable LDAP, set `enable_ldap` parameter to true and complete other variables such as `ldap_admin_password`, `ldap_user_name`, and `ldap_user_password`. For more information, refer to [Deployment values](/docs/storage-scale?topic=storage-scale-deployment-values). Existing LDAP is also supported.

### Enable AFM
{: #enable-afm}
{: step}

To enable AFM, set `total_afm_cluster_instances` parameter to a value greater than zero. For more information, refer to [Deployment values](/docs/storage-scale?topic=storage-scale-deployment-values).

## Next steps
{: #getting-started-next-steps}

Once the necessary input values are gathered to define your cluster configuration, you are ready to deploy your {{site.data.keyword.scale_full_notm}} cluster. The {{site.data.keyword.scale_short}} cluster can be deployed on {{site.data.keyword.cloud_notm}} by using the {{site.data.keyword.cloud_notm}} catalog tile, {{site.data.keyword.bpshort}} UI, {{site.data.keyword.bpshort}} CLI, or the {{site.data.keyword.bpshort}} APIs. If you want to deploy your cluster by using the CLI or API, review the prerequisites for your interface of choice:

* [Setting up the {{site.data.keyword.bplong_notm}} CLI](/docs/storage-scale?topic=storage-scale-setting-up-cli)
* [Setting up the {{site.data.keyword.bplong_notm}} API](/docs/storage-scale?topic=storage-scale-setting-up-api)

After you have created and reviewed for any additional prerequisites for your interface, perform the following: 

1. **Create a workspace** on {{site.data.keyword.bplong_notm}} that uses the [Terraform code](https://github.com/IBM/ibm-spectrum-scale-ibm-cloud-schematics){: external} that is developed for this offering. This step defines the set of configuration properties that are used to perform the automation. For more information, see [Creating a workspace](/docs/storage-scale?topic=storage-scale-creating-workspace).

2. **Generate a plan** to confirm whether the configuration properties are valid, so that when you run the Terraform code, all of the resources are provisioned correctly. If the validation fails, fix the configuration properties and try again.

3. **Apply a plan** triggers the actual deployment of the {{site.data.keyword.cloud_notm}} resources to have an HPC cluster up and running by the time the deployment completes. If the deployment fails, identify the reason for failure, fix the problem, and try again. If a change is needed to the configuration properties, it might be better to generate a plan again.

If instead of using {{site.data.keyword.bplong_notm}} you decide to deploy your {{site.data.keyword.scale_full_notm}} cluster through the {{site.data.keyword.cloud_notm}} catalog, when you click **Install**, the **Generate plan** action is skipped, and the steps go from **Create workspace** to **Apply plan** directly. You need to enter values in the catalog that work for your permissions and {{site.data.keyword.cloud_notm}} account. If the deployment fails, the {{site.data.keyword.bpshort}} UI can be used to fix the errors, and you can retry the **Apply Plan** step.
{: note}
