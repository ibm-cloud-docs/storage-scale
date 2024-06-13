---

copyright:
  years: 2022, 2023
lastupdated: "2023-09-05"

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
{:ui: .ph data-hd-interface='ui'}
{:cli: .ph data-hd-interface='cli'}
{:api: .ph data-hd-interface='api'}
{:table: .aria-labeledby="caption"}

# Applying a plan
{: #applying-plan}

When you apply a plan, the {{site.data.keyword.cloud}} resources are deployed to build a {{site.data.keyword.scale_short}} cluster instance on your {{site.data.keyword.cloud_notm}} account with your specific choice of configuration properties. 

## Applying a plan using the UI
{: #apply-plan-ui}
{: ui}

1. After you generate a plan in the {{site.data.keyword.cloud_notm}} console, click **Apply plan**. This action generates a new log that can be viewed in the _Jobs_ tab.
2. Review the log file for any errors, fix the errors, and then click **Apply plan** again.
3. After you successfully apply a plan, you can review all of the resources that are deployed under this workspace by clicking the _Resources_ tab.

For more information on viewing successful or failed cluster deployments, see [Retrieving apply plan logs](/docs/storage-scale?topic=storage-scale-retrieve-apply-plan-logs).

## Next steps
{: #apply-plan-ui-next-steps}
{: ui}

After you've successfully applied a plan, you can begin [Accessing the GUI](/docs/storage-scale?topic=storage-scale-access-gui) to monitor the resources and status of the service for both the compute and storage clusters.

## Applying a plan using the CLI
{: #apply-plan-cli}
{: cli}

Run the following command to apply a plan to your workspace:

```
ibmcloud schematics apply --id <WORKSPACE_ID>
```
{: pre}

While the resources are being deployed, you can monitor the log files for errors or confirm that the action was completed successfully. This step can take some time based on how many virtual servers are being provisioned as part of your configuration file. Run the following command to monitor your log files:

```
ibmcloud schematics logs --id <WORKSPACE_ID>
```
{: pre}

**Sample response:**

```
2022/05/09 14:35:53 Terraform apply | Apply complete! Resources: 41 added, 0 changed, 0 destroyed.
2022/05/09 14:35:53 Terraform apply |
2022/05/09 14:35:53 Terraform apply | Outputs:
2022/05/09 14:35:53 Terraform apply |
2022/05/09 14:35:53 Terraform apply | ssh_command = "ssh -J ubuntu@141.125.161.0 vpcuser@10.241.1.5"
2022/05/09 14:35:53 Command finished successfully.
```
{: screen}

After the plan is successfully applied, it generates an `ssh_command` in the _Outputs_ section in the Terraform code. You can use this command to log in to the {{site.data.keyword.scale_short}} cluster. Only systems with the specified IP addresses from the `remote_cidr_blocks` deployment value will be able to access the cluster nodes by using the SSH command.

For more information on viewing successful or failed cluster deployments, see [Retrieving apply plan logs](/docs/storage-scale?topic=storage-scale-retrieve-apply-plan-logs).

## Next steps
{: #apply-plan-cli-next-steps}
{: cli}

After you've successfully applied a plan, you can begin [Accessing the GUI](/docs/storage-scale?topic=storage-scale-access-gui) to monitor the resources and status of the service for both the compute and storage clusters.

## Applying a plan using the API
{: #apply-plan-api}
{: api}

1. To apply a plan by using the {{site.data.keyword.bplong_notm}} Python APIs, create a Python file and provide a name of your choice, for example, `schematics_apply_plan.py`.
2. Copy and paste the [Apply a plan using {{site.data.keyword.bpshort}} Python API](/docs/storage-scale?topic=storage-scale-applying-plan&interface=api#example-request-apply) example request to your Python file.
3. Change the following parameters as part of the request:
   * Replace your {{site.data.keyword.cloud_notm}} API key to the `authenticator = IAMAuthenticator('<ibm-api-key>')` variable.
   * Change the API endpoint to the endpoint mentioned in [API endpoints](https://cloud.ibm.com/apidocs/schematics?code=python#api-endpoints){: external} according to the location that you want your {{site.data.keyword.bpshort}} workspace to reside, for example, `schematics_service.set_service_url('https://us.schematics.cloud.ibm.com')`.
4. Inside the `schematics_service.apply_workspace_command` function, provide the following parameters:
   * Provide the workspace ID that you generated in the [Creating a workspace](/docs/storage-scale?topic=storage-scale-creating-workspace&interface=api) task, for example, `us-south.workspace.Terraform-Schematics-Python-Workspace.b3bbc9f5`.
   * Export your {{site.data.keyword.cloud_notm}} API key by using the following command:
  
    ```
    export IBMCLOUD_API_KEY =”<ibm-cloud-api-key>”
    ```
    {: pre} 
    
   * Run the following curl command to create a refresh token:

    ```
    curl -X POST "https://iam.cloud.ibm.com/identity/token" -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=urn:ibm:params:oauth:grant-type:apikey&apikey=$IBMCLOUD_API_KEY" -u bx:bx
    ```
    {: pre}

5. Run the Python script by using `python3 <python-file-name>` to apply a plan in the {{site.data.keyword.cloud_notm}}.
6. You get an activity ID in response if the parameters passed as part of the request are valid. You should see the plan being applied in the {{site.data.keyword.bpshort}} workspace that you created in the {{site.data.keyword.cloud_notm}} console. If you don’t get a successful response, the error response contains the errors that you need to resolve. Resolve those errors and run the script until you are able to get a valid response and apply a plan.
7. If you want to check the logs of the action, see [Retrieving action logs with {{site.data.keyword.bpshort}} API](/docs/storage-scale?topic=storage-scale-retrieve-action-logs) to retrieve the logs. The response contains the status of the action, and it appears in either a `COMPLETED` or `FAILED` state.

For more information on viewing successful or failed cluster deployments, see [Retrieving apply plan logs](/docs/storage-scale?topic=storage-scale-retrieve-apply-plan-logs).

### Example Python request
{: #example-request-apply}
{: api}

```python

# Apply a plan using Schematics Python API

import json, logging

from ibm_cloud_sdk_core.authenticators import IAMAuthenticator
from ibm_schematics.schematics_v1 import SchematicsV1

logging.basicConfig()
logging.root.setLevel(logging.NOTSET)
logging.basicConfig(level=logging.NOTSET)

authenticator = IAMAuthenticator('<ibmcloud-api-key>')

schematics_service = SchematicsV1(authenticator = authenticator)

schematics_service.set_service_url('https://us.schematics.cloud.ibm.com')

logging.info("Started Applying Schematic Plan")

workspace_activity_apply_result = schematics_service.apply_workspace_command(
    w_id='<workspace id>',
    refresh_token='<refresh-token>'
).get_result()

print(json.dumps(workspace_activity_apply_result, indent=2))

logging.info("Completed Applying Schematic Plan")
```
{: codeblock}

### Example Python response
{: #example-response-apply}
{: api}

```python
INFO:root:Started Applying Schematic Plan
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): iam.cloud.ibm.com:443
DEBUG:urllib3.connectionpool:https://iam.cloud.ibm.com:443 "POST /identity/token HTTP/1.1" 200 985
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): us.schematics.cloud.ibm.com:443
DEBUG:urllib3.connectionpool:https://us.schematics.cloud.ibm.com:443 "PUT /v1/workspaces/us-south.workspace.Schematic-Sunil-Test-Workspace.5a4cbf11/apply HTTP/1.1" 202 49
{
  "activityid": "33bade478b4e93f5c613e4f62943ad9a"
}
INFO:root:Completed Applying Schematic Plan
```
{: screen}

## Next steps
{: #apply-plan-api-next-steps}
{: api}

After you've successfully applied a plan, you can begin [Accessing the GUI](/docs/storage-scale?topic=storage-scale-access-gui) to monitor the resources and status of the service for both the compute and storage clusters.


