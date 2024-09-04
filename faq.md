---

copyright:
  years: 2022, 2024
lastupdated: "2024-06-13"

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
{:faq: data-hd-content-type='faq'}

# FAQs
{: #storage-scale-faqs}

## What locations are available for deploying the VPC resources that make up the Storage Scale cluster?
{: #locations-vpc-resources}
{: faq}

The available regions and zones for deploying VPC resources and a mapping them to city locations and data centers can be found in [Locations for resource deployment](/docs/overview?topic=overview-locations){: external}. While any of the available regions can be used, resources are provisioned only in a single availability zone within the selected region.

## What permissions are required to create a cluster that uses the offering?
{: #permissions-cluster-offering}
{: faq}

Instructions for the appropriate permissions for {{site.data.keyword.cloud_notm}} services that are used by the offering for creating a cluster can be found in Granting user permissions for VPC resources, Managing user access for Schematics, Assigning access to Secrets Manager, and [Creating trusted profiles](/docs/account?topic=account-create-trusted-profile).

## How do you SSH among nodes?
{: #ssh-among-nodes}
{: faq}

The {{site.data.keyword.scale_full_notm}} solution consists of two separate clusters (storage and compute). The SSH key parameter that is provided through {{site.data.keyword.bpshort}} (`storage_cluster_key_pair` and `compute_cluster_key_pair`) can be used to log in to the respective cluster nodes. You can log in to any node only through the bastion host by using the following command:

```shell
ssh -J ubuntu@<IP_address_bastion_host> vpcuser@<IP-address-of-nodes>
```
{: pre}

Although all the nodes of each cluster have passwordless SSH set up among them, due to security constraints, you cannot directly log in to a node from one cluster to another cluster.
{: note}

## How many compute and storage nodes can you deploy in the Storage Scale cluster through this offering?
{: #how-many-compute-storage-nodes}
{: faq}

Before you deploy a cluster, it is important to make sure that the VPC resource quota is appropriate for the size of the cluster that you would like to create (see [Quotas and service limits](/docs/vpc?topic=vpc-quotas)).

See the following minimum and maximum number of nodes that are supported in a cluster:
* Compute nodes: For all storage clusters, a minimum of 3 and a maximum of 64 virtual server instance compute nodes are supported.
* Scratch and evaluation cluster storage nodes: For a scratch and evaluation storage clusters, a minimum of 3 and a maximum of 18 virtual server instance storage nodes are supported.
* Persistent cluster storage nodes: For a persistent storage cluster, a minimum of 3 and a maximum of 10 bare metal server storage nodes are supported.

For more information, see [Deployment values](/docs/storage-scale?topic=storage-scale-deployment-values).

## What storage types are available through this offering? 
{: #storage-types-scale-offering}
{: faq}

The {{site.data.keyword.scale_short}} solution offers three different storage types: scratch, persistent, and evaluation. For more information, see [Storage types](/docs/storage-scale?topic=storage-scale-storage-types).

Parallel vNIC is not supported on the persistent storage type and it is only supported by a custom image.
{: note}

## Why are there two different resource group parameters that can be specified in the IBM Cloud catalog tile?
{: #resource-group-parameters}
{: faq}

The first resource group parameter entry in the **Configure your workspace** section in the {{site.data.keyword.cloud_notm}} catalog applies to the resource group where the {{site.data.keyword.bpshort}} workspace is provisioned on your {{site.data.keyword.cloud_notm}} account. The value for this parameter can be different than the one used for the second entry in the **Parameters with default values** section in the catalog. The second entry applies to the resource group where VPC resources are provisioned.

## Where are the Terraform files used by the IBM Storage Scale tile located?
{: #terraform-file-location}
{: faq}

The Terraform-based templates can be found in this [GitHub repository](https://github.com/IBM/ibm-spectrum-scale-ibm-cloud-schematics){: external}.

## Where can you find the custom image name to image ID mappings for each cloud region?
{: #custom-image-mappings}
{: faq}

The mappings can be found in the `image-map.tf` file in this [GitHub repository](https://github.com/IBM/ibm-spectrum-scale-ibm-cloud-schematics){: external}.

## Can you use own custom image in the Storage Scale cluster by specifying the image name in the deployment value `bootstrap_osimage_name`?
{: #bring-own-custom-image}
{: faq}

No, you can't use your own custom image for the bootstrap node currently. The bootstrap node image is configured with all of the required functions to setup the {{site.data.keyword.scale_short}} compute and storage resources.

## Can you connect directly through SSH to the bootstrap, compute, or storage nodes from a system external to IBM Cloud?
{: #connecting-nodes-external}
{: faq}

No, any SSH connection to the bootstrap, compute, or storage nodes is only possible through the bastion node for security reasons. You would use the following command to connect to your bootstrap, compute, or storage nodes (the IP address is specific to your particular node): `ssh -J ubuntu@<bastion_IP_address> vpcuser@<IP_address>`

## Can you establish an SSH connection between compute and storage nodes?
{: #establish-connection-between-nodes}
{: faq}

The compute and storage clusters are created to not have the same passwordless SSH keys. This make sure that there are separate administration domains for the compute and storage clusters; therefore, SSH between nodes from different clusters is not possible.

## Does the Storage Scale offering support multiple `key_pairs` to establish SSH to all of the nodes?
{: #multiple-key-pairs}
{: faq}

Yes, the current version of the {{site.data.keyword.scale_short}}offering supports multiple key_pair that provide access to all the nodes that are part of the cluster.

## Can you use own resource group to configure the resources?
{: #resource-group-configure-resources}
{: faq}

Yes, you can provide the resource group of your choice for the deployment of your cluster's VPC resources. Due to the use of trusted profiles in this offering, you must ensure that all the `key_pair` values that are specified in the deployment values are created in the same resource group.

## Which operating system versions are supported for the images used for the compute and storage nodes in Storage Scale?
{: #supported-operating-systems}
{: faq}

With {{site.data.keyword.scale_short}}, you can use custom or stock images based on RHEL 7.9 or RHEL 8.8. For compute nodes, both RHEL 7.9 or 8.8 can be used. For storage nodes, only RHEL 8.4 is supported.

## Why does Storage Scale not allow use of the default value of 0.0.0.0/0 for security group creation?
{: #default-value-security-group-creation}
{: faq}

For security reasons, {{site.data.keyword.scale_short}} does not allow you to provide a default value that will allow network traffic from any external device. Instead, you can provide the address of your user system (for example, by using https://ipv4.icanhazip.com/) or a multiple IP address range.

## What is an IBM Customer Number and what happens if I don't provide it?
{: #provide-icn}
{: faq}

An {{site.data.keyword.IBM_notm}} Customer Number (ICN) is the unique number that {{site.data.keyword.IBM_notm}} issues its customers during the post-contract signing process. The ICN is important because it allows {{site.data.keyword.IBM_notm}} to identify your company and support contract. Without an ICN, you can't deploy the {{site.data.keyword.scale_short}} resources through {{site.data.keyword.bplong_notm}}.

If the `storage_type` deployment value is set as either "scratch" or "persistent", the ICN can't be set as an empty value. An empty value is accepted only if the `storage_type` is set as "evaluation".
{: important}

## Can you directly destroy all Storage Scale resources from the CLI?
{: #destroy-resources-cli}
{: faq}

Do not destroy all of the resources from the CLI directly. The required resources that configure the {{site.data.keyword.scale_short}} cluster are created in two different phases of automation. To cleanly destroy a cluster, see [Destroying resources](/docs/storage-scale?topic=storage-scale-deleting-resources&interface=cli).

## What are trusted profiles and what permissions are required to set up the offering?
{: #trusted-profiles-required-permissions}
{: faq}

With {{site.data.keyword.scale_short}}, trusted profiles are used to set up granular authorization for applications that are running in compute resources. Therefore, you are not required to create or use service IDs or API keys for the creation of compute resources.

The required set of permissions to create the compute resources are already added as part of the automation code. For more information, see [Creating trusted profiles](/docs/account?topic=account-create-trusted-profile).

## Why did the destroy process fail to remove resources?
{: #failed-destroy}
{: faq}

There are a few potential reasons why the destroy process failed to remove resources:

* The input parameter from the deployment value might have been changed for some reason and {{site.data.keyword.bpshort}} is looking for the existing value. Mismatch of values might cause the destroy action to fail.
* If other resources are manually created after deployment of the VPC and subnets through the offering and those resources are associated with the same VPC, the destroy action will fail. 
* There might be issues on the {{site.data.keyword.cloud_notm}} infrastructure side that cause the destroy action to fail.

## Why are you not able to see the data on the shared file system storage after stopping the storage nodes?
{: #not-able-to-see-data}
{: faq}

The {{site.data.keyword.scale_full_notm}} file system data resides on instance storage. In general, data that is stored on instance storage is ephemeral so stopping the storage node results in data loss. However, instance storage data is not lost when an instance is rebooted. For more information, see [Lifecycle of instance storage](/docs/vpc?topic=vpc-instance-storage#instance-storage-lifecycle).

## Why do you see `xxxhiddenxxx` in the deployment output log instead of a variable name that is provided?
{: #variable-name-issue}
{: faq}

The solution provides useful information in the Terraform output log about the cluster and how to access cluster nodes (for example, SSH command, region, trusted profile ID, etc.n). 

The solution is integrated with the {{site.data.keyword.cloud_notm}} cataided there triggers {{site.data.keyword.bpshort}} to deploy the VPC resources that form the cluster. When the system is passing sensitive information such as username and password, if that value matches with data in the deployment logs, {{site.data.keyword.bpshort}} outputs the value as `xxxhiddenxxx` according to an implemented security policy.

## What version of scale supports MROT configuration?
{: #support-mrot-versions}

Anything above {{site.data.keyword.scale_full_notm}} 5.1.5 supports the Multi-Rail over TCP (MROT) feature.

## What version of OS does Scale support?
{: #os-version-supported}

With Storage Scale, you can use custom or stock images based on RHEL 7.9 or RHEL 8.8. For compute nodes, both RHEL 7.9 or 8.8 can be used. For storage nodes, only RHEL 8.8 is supported.

## Why does the 'mmlsconfg' command display 5.1.9.0 in the 'minReleaseLevel' parameter?
{: #version-command}

After running the `mmlsconfg` command, the 'minReleaseLevel' parameter displays 5.1.9.0. This is because version 5.1.9.2 includes 'minReleaseLevel' set to 5.1.9.0. For verification of the actual version, run the `mmdiag --version` command.

```
[root@scale-3-strg-001 ~]# mmdiag --version

=== mmdiag: version ===
Current GPFS build: "5.1.9.2 ".
Built on Jan 24 2024 at 13:45:43
Running 2 hours 35 minutes 24 secs, pid 17580
```
