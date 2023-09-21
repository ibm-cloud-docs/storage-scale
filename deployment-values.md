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
{:table: .aria-labeledby="caption"}

# Deployment values
{: #deployment-values}

The following deployment values can be used to configure the {{site.data.keyword.scale_short}} cluster instance on {{site.data.keyword.cloud}}:

The following deployment values can be used to configure the {{site.data.keyword.scale_short}} cluster instance on {{site.data.keyword.cloud}}:

| Value | Description | Is it required? | Default value |
| ----- | ----------- | --------------- | ------------ |
| `bastion_key_pair` | Name of the SSH key configured in your {{site.data.keyword.cloud_notm}} account that is used to establish a connection to the bastion and bootstrap nodes. Make sure that the SSH key is present in the same resource group and region where the cluster is being provisioned. If you do not have an SSH key in your {{site.data.keyword.cloud_notm}} account, create one by using the [SSH keys](/docs/vpc?topic=vpc-ssh-keys) instructions. | Yes | None |
| `bastion_osimage_name` | Name of the image that will be used to provision the bastion node for the {{site.data.keyword.scale_short}} cluster. Only Ubuntu stock images of any version available to the {{site.data.keyword.cloud_notm}} account in the specific region are supported. | No | ibm-ubuntu-20-04-3-minimal-amd64-2 |
| `bastion_vsi_profile` | The virtual server instance profile type name used to create the bastion node. For more information, see [Instance profiles](/docs/vpc?topic=vpc-profiles). | No | cx2-2x4 |
| `bootstrap_osimage_name` | Name of the custom image that you would like to use to create the bootstrap node for the {{site.data.keyword.scale_short}} cluster. The solution supports only the default custom image. | No | hpcc-scale-bootstrap-v2-2 |
| `bootstrap_vsi_profile` | The virtual server instance profile type name used to create the bootstrap node. For more information, see [Instance profiles](/docs/vpc?topic=vpc-profiles). | No | bx2-8x32 |
| `compute_cluster_filesystem_mountpoint` | Compute cluster (accessingCluster) file system mount point.  The accessingCluster is the cluster that accesses the owningCluster. For more information, see [Mounting a remote GPFS file system](https://www.ibm.com/docs/en/spectrum-scale/5.1.4?topic=system-mounting-remote-gpfs-file){: external}. | No | `/gpfs/fs1` |
| `compute_cluster_gui_password` | Password that is used for logging in to the compute cluster through the GUI. The password should contain a minimum of eight characters. For a strong password, use a combination of uppercase and lowercase letters, one number, and a special character. Make sure that the password doesn't contain the username. | Yes | None |
| `compute_cluster_gui_username` | GUI username to perform system management and monitoring tasks on the compute cluster. The username should be at least four characters (any combination of lowercase and uppercase letters). | Yes | None |
| `compute_cluster_key_pair` | Name of the SSH key configured in your {{site.data.keyword.cloud_notm}} account that is used to establish a connection to the compute cluster nodes. Make sure that the SSH key is present in the same resource group and region where the cluster is provisioned. The solution supports only one SSH key that can be attached to compute nodes. If you do not have an SSH key in your {{site.data.keyword.cloud_notm}} account, create one by using the [SSH keys](/docs/vpc?topic=vpc-ssh-keys) instructions. | Yes | None |
| `compute_vsi_profile` | The virtual server instance profile type name used to create the compute cluster nodes. For more information, see [Instance profiles](/docs/vpc?topic=vpc-profiles). | No | bx2-2x8 |
| `compute_vsi_osimage_name` | Name of the image that you would like to use to create the compute cluster nodes for the {{site.data.keyword.scale_short}} cluster. The solution supports both stock and custom images that use RHEL 7.9 and 8.6 versions that have the appropriate {{site.data.keyword.scale_short}} functionality. The supported custom images mapping for the compute nodes can be found [here](https://github.com/IBM/ibm-spectrum-scale-ibm-cloud-schematics/blob/main/image_map.tf#L15){: external}. If you'd like, you can follow the instructions for [Planning for custom images](/docs/vpc?topic=vpc-planning-custom-images) to create your own custom image. | No | hpcc-scale5181-rhel86 |
| `filesystem_block_size` | File system [block size](https://www.ibm.com/docs/en/spectrum-scale/5.1.4?topic=considerations-block-size){: external}. {{site.data.keyword.scale_short}} supported block sizes (in bytes): 256K, 512K, 1M, 2M, 4M, 8M, and 16M. | No | 4M |
| `ibmcloud_api_key` | The {{site.data.keyword.cloud_notm}} API key for the {{site.data.keyword.cloud_notm}} account where the {{site.data.keyword.scale_short}} cluster needs to be deployed. For more information on how to create an API key, see [Managing user API keys](/docs/account?topic=account-userapikey&interface=ui). | Yes | None |
| `ibm_customer_number` | The {{site.data.keyword.IBM_notm}} Customer Number (ICN) that is used for the Bring Your Own License (BYOL) entitlement check. **Note:** An ICN is not required for evaluation storage. For more information on how to find your ICN, see [What is my {{site.data.keyword.IBM_notm}} Customer Number (ICN)?](https://www.ibm.com/support/pages/what-my-ibm-customer-number-icn){: external} | Conditional | None |
| `remote_cidr_blocks` | Comma-separated list of IP addresses that can access the {{site.data.keyword.scale_short}} cluster bastion node through SSH. For security purposes, provide the public IP addresses assigned to the devices that are authorized to establish SSH connections (for example, 169.45.117.34). To fetch the IP address of the device, use https://ipv4.icanhazip.com/. | Yes | None |
| `resource_group` | Resource group name from your {{site.data.keyword.cloud_notm}} account where the VPC resources should be deployed. For more information, see [Managing resource groups](/docs/account?topic=account-rgs). | No | Default |
| `resource_prefix` | Prefix that is used to name the {{site.data.keyword.cloud_notm}} resources that are provisioned to build the {{site.data.keyword.scale_short}} cluster. Make sure that the prefix is unique since you cannot create multiple resources with the same name. The maximum length of supported characters is 64. | No | spectrum-scale |
| `scale_encryption_enabled` | Set to "true" if you want to enable encryption for the file system. For more information, see [Enabling encryption by using GKLM](/docs/hpc-spectrum-scale?topic=hpc-spectrum-scale-enable-encryption). | No | false |
| `scale_encryption_vsi_osimage_name` | Name of the image that you would like to use to create the GKLM server for encryption. The solution supports only a RHEL 8.6 stock image. | No | `hpcc-scale-gklm-v-4-1-1-7` |
| `scale_encryption_vsi_profile` | Specify the virtual server instance profile type name used to create the storage nodes. For more information, see [Instance profiles](/docs/vpc?topic=vpc-profiles). | Yes, if `scale_encryption_enabled` is set to "true". | bx2-2x8 |
| `scale_encryption_server_count` | The number of highly available encryption servers that are set up. You can choose between a minimum of two servers and a maximum of five servers. | Yes, if `scale_encryption_enabled` is set to "true". | 2 |
| `scale_encryption_admin_password` | The admin password for the GKLM application, which needs to be configured by using specific guidelines and [policies](https://www.ibm.com/docs/en/sgklm/4.1.1?topic=manager-password-policy){: external}. | Yes, if `scale_encryption_enabled` is set to "true". | None |
| `scale_encryption_dns_domain` | {{site.data.keyword.dns_full_notm}} domain name to be used for the GKLM cluster. | No | gklmscale.com |
| `scale_encryption_instance_key_pair` | Name of the SSH key configured in your {{site.data.keyword.cloud_notm}} account that is used to establish a connection to the encrypted Scale key server nodes. Make sure that the SSH key is present in the same resource group and region where the key servers are provisioned. The solution supports only one SSH key that can be attached to key server nodes. If you do not have an SSH key in your {{site.data.keyword.cloud_notm}} account, create one by using the [SSH keys](/docs/vpc?topic=vpc-ssh-keys) instructions. | No | None |
| `storage_bare_metal_osimage_name` | Name of the image that you would like to use to create the storage cluster nodes for the {{site.data.keyword.scale_short}} cluster. The solution supports only a RHEL 8.6 stock image. | No | ibm-redhat-8-6-minimal-amd64-5 |
| `storage_bare_metal_server_profile` | Specify the bare metal server profile type name to be used to create the bare metal storage nodes. For more information, see [Bare metal server profiles](/docs/vpc?topic=vpc-bare-metal-servers-profile). | No | cx2d-metal-96x192 |
| `storage_cluster_filesystem_mountpoint` | {{site.data.keyword.scale_short}} storage cluster (owningCluster) file system mount point. The owningCluster is the cluster that owns and serves the file system to be mounted. For more information, see [Mounting a remote GPFS file system](https://www.ibm.com/docs/en/spectrum-scale/5.1.4?topic=system-mounting-remote-gpfs-file){: external}. | No | `/gpfs/fs1` |
| `storage_cluster_gui_password` | Password that is used for logging in to the storage cluster through the GUI. The password should contain a minimum of 8 characters. For a strong password, use a combination of uppercase and lowercase letters, one number, and a special character. Make sure that the password doesn't contain the username. | Yes | None |
| `storage_cluster_gui_username` | GUI username to perform system management and monitoring tasks on the storage cluster. The username should be at least four characters (any combination of lowercase and uppercase letters). | Yes | None |
| `storage_cluster_key_pair` | Name of the SSH key configured in your {{site.data.keyword.cloud_notm}} account that is used to establish a connection to the storage cluster nodes. Make sure that the SSH key is present in the same resource group and region where the cluster is provisioned. The solution supports only one SSH key that can be attached to the storage nodes. If you do not have an SSH key in your {{site.data.keyword.cloud_notm}} account, create one by using the [SSH keys](/docs/vpc?topic=vpc-ssh-keys) instructions. | Yes | None |
| `storage_type` | Select the {{site.data.keyword.scale_short}} file system deployment method. **Note:** The {{site.data.keyword.scale_short}} scratch and evaluation types deploy the {{site.data.keyword.scale_short}} file system on virtual server instances, and the persistent type deploys the {{site.data.keyword.scale_short}} file system on bare metal servers. | No | scratch |
| `storage_vsi_osimage_name` | Name of the custom image that is used to create the storage cluster nodes for the {{site.data.keyword.scale_short}} cluster. The solution supports both stock and custom  images that use RHEL 8.6 and have the appropriate {{site.data.keyword.scale_short}} functionality. If you'd like, you can follow the instructions for [Planning for custom images](/docs/vpc?topic=vpc-planning-custom-images) to create your own custom image. | No | hpcc-scale5181-rhel86 | 
| `storage_vsi_profile` | Specify the virtual server instance profile type name used to create the storage nodes. For more information, see [Instance profiles](/docs/vpc?topic=vpc-profiles). | No | bx2d-32x128 |
| `total_compute_cluster_instances` | Total number of compute cluster instances that you need to provision. A minimum of three nodes and a maximum of 64 nodes are supported. A count of 0 can be defined when no compute nodes are required. | No | 3 |
| `total_storage_cluster_instances` | Total number of storage cluster instances that you need to provision. A minimum of three nodes and a maximum of 18 nodes are supported if the storage type selected is scratch.  A minimum of three nodes and a maximum of 10 nodes are supported if the storage type selected is persistent. | No | 4 |
| `vpc_availability_zones` | {{site.data.keyword.cloud_notm}} availability zone names within the selected region where the {{site.data.keyword.scale_short}} cluster should be deployed. For the current release, the solution supports only a single availability zone. For more information, see [Region and data center locations for resource deployment](/docs/overview?topic=overview-locations). | Yes | None |
| `vpc_cidr_block` | {{site.data.keyword.vpc_short}} address prefixes that are needed for VPC creation. Since the solution supports only a single availability zone, provide one CIDR address prefix for VPC creation. For more information, see [Bring your own subnet](/docs/vpc?topic=vpc-configuring-address-prefixes). | No | 10.241.0.0/18 |
| `vpc_compute_cluster_dns_domain` | {{site.data.keyword.dns_full_notm}} domain name to be used for the compute cluster. | No | compscale.com |
| `vpc_compute_cluster_private_subnets_cidr_blocks` | The CIDR block that's required for the creation of the compute cluster private subnet. Modify the CIDR block if it has already been reserved or used for other applications within the VPC or conflicts with any on-premises CIDR blocks when using a hybrid environment. Provide only one CIDR block for the creation of the compute subnet. | No | 10.241.0.0/24 |
| `vpc_region` | Name of the {{site.data.keyword.cloud_notm}} region where the resources need to be provisioned (for example, us-east, us-south). For more information, see [Region and data center locations for resource deployment](/docs/overview?topic=overview-locations). | Yes | None |
| `vpc_storage_cluster_dns_domain` | {{site.data.keyword.dns_full_notm}} domain name to be used for the storage cluster. | No | strgscale.com |
| `vpc_storage_cluster_private_subnets_cidr_blocks` | The CIDR block that's required for the creation of the storage cluster private subnet. Modify the CIDR block if it has already been reserved or used for other applications within the VPC or conflicts with any on-premises CIDR blocks when using a hybrid environment. Provide only one CIDR block for the creation of the storage subnet. | No | 10.241.1.0/24 |
{: caption="Table 1. Deployment values" caption-side="top"}