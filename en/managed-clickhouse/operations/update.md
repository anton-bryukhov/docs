# Changing cluster settings

After creating a cluster, you can:

* [Change the host class](#change-resource-preset).

* [Increase the storage size](#change-disk-size) (available only for network storage, `network-hdd` and `network-ssd`).

* [Configure servers](#change-clickhouse-config){{ CH }} according to the [{{ CH }} documentation](https://clickhouse.yandex/docs/en/operations/server_settings/settings/).

## Changing the host class {#change-resource-preset}

{% list tabs %}

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  To change the [host class](../concepts/instance-types.md) for the cluster:

  1. View a description of the CLI's update cluster command:

     ```
     $ yc managed-clickhouse cluster update --help
     ```

  1. Request a list of available host classes (the `ZONES` column specifies the availability zones where you can select the appropriate class):

     ```bash
     $ yc managed-clickhouse resource-preset list
     
     +-----------+--------------------------------+-------+----------+
     |    ID     |            ZONE IDS            | CORES |  MEMORY  |
     +-----------+--------------------------------+-------+----------+
     | s1.nano   | ru-central1-a, ru-central1-b,  |     1 | 4.0 GB   |
     |           | ru-central1-c                  |       |          |
     | s1.micro  | ru-central1-a, ru-central1-b,  |     2 | 8.0 GB   |
     |           | ru-central1-c                  |       |          |
     | ...                                                           |
     +-----------+--------------------------------+-------+----------+
     ```

  3. Specify the class in the update cluster command:

     ```
     $ yc managed-clickhouse cluster update <cluster name>
          --clickhouse-resource-preset <class ID>
     ```

     {{ mch-short-name }} will run the update host class command for the cluster.

     You can change the {{ZK}} host class using a similar parameter: `--zookeeper-resource-preset`.

- API

  You can change the [host class](../concepts/instance-types.md) using the API [update](../api-ref/Cluster/update.md) method: pass the appropriate values in the request parameter `configSpec.clickhouse.resources.resourcePresetId` (for ZooKeeper — `configSpec.zookeeper.resources.resourcePresetId`).

  To request a list of supported values, use the [list](../api-ref/ResourcePreset/list.md) method for the `ResourcePreset` resources.

{% endlist %}

## Increasing storage size {#change-disk-size}

{% list tabs %}

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  To increase the storage size for a cluster:

  1. Make sure the required cluster is using network storage (it is not yet possible to increase the size of local storage). To do this, request information about the cluster and find the `disk_type_id` field: it should be set to `network-hdd` or `network-ssd`:

     ```
     $ yc managed-clickhouse cluster get <cluster name>
     
     id: c7qkvr3u78qiopj3u4k2
     folder_id: b1g0ftj57rrjk9thribv
     ...
     config:
       clickhouse:
         resources:
           resource_preset_id: s1.nano
           disk_size: "21474836480"
           disk_type_id: network-ssd
     ...
     ```

  1. View a description of the CLI's update cluster command:

     ```
     $ yc managed-clickhouse cluster update --help
     ```

  1. Make sure the cloud's quota is sufficient to increase the storage size: open the [Quotas]({{ link-console-quotas }}) page for your cloud and check that the {{ mch-full-name }} section still has space available in the **space** line.

  1. Specify the required amount of storage in the update cluster command (it must be at least as large as `disk_size` in the cluster properties):

     ```
     $ yc managed-clickhouse cluster update <cluster name>
          --clickhouse-disk-size <storage size in GB>
     ```

     If all these conditions are met, {{ mch-short-name }}  launches the operation to increase storage space.

     You can change the storage size for ZooKeeper by using the same parameter, `--zookeeper-disk-size`.

- API

  You can change the cluster's storage size using the API [update](../api-ref/Cluster/update.md) method: pass the appropriate values in the request parameter `configSpec.clickhouse.resources.diskSize` (for ZooKeeper, the parameter is `configSpec.zookeeper.resources.diskSize`).

  Make sure the cloud's quota is sufficient to increase the storage size: open the [Quotas]({{link-console-quotas}}) page for your cloud and check that the {{ mch-full-name }} section still has space available in the **space** line.

{% endlist %}

## Changing {{ CH }} settings {#change-clickhouse-config}

You can change the DBMS settings of the hosts in your cluster.

{% list tabs %}

- Management console

  1. Go to the folder page and select **{{ mch-name }}**.

  1. Select the cluster and click **Edit cluster** in the top panel.

  1. Under **DBMS settings**, click **Configure**.

  1. Configure the cluster parameters:

     {% include [mch-additional-properties](../../_includes/mdb/mch-additional-properties.md) %}

  1. Click **Save**.

- API

  You can change the DBMS settings for a cluster using the API [update](../api-ref/Cluster/update.md) method: pass the appropriate values in the request parameter `configSpec.clickhouse.config`. All supported settings are described [in the API reference](../api-ref/Cluster/update.md).

{% endlist %}

