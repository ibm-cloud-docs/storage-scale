---

copyright:
  years: 2022, 2023
lastupdated: "2023-09-05"

keywords: 

subcollection: storage-scale

---

{:support: data-reuse='support'}
{:tsSymptoms: .tsSymptoms}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:troubleshoot: data-hd-content-type='troubleshoot'}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:external: target="_blank" .external}
{:pre: .pre}
{:tip: .tip}
{:note: .note}
{:important: .important}

# Troubleshooting
{: #troubleshooting-spectrum-scale}

## Why is IBM Cloud Schematics not able to clone the public GitHub repo?
{: #troubleshoot-topic-2}
{: troubleshoot}
{: support}

Schematics isn't able to clone the public GitHub repository, and you are seeing one of the following error messages:

* `Fatal, could not download repo, Failed to clone git repository, authentication required (or the git url is incorrect). Problems found with the Repository. Please Rectify and Retry`
* `Template error: Failed to clone git repository, authentication required (or the git url is incorrect)`
{: tsSymptoms}

You didn't provide the correct GitHub URL, or you provided a GitHub token, which is not required to clone a public repo. A GitHub access token is only required to access a private repo.
{: tsCauses}

Do not provide a GitHub token, and check to see whether the GitHub token was provided in the `github_token` parameter while creating a workspace by using the [public repo](https://github.com/IBM/ibm-spectrum-scale-ibm-cloud-schematics){: external}.
{: tsResolve}

## Why is IBM Cloud Schematics not able to create a workspace?
{: #troubleshoot-topic-3}
{: troubleshoot}
{: support}

Schematics isn't able to create a workspace, and you are seeing the following error message: `You don't have the required to create a workspace in any resource groups. You must be assigned the manager role on the Schematics service in at least one resource group. Contact your account administrator for access.`
{: tsSymptoms}

You don't have the required access to create a workspace in any resource groups. You must be assigned the manager role on the Schematics service for the resource group where you want to deploy the cluster resources.
{: tsCauses}

Contact your account administrator and get assigned with the manager role on the Schematics service for the resource group where you want to deploy the cluster resources.
{: tsResolve}

## Why is IBM Cloud Schematics not able to provision the cluster and fails with an authorization error?
{: #troubleshoot-topic-4}
{: troubleshoot}
{: support}

Schematics isn't able to provision the cluster, and you are seeing the following error message: `Request is not authorized. Check your user permissions and authorizations and try again.`
{: tsSymptoms}

You don't have the required access to get any VPC resources provisioned. 
{: tsCauses}

Contact your account administrator and get the required access permissions. For more information, see [Required permissions](/docs/vpc?topic=vpc-resource-authorizations-required-for-api-and-cli-calls).
{: tsResolve}

## Why is IBM Cloud Schematics not able to provision the cluster and fails with an error that the provided name is not unique? 
{: #troubleshoot-topic-5}
{: troubleshoot}
{: support}

Schematics isn't able to provision the cluster, and you are seeing the following example error message:
{: tsSymptoms}

```
"code": "validation_unique_failed",
"message": "Provided Name (sample-scale-vpc) is not unique",
"target": {
"name": "name",
"type": "field",
"value": "sample-scale-vpc"
}
```
{: screen}

Resource names must be unique. If a resource exists with the same name, you might get a similar error.
{: tsCauses}

Since the resource names need to be unique, check which resource is generating the error. From the UI and CLI, you can fetch  the details of that specific resource. If that resource is owned by you, update the resource name so it's unique. If the resource is owned by another user, navigate to your {{site.data.keyword.bpshort}} workspace and destroy all of the resources. Change the cluster prefix name, and provision the cluster again through {{site.data.keyword.bpshort}}.
{: tsResolve}

## Why is IBM Cloud Schematics not able to provision the cluster while using a custom image?
{: #troubleshoot-topic-6}
{: troubleshoot}
{: support}

While using a custom image, Schematics isn't able to provision the cluster, and you are seeing one of the following error messages:

* `The argument "image" is required, but no definition was found.`
* `Unknown variable. There is no variable named "image_id".`
{: tsSymptoms}

The custom image that is used for one of the virtual server instances isn't present in the target region and zone or it is not accessible by the account and API key that is used to provision the cluster.
{: tsCauses}

If you are using a custom image for any of your virtual server instances, ensure that the custom image is available in the target region and zone and is accessible by the account and API key that is used to provision the cluster.
{: tsResolve}

## Why am I receiving an error for my refresh token?
{: #troubleshoot-topic-7}
{: troubleshoot}
{: support}

You are receiving a refresh token error in the **generate a plan**, **apply a plan**, and **destroy resources** requests: `Error: The provided Refresh Token is invalid. Please provide a proper refresh token for Terraform to run the configuration. Code: 400`
{: tsSymptoms}

You didn't provide the correct refresh token, or you didn't provide a refresh token altogether.
{: tsCauses}

Check to see whether the refresh token that was generated by using the curl command is correct; otherwise, regenerate the refresh token.
{: tsResolve}

## Why am I receiving an error when I apply a change to my workspace?
{: #troubleshoot-topic-8}
{: troubleshoot}
{: support}

You are receiving the following error when you try to apply a change to your workspace: `Apply failed due to "Error: Error Deleting Volume : The volume is still attached to an instance."`
{: tsSymptoms}

After reconfiguring the volume profile, capacity, or IOPS, your workspace needs to be cleaned up before you apply the change. 
{: tsCauses}

You need to destroy your existing resources and try applying the change again. Your data on the storage node will be deleted if you destroy your existing resources.
{: tsResolve}

## Why am I receiving an error with the resource group when I generate or apply a plan?
{: #troubleshoot-topic-9}
{: troubleshoot}
{: support}

You are receiving the following error when you try to either generate or apply a plan to your workspace: `Apply failed due to "[ERROR] Given Resource Group is not found in the account %!!(MISSING)s()."`
{: tsSymptoms}

Either during generating or applying a plan, Terraform tries to validate if all of the basic input parameters that are provided to configure the resources are available. During the process, Terraform checks whether the name for the resource group is valid and available on {{site.data.keyword.cloud_notm}}.
{: tsCauses}

You need to check whether the provided resource group name is available in the specific account where the deployment will be. You can also check to see whether there are any spaces included in the resource group name and validate if the name is case-sensitive.
{: tsResolve}

## Why am I receiving an error that the image is not found?
{: #troubleshoot-topic-10}
{: troubleshoot}
{: support}

You are receiving the following error when you try to either generate or apply a plan to your workspace: `Apply failed due to "Error: [ERROR] No image found with name hpcc-spectrumscalecontroller513-06may2021-rhel84-v6."`
{: tsSymptoms}

Either during generating or applying a plan, Terraform tries to validate if the provide image name and its image ID is present in the `image_map.tf` file. If Terraform finds the correct image details, it provisions the instances, but if the correct image details can't be found, Terraform tries to fetch the image details from {{site.data.keyword.cloud_notm}} through `data_source`. 

Even if the provided image is not present in the cloud from that specific region, you still might receive the error.
{: tsCauses}

You need to check whether the provided image name has any spaces and if that image is present in the region where you want to do the deployment.
{: tsResolve}

## Why am I receiving a `cannot_start_capacity` error?
{: #troubleshoot-topic-11}
{: troubleshoot}
{: support}

You are receiving the following error when you try to apply a plan to your workspace: `Apply failed due to "code : cannot_start_capacity : message : Can't start instance because resource capacity is unavailable."`
{: tsSymtoms}

During the apply plan process, Terraform initiates the virtual server instance provisioning process based on the selected deployment values. If there is a resource capacity issue or a quota issue from the region where you are trying to deploy, the resources won't provision as expected.
{: tsCauses}

You need to talk to the administrator of the account to increase the quota for the specific region, or you can try to clean up all of the unwanted resources that are associated with the cloud infrastructure. If you clean up unwanted resources, you might free up space for the deployment to process.
{: tsResolve}

## Why does the cluster fail with an IBM Customer Number error?
{: #troubleshoot-topic-12}
{: troubleshoot}
{: support}

You are receiving the following error when you try to apply a plan to your workspace: `Apply failed due to "ERROR - [CLOUD-DEPLOY] Provided IBM Customer Number is not entitled to use Storage Scale on Cloud. Kindly contact IBM Support Team. Exiting!"`
{: tsSymptoms}

During the apply plan process, the bootstrap node initiates provisioning the resources for storage and compute cluster creation. During the process, rpm- and gpfs-related packages need to be decrypted through the Bring-Your-Own-License concept. If the {{site.data.keyword.IBM_notm}} Customer Number is valid, the deployment begins. If not, the automation errors out the deployment.
{: tsCauses}

You need to provide a valid {{site.data.keyword.IBM_notm}} Customer Number that is entitled to {{site.data.keyword.scale_short}} without any spaces in the number. If the value that you provided is valid and you still received this error, contact {{site.data.keyword.IBM_notm}} support to clarify about the entitlement.
{: tsResolve}

## Why does the SSH connection fail and I can't connect to the nodes?
{: #troubleshoot-topic-13}
{: troubleshoot}
{: support}

You aren't able to SSH to the nodes from a local machine after a successful cluster deployment.
{: tsSymptoms}

After a successful cluster deployment, you won't be able to SSH to the nodes due to the following issues:
1. SSH keys were not properly installed on the virtual server instance during the automation process.
2. The wrong SSH key names were used. For example, SSH key names that aren't owned by the individual trying to establish SSH connection were used.
3. The security group doesn't have the appropriate source range.
{: tsCauses}

You can try the following procedures to help troubleshoot the SSH issue:
1. Ensure that you have the correct IP address to establish SSH connection. Refresh the UI to fetch the latest IP address details.
2. Check whether the SSH connection works for the bastion host (for example, `ssh ubuntu@<bastion-IP-address>). If the connection is successful, then you can troubleshoot the SSH issue for the other nodes.
3. Open the security group of the bastion host and check if TCP port 22 with source range is open from the user machine.
4. Use https://ipv4.icanhazip.com/ to fetch the current IP address and validate whether there is a different, updated IP address on the security group of the source address range.
5. Open the security group of the bootstrap, compute, and storage nodes to see the bastion node security group source details to access SSH to connect to the other nodes.
6. Make sure that the public SSH key that's used in the region matches the `id_rsa.pub` content from the local machine. Use the commands `cd .ssh` and `cat id_rsa.pub` to check.
7. Ensure that there are no duplicate `id_rsa.pub` files present in the `.ssh` folder in the local machine.
{: tsResolve}

## What is the `subnet_not_in_address_prefix` error or invalid CIDR format error during the VPC and subnet creation?
{: #troubleshoot-topic-14}
{: troubleshoot}
{: support}

You are receiving the following error when you try to apply a plan to your workspace: `Apply failed due to Error: [ERROR] Error while creating subnet. The specified CIDR does not fit in any of the address prefixes in the specified VPC. Make sure the subnet's CIDR is a subset of the CIDR of one of the address prefixes.`
{: tsSymptoms}

During the apply plan process, the workspace tries to create the VPC and subnet with the specified range of the CIDR address prefix from the deployment value. If the address prefix range is out of scope or doesn't belong to the family of the IP address range of the VPC, then you get an error that the address is not in range.
{: tsCauses}

Validate if the address prefix range that's provided for subnet creation is from the same range of addresses that are used for the VPC. For example, if the VPC address prefix is 10.241.0.0/18, then the subnet should be in the 10.241.x.x range. If a different IP address range is used, then you need to divide the [subnets](https://www.davidc.net/sites/default/subnets/subnets.html?network=10.23.124.128&mask=25&division=11.721){: external} and choose the IP address range that's required for subnet creation.
{: tsResolve}

## Why does my instance provisioning remain in `Starting` state?
{: #troubleshoot-topic-15}
{: troubleshoot}
{: support}

After you apply a plan, the workspace takes a long time to provision the virtual server instance, and you notice in the UI that the virtual server instance remains in `Starting` state.
{: tsSymptoms}

During the apply plan process, Terraform initiates the provisioning process of the virtual server instance in the cloud infrastructure. If there is a capacity issue or any issue from the infrastructure side for that particular region and zone, you might see this issue.
{: tsCauses}

You can try to manually create an instance in the UI with the same image that is used during the automation process to see whether you get the same issue, or you can try a different zone for the deployment. You can also raise a support request for this issue to see whether it's originating from the infrastructure side.
{: tsResolve}

## Why do I get an error that I'm not authorized to view the instance?
{: #troubleshoot-topic-16}
{: troubleshoot}
{: support}

You are receiving the following errors when you try to apply a plan to your workspace: 

* `Apply failed due to error fetching keys. The provided token is not authorized to list keys in this account`
* `Error: the provided token is not authorized to view the specified instance (ID:*) in this account`
{: tsSymptoms}

During the apply plan process, the {{site.data.keyword.scale_short}} automation is integrated with trusted profiles, which is used for authorization purposes to provide permissions for the bootstrap node to create compute resources.
{: tsCauses}

Contact your administrator to provide the required set of permissions on the trusted profile for the deployment to happen. For more support, you can raise a request with {{site.data.keyword.IBM_notm}} support.
{: tsResolve}

## Why does the health of the PERFMON show a failed state?
{: #troubleshoot-topic-17}
{: troubleshoot}
{: support}

After the cluster setup is done and you run the command `mmhealth node show` or `mmhealth cluster show` to validate the health of the node, the PERFMON node is in a failed state.
{: tsSymptoms}

The PERFMON node makes use of one of the `pmsensors` for the setup, and the `pmsensors` service might not come up as expected.
{: tsCauses}

Run the following commands to see the specific logs to fix the issue:
{: tsResolve}

1. Run the following command to check the status of `pmsensors`:

    ```
    systemctl status pmsensors
    ```
    {: pre}

    **Sample Response**

    ```
    pmsensors.service - zimon sensor daemon
    Loaded: loaded (/usr/lib/systemd/system/pmsensors.service; enabled; vendor preset: disabled)
    Active: failed (Result: start-limit) since Wed 2022-05-11 11:13:44 UTC; 2h 5min ago
    Main PID: 19206 (code=exited, status=78)
    May 11 11:13:44 anbu-scale-r4-compute-1 systemd[1]: pmsensors.service failed.
    ```
    {: screen}

2. Run the following command to restart the `pmsensors` service:

    ```
    systemctl restart pmsensors
    ```
    {: pre}

The logs can be checked at `cd /var/adm/ras` and `cat mmsysmonitor.log`.

## Why does my cluster deployment fail in Schematics with a `signal KILL` error?
{: #troubleshoot-topic-18}
{: troublshoot}
{: support}

While {{site.data.keyword.bpshort}} sets up the cluster, the deployment fails with an error similar to: `Error: error executing "/tmp/terraform_516879781.sh": Process exited with status 137 from signal KILL`
{: tsSymptoms}

Cluster provisioning is a two-phase process. In the first phase, {{site.data.keyword.bpshort}} deploys some initial resources and the bastion host. In the second phase, {{site.data.keyword.bpshort}} uses SSH for remote access from the bastion host to the bootstrap node and then starts the subsequent resource provisioning. During the second phase, if any resource provisioning takes too long, {{site.data.keyword.bpshort}} stops the SSH process, which results in the `signal KILL` error. This can happen if bare metal provisioning takes longer than 40 minutes. 

Since {{site.data.keyword.bpshort}} is a free service, there is a time limit of one hour on remote execution. If the one hour time limit elapses, {{site.data.keyword.bpshort}} automatically stops the deployment and returns a `signal KILL` error.
{: tsCauses}

The only way to fix this issue is by cleaning up all of the resources from the failed deployment and then trying to do a new deployment.
{: tsResolve}

## Why does my cluster deployment fail with a "unique name" error?
{: #troubleshoot-topic-19}
{: troubleshoot}
{: support}

During cluster provisioning, the deployment fails with the following error: `The provided name is not unique name: <bare_metal_server_name>`.
{: tsSymptoms}

In the cloud environment, all of the resources should be uniquely named. During cluster provisioning, all resource names are stored in a backend database. If a cluster provisioning attempt fails and you try to clean up resources from this failed attempt, it can take a while for the resource names to be cleaned up from the backend database. If during a subsequent reprovisioning attempt the same cluster prefix is used, it's possible that the new resources' names collide with the stale entries from the previous attempt.
{: tsCcauses}

After a failed deployment, clean up all resources. During a subsequent attempt, use a new cluster prefix to avoid any name collisions with resources from the previous failed attempt. 
{: tsResolve}

## Why does my cluster fail with an "API Marketplace" check error?
{: #troublehsoot-topic-20}
{: troubleshoot}
{: support}

During cluster provisioning, the deployment fails with the following errors:
* `ERROR - IBM Marketplace API error: An error occurred processing the request`
* `ERROR - IBM Marketplace API error: Internal Server Error`
{: tsSymptoms}

The {{site.data.keyword.scale_short}} solution is based on a "Bring Your Own License" model. During cluster provisioning, there is a check to ensure that you are entitled to use the {{site.data.keyword.scale_short}} software.

To validate the software entitlement, the automation code uses a Marketplace API URL to check whether the provided IBM Customer Number (ICN) has entitlement to the {{site.data.keyword.scale_short}} software part numbers. It's possible that the Marketplace API service is experiencing a temporary issue and is not able to service the request.
{: tsCauses}

Open an issue with {{site.data.keyword.cloud_notm}} Support. This needs to be reported to the specific API team that provides this capability. Ask the IBM customer support team to pass this issue to the Marketplace API team.
{: tsResolve}

## Why am I getting errors when I try to delete resources after a bare metal server fails to provision?
{: #troubleshooting-topic-21}
{: troubleshoot}
{: support}

After a bare metal server fails to provision and you try to delete resources from the bootstrap node, you are receiving the following error after you apply the `mmcloudworkflows cluster destroy ibmcloud` command: `[ERROR] Error deleting security group target binding while deleting security group : The specified network interface is not attached to any other security groups..`
{: tsSymptoms}

If cluster provisioning fails, it's recommended to clean up all of the resources from the failed provision before you attempt to reprovision a cluster.

During the destroy process, the bootstrap node tries to clean up all of the resources that were created during the provisioning phase (for example, compute node, bare metal server, security group, and so on). It's possible that if a bare metal server provisioning took longer than expected and then failed, then during a subsequent cleanup, the destroy process complains that the failed bare metal server is still attached to a security group it is attempting to destroy.
{: tsCauses}

Copy the cluster prefix name and complete the following steps:
{: tsResolve}

1. Go to the security group and access the `-storage-sg`.
2. Go to the attached resources section for the security group.
3. Click the attached bare metal server and copy the ID of the server.
4. Run the following commands from the CLI to stop and delete the server:

    ```
    ibmcloud is bare-metal-server-stop $bare_metal_server_id
    ```
    {: pre}

    ```
    ibmcloud is bare-metal-server-delete $bare_metal_server_id
    ```
    {: pre}

5. Deletion of the bare metal server takes a few seconds. After the bare metal node is deleted, access the bootstrap node and reapply the `mmcloudworkflows cluster destroy ibmcloud` command.

## Why is IBM Cloud Schematics not able to provision the cluster and fails with a passwordless SSH error?
{: #troubleshoot-topic-22}
{: troubleshoot}
{: support}

After the bootstrap node creates all the resources, the solution triggers the Ansible code to configure the entire Scale configuration on storage bare metal servers. During the Ansible configuration, the following error occurs: `[ERROR] Check passwordless SSH on all scale inventory hosts (1 retries left)`
{: tsSymptoms}

After all the infrastructure-related resources are up and running, the Ansible code tries to perform the Scale configuration through a passwordless SSH method. During this process, on the storage bare metal server, if the SSH service is not in a running state, then Ansible can't SSH to that specific bare metal storage node and it fails with the error.
{: tsCauses}

After a failed deployment, clean up all resources. During a subsequent attempt, use a new cluster prefix to avoid any name collisions with resources from the previous failed attempt. If the issue continues to occur, open an issue with {{site.data.keyword.cloud_notm}} Support.
{: tsResolve}

## Why is IBM Cloud Schematics not able to provision the cluster and fails with an `Enabling the Custom resolver` error?
{: #troubleshoot-topic-23}
{: troubleshoot}
{: support}

While {{site.data.keyword.bpshort}} tries to create the VPC resources, it attempts to create a custom resolver, but fails with this error: `[ERROR] Error Enabling the Custom resolver : MaxTimeout`
{: tsSymptoms}

Terraform attempts to create the custom resolver environment and waits for the custom resolver state to reach active state. During this process, if the custom resolver takes more time than expected, Terraform throws the error message.
{: tsCauses}

After a failed deployment, clean up all resources. During a subsequent attempt, use a new cluster prefix to avoid any name collisions with resources from the previous failed attempt. If the issue continues to occur, open an issue with {{site.data.keyword.cloud_notm}} Support.
{: tsResolve}



