---

copyright:
  years: 2025
lastupdated: "2025-03-14"

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

# Storage Scale Security Groups
{: #security-groups}

The {{site.data.keyword.scale_full_notm}} deployment currently supports the creation of new security groups through the automation process. This automated setup provisions distinct security groups customized for different node types such as bastion, storage, and bootstrap security group. Additionally, when optional features are enabled, the automation creates specific security groups: ldap for LDAP integration, gklm for Guardium Key Lifecycle Manager (GKLM), and comp for Compute Nodes.

Each security group is dedicated to a specific set of scale nodes, ensuring that only the necessary ports, sources, and destinations are enabled for those nodes. This approach minimizes exposure, enhances security, and reduces the risk of misconfigurations.

When using the Storage Scale automation, the required security groups and their associated rules are automatically created and applied. However, if users prefer to utilize pre-existing security groups, they must ensure that at least three default security groups `bastion_sg`, `storage_sg`, and `bootstrap_sg` are provided for the clusters core functionality. Depending on the optional features enabled, the respective additional security groups (`ldap_sg`, `gklm_sg`, and `comp_sg`) must also be provided.

For best practices, it is recommended to use separate and dedicated security groups for each respective scale node type. This approach ensures consistent security policies across all resources and simplifies management by reducing the risk of policy conflicts and misaligned configurations.

When users opt for the existing security groups, they must ensure that all necessary groups are specified. Failure to provide the required security groups will result in automation failure.

**Scenario 1**

If the variable `enable_sg_validation` is set to `true` and the user provides only the `bastion_sg` and `bootstrap_sg`, omitting the `storage_sg`, then the terraform automation fails due to built-in validations designed to ensure all mandatory security groups are passed for cluster creation.

```console
`2025/03/12 10:30:01 Terraform plan | on input-validations.tf line 537, in locals: 2025/03/12 10:30:01 Terraform plan | 537: validate_strg_sg_chk = regex("^${local.strg_sg_msg}$", local.validate_strg_sg ? local.strg_sg_msg : "") 2025/03/12 10:30:01 Terraform plan | ├──────────────── 2025/03/12 10:30:01 Terraform plan | │ while calling regex(pattern, string) 2025/03/12 10:30:01 Terraform plan | │ local.strg_sg_msg is "Only new or existing security groups are supported. If any security group name is not null, then strg_sg_name must be an existing security group." 2025/03/12 10:30:01 Terraform plan | │ local.validate_strg_sg is false 2025/03/12 10:30:01 Terraform plan | 2025/03/12 10:30:01 Terraform plan | Call to function "regex" failed: pattern did not match any part of the given 2025/03/12 10:30:01 Terraform plan`
```
{: pre}

**Scenario 2**

If the variable `enable_sg_validation` is set to `false`, then the automation bypasses these validations. This allows the deployment to proceed without all security groups in place, it can lead to incomplete configurations and improper cluster functionality due to missing security group associations.

**Scenario 3**

If the variable `enable_sg_validation` is set to `true`, then the automation is capable of validating security group rules. For example, the `storage_sg` variable must include entries allowing communication from all other security groups associated with the deployment. If any of these entries are missing, then the deployment fails.

On the other hand, when `enable_sg_validation` is set to `false`, then the validations are skipped increasing the risk of misconfigurations that could prevent the cluster from operating as expected.

![Security Group Rules](images/security-group-rules.png){: caption="Security Group Inbound and Outbound rules" caption-side="bottom"}

You must either use new security groups created by the automation or depend on the pre-existing ones. A mixed approach combining both new and existing security groups is not supported and can lead to inconsistent configurations and potential security vulnerabilities.
{: important}

## Security Group Validation
{: #security-group-validation}

Security group validation ensures that the specified security groups are properly assigned. The validation is controlled by the `enable_sg_validation` variable.

* If `enable_sg_validation` is set to true, the deployment will validate whether the appropriate security groups are provided.

* If `enable_sg_validation` is set to false, no validation will be performed and the deployment will proceed without checking the security groups.

| Variable	|Description	| Value |
|----------|----------|----------|
|`enable_sg_validation`| If `enable_sg_validation` is set to true, the deployment confirms that the correct security groups are attached and allows the appropriate rules. When set to false, no validation is performed, and the deployment proceeds without verifying the security groups. | Null |
|`strg_sg_name`| Provide the security group name to provision the storage nodes. If set to `null`, the solution automatically creates the necessary security group and rules. If you choose to use an existing security group, ensure it has the appropriate rules configured for the storage nodes to function properly. | Null |
|`comp_sg_name`| Provide the security group name to provision the compute nodes. If set to `null`, the solution will automatically create the necessary security group and rules. If you choose to use an existing security group, ensure it has the appropriate rules configured for the compute nodes to function properly. | Null |
|`gklm_sg_name`| Provide the security group name to provision the gklm nodes. If set to `null`, the solution will automatically create the necessary security group and rules. If you choose to use an existing security group, ensure it has the appropriate rules configured for the gklm nodes to function properly. | Null |
|`ldap_sg_name`| Provide the security group name to provision the ldap nodes. If set to `null`, the solution will automatically create the necessary security group and rules. If you choose to use an existing security group, ensure it has the appropriate rules configured for the ldap nodes to function properly. | Null |
|`bastion_sg_name`| Provide the security group name to provision the bastion node. If set to `null`, the solution will automatically create the necessary security group and rules. If you choose to use an existing security group, ensure it has the appropriate rules configured for the bastion node to function properly. | Null |
|`bootstrap_sg_name`| Provide the security group name to provision the bootstrap node. If set to `null`, the solution will automatically create the necessary security group and rules. If you choose to use an existing security group, ensure it has the appropriate rules configured for the bootstrap node to function properly. | Null |
