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
{:step: data-tutorial-type='step'}
{:table: .aria-labeledby="caption"}

# Accessing the GUI
{: #access-gui}

After the cluster setup is done, you can monitor the resources and status of the service directly from the {{site.data.keyword.scale_full_notm}} GUI for both the compute and storage clusters. For more information about the GUI, see [{{site.data.keyword.scale_full_notm}} GUI](https://www.ibm.com/docs/en/spectrum-scale/5.1.3?topic=reference-spectrum-scale-gui){: external}.
{: shortdesc}

## Before you begin
{: #before-you-begin}

Before you begin accessing the {{site.data.keyword.scale_short}} GUI, review the following considerations and requirements:

* The initial setup must be done from your local system.
* Provide the SSH key path from the local system that is used to configure the compute and storage nodes.
* It is recommended to use the Safari browser to access the GUI.
* If you encounter slowness in loading or accessing the GUI, clear the browsers cache.
* You cannot open both the compute and storage GUIs on the same port 22443. Use a different port or close one of the GUIs so you can access the other GUI cluster.

## Identifying Scale Management Node
{: #identify-scale-mgmt-node}

Scale GUI daemon runs on a separate virtual server instance for each cluster (compute and storage) named as Scale Management node. This node can easily be identified with `<resource_prefix>-mgmt-001-<domain_name>`.

Currently, single Scale Management node per cluster is only supported.
{: note}

## Setting up the access
{: #setting-up-access}

This automation always uses the same IP address, so there can be issues in the `~/.ssh/known_hosts` file. If you encounter problems with this file, then remove the unwanted entries from that file and make sure to get a clean SSH, otherwise the GUI access does not work.
{: note}

### Accessing the storage GUI
{: #accessing-storage-gui}

1. Open a new command line terminal.
2. Run the following command to access the storage cluster:

  `ssh -L 21443:localhost:443 -J ubuntu@<BASTION_HOST_IP> vpcuser@<MANAGEMENT_NODE_IP_ADDRESS>`

3. Open the browser on your local system, and run https://localhost:21443. You get an SSL self-assigned certificate warning in the browser, when you access this URL for the first time.

4. Enter your login credentials that you set up when you created your workspace to access the Storage Scale GUI.

### Accessing the compute GUI
{: accessing-compute-gui}

1. Open a new command line terminal.
2. Run the following command to access the compute cluster:

  `ssh -L 21443:localhost:443 -J ubuntu@<BASTION_HOST_IP> vpcuser@<MANAGEMENT_NODE_IP_ADDRESS>`

3. Open the browser on your local system, and run https://localhost:21443. You will get an SSL self-assigned certificate warning in the browser, when you access this URL for the first time.

4. Enter your login credentials that you set up when you created your workspace to access the Compute GUI.
