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

# Accessing the GUI
{: #access-gui}

After the cluster setup is done, you can monitor the resources and status of the service directly from the {{site.data.keyword.scale_full_notm}} GUI for both the compute and storage clusters. For more information about the GUI, see [{{site.data.keyword.scale_full_notm}} GUI](https://www.ibm.com/docs/en/spectrum-scale/5.1.3?topic=reference-spectrum-scale-gui){: external}.
{: shortdesc}

## Before you begin
{: #before-you-begin}

Before you begin accessing the {{site.data.keyword.scale_short}} GUI, review the following considerations and requirements:

* The initial setup must be done from your local machine.
* Provide the SSH key path from the local machine that's used to configure the compute and storage nodes.
* It's recommended to use the Safari browser to access the GUI.
* If you encounter slowness in loading or accessing the GUI, clear the browser's cache.
* You cannot open both the compute and storage GUIs on the same port 22443. Use a different port or close one of the GUIs so you can access the other GUI cluster.

## Fetching `GUI_node_IP`
{: #fetching-gui-node-IP}

1. Log in to the individual Scale clusters (either compute or storage).
2. Run the following command:

    ```
    mmlsnodeclass GUI_MGMT_SERVERS
    ```
    {: pre}

    **Example output:**
    ```
    [root@scale-comp-001 ~]# mmlsnodeclass GUI_MGMT_SERVERS
    Node Class Name    Members
    -----------------  --------------------------------
    GUI_MGMT_SERVERS   scale-comp-001.scl.com
    ```
    {: screen}

3. All of the Scale instances that are listed in the "Members" column are running the Scale GUI service.

Usually, the first instance in the individual Scale clusters is running the the Scale GUI service.
{: note}

## Setting up access
{: #setting-up-access}

1. Open a new command line terminal.
2. Run the following commands from the local machine:

    ```
    eval `ssh-agent`
    ssh-add -k <id_rsa private key of a specific cluster (compute/storage)>
    ssh -A -L 22443:<GUI_node_IP>:443 -N ubuntu@<bastion_host_IP>
    ```
    {: codeblock}

    Provide the `id_rsa` key for the SSH key that is passed from either the `compute_cluster_key_pair` or `storage_cluster_key_pair` deployment value. For more information, see [Deployment values](/docs/storage-scale?topic=storage-scale-deployment-values).
    {: note}

3. Open the browser on the local machine, and run https://localhost:22443. 



