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
{:step: data-tutorial-type='step'}
{:table: .aria-labeledby="caption"}

# Retrieving apply plan logs
{: #retrieve-apply-plan-logs}

You can retrieve the logs from either the Schematics workspace or the bootstrap node to view both successful and failed cluster deployments.
{: shortdesc}

## Retrieving apply plan logs in Schematics workspace
{: #retrieve-apply-plan-logs-schematics-workspace}

After you apply a plan, a new log file is generated, which can be viewed in the _Jobs_ tab in the Schematics workspace. See the following sections for instructions and examples of successful or failed deployments.

### Successful cluster deployment
{: #successful-apply-plan}

1. In the _Jobs_ tab in the Schematics workspace, select the job and expand the log file.
2. If the job was successful in creating all of the resources that are part of the deployment, then your workspace goes to an active state.
3. Use the SSH command in the output of your apply plan log to log in as `vpcuser` for the bootstrap, compute, and storage node through the bastion host as `ubuntu` user. See the following sample response of a successful deployment:

**Sample response**

```
2022/05/09 14:35:53 Terraform apply | Apply complete! Resources: 41 added, 0 changed, 0 destroyed.
2022/05/09 14:35:53 Terraform apply |
2022/05/09 14:35:53 Terraform apply | Outputs:
2022/05/09 14:35:53 Terraform apply |
2022/05/09 14:35:53 Terraform apply | ssh_command = "ssh -J ubuntu@141.125.161.0 vpcuser@10.241.1.5"
2022/05/09 14:35:53 Command finished successfully.
```
{: screen}

### Failed cluster deployment
{: #failed-apply-plan}

1. In the _Jobs_ tab in the Schematics workspace, select the job and expand the log file for a better view.
2. If the job fails to create any of the resources that are a part of the deployment, then your workspace goes to a failed state. Deployment might error out if any of the deployment values are incorrect or if there are any issues at the infrastructure level. 
3. Fix the errors, and then click **Apply plan** again. See the following sample response of a failed deployment:

**Sample response**

```
2022/05/09 12:51:12 Terraform plan | Error: [ERROR] No SSH Key found with name ssh-key-east-new
2022/05/09 12:51:12 Terraform plan |
2022/05/09 12:51:12 Terraform plan | on main.tf line 85, in data "ibm_is_ssh_key" "compute_ssh_key":
2022/05/09 12:51:12 Terraform plan | 85: data "ibm_is_ssh_key" "compute_ssh_key" { # This block is trying to fetch the key_pair details for compute node, which is dependent on the code present on public repo
2022/05/09 12:51:12 Terraform plan |
2022/05/09 12:51:12 Terraform plan |
2022/05/09 12:51:12 Terraform plan |
2022/05/09 12:51:12 Terraform plan | Error: [ERROR] No SSH Key found with name ssh-key-east-new
2022/05/09 12:51:12 Terraform plan |
2022/05/09 12:51:12 Terraform plan | on main.tf line 89, in data "ibm_is_ssh_key" "storage_ssh_key":
2022/05/09 12:51:12 Terraform plan | 89: data "ibm_is_ssh_key" "storage_ssh_key" { # This block is trying to fetch the key_pair details for compute node, which is dependent on the code present on public repo
2022/05/09 12:51:12 Terraform plan |
2022/05/09 12:51:12 Terraform plan |
2022/05/09 12:51:12 �[1m�[31mTerraform PLAN error: Terraform PLAN errorexit status 1�[39m�[0m
2022/05/09 12:51:12 �[1m�[31mCould not execute job: Error : Terraform PLAN errorexit status 1�[39m�[0m
```
{: screen}

