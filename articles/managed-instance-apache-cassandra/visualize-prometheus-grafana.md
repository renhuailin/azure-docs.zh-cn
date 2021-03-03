---
title: 配置 Grafana 以直观显示从 Azure 托管实例针对 Apache Cassandra 发出的指标
description: 了解如何在 VM 中安装和配置 Grafana，以可视化从 Azure 托管实例为 Apache Cassandra 群集发出的指标。
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: ed0ff343595429a4cb81fef280203f1180eeb098
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744446"
---
# <a name="configure-grafana-to-visualize-metrics-emitted-from-the-managed-instance-cluster"></a>将 Grafana 配置为显示从托管实例群集发出的指标

> [!IMPORTANT]
> 适用于 Apache Cassandra 的 Azure 托管实例当前提供公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

当你为 Apache Cassandra 群集部署 Azure 托管实例时，该服务将预配托管 [Prometheus](https://prometheus.io/) 的服务器，该服务器可供各种客户端工具使用。 Prometheus 是一种开源监视解决方案。 托管实例将发出指标，并保留10分钟或 10 GB 的数据 (每) 阈值。 本文介绍如何配置 Grafana 以直观显示从托管实例群集发出的指标。 需要以下任务来可视化度量值：

* 在存在托管实例的 Azure 虚拟网络中部署 Ubuntu 虚拟机。
* 安装开源 [Grafana 工具](https://grafana.com/grafana/) ，以构建仪表板并可视化从 Prometheus 发出的指标。

## <a name="deploy-a-ubuntu-server"></a>部署 Ubuntu 服务器

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 导航到托管实例群集所在的资源组。 选择 " **添加** 并搜索 **UBUNTU Server 18.04 LTS** " 映像：

   :::image type="content" source="./media/visualize-prometheus-grafana/select-ubuntu-image.png" alt-text="从 Azure 门户查找 Ubuntu server 映像。" border="true":::

1. 选择映像，然后选择 " **创建**"。

1. 在 " **创建虚拟机** " 边栏选项卡中，为以下字段输入值，你可以为其他字段保留默认值：

   * **虚拟机名称** -输入 VM 的名称。
   * **区域** -选择部署了虚拟网络的同一区域。

   :::image type="content" source="./media/visualize-prometheus-grafana/create-vm-ubuntu.png" alt-text="填写表单以使用 Ubuntu server 映像创建 VM。" border="true":::

1. 在 " **网络** " 选项卡中，选择托管实例部署到的虚拟网络流入量：

   :::image type="content" source="./media/visualize-prometheus-grafana/configure-networking-details.png" alt-text="配置 Ubuntu 服务器的网络设置。" border="true":::

1. 最后，选择 " **查看 + 创建** " 创建 Grafana 服务器。

## <a name="install-grafana"></a>安装 Grafana

1. 在 Azure 门户中，打开部署了托管实例的虚拟网络和 Grafana 服务器。 应会看到名为 cassandra 的虚拟机规模集实例 **(实例 0)**。 此 Prometheus 度量值托管在此虚拟机规模集中。 记下此实例的 IP 地址：

   :::image type="content" source="./media/visualize-prometheus-grafana/prometheus-instance-address.png" alt-text="获取 Prometheus 实例的 IP 地址。" border="true":::

1. 使用 [Azure CLI](../virtual-machines/linux/ssh-from-windows.md#ssh-clients) 或你喜欢的客户端工具通过 SSH 连接到新创建的 Ubuntu 服务器。

1. 连接到 VM 后，必须安装并配置 Grafana 以连接到托管指标的虚拟机规模集。 打开命令提示符，然后输入 `nano` 命令以打开 Nano 文本编辑器。 将以下脚本粘贴到文本编辑器中，确保将替换为 `<prometheus IP address>` 上一步中记录的 IP 地址：

   ```bash
   #!/bin/bash
   
   echo "Installing Grafana..."
   
   if ! $SSH dpkg -s grafana prometheus > /dev/null; then
       echo "Installing packages."
       echo 'deb https://packages.grafana.com/oss/deb stable main' | $SSH sudo tee /etc/apt/sources.list.d/grafana.list > /dev/null
       curl https://packages.grafana.com/gpg.key | $SSH sudo apt-key add -
       $SSH sudo apt-get update
       $SSH sudo apt-get install -y grafana prometheus
   else
       echo "Skipping package installation"
   fi
   
   echo "Configuring grafana"
   cat <<EOF | $SSH sudo tee /etc/grafana/provisioning/datasources/prometheus.yml
   apiVersion: 1
   datasources:
     - name: Prometheus
       type: prometheus
       url: https://<prometheus IP address>:9443
       jsonData:
         tlsSkipVerify: true
   EOF
   
   echo "Restarting Grafana"
   $SSH sudo systemctl enable grafana-server
   $SSH sudo systemctl restart grafana-server
   
   echo "Installing Grafana plugins"
   $SSH sudo grafana-cli plugins install natel-discrete-panel
   $SSH sudo grafana-cli plugins install grafana-polystat-panel
   $SSH sudo systemctl restart grafana-server
   ```

1. 键入 `ctrl + X` 以保存文件。 您可以命名该文件 `grafana.sh` 。

1. `./grafana.sh`在命令提示符下输入命令以安装 Grafana。

1. 安装完成后，Grafana 将在服务器 IP 地址的 **端口 3000** 中提供，如以下屏幕截图所示：

   :::image type="content" source="./media/visualize-prometheus-grafana/open-grafana-port.png" alt-text="在端口3000上运行 Grafana。" border="true":::

1. 你可以选择在 Grafana 中为 Apache Cassandra 创建的开放源仪表板，例如 [群集概述](https://github.com/TheovanKraay/cassandra-exporter/blob/master/grafana/instaclustr/cluster-overview.json) JSON 文件。 将仪表板的 JSON 定义下载并导入到 Grafana 中：

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-import.png" alt-text="导入 Grafana JSON 定义。" border="true":::

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-upload-json.png" alt-text="上传 Grafana JSON 定义。" border="true":::

1. 然后，你可以通过所选仪表板监视 cassandra 托管实例群集：

   :::image type="content" source="./media/visualize-prometheus-grafana/monitor-cassandra-metrics.gif" alt-text="查看仪表板中的 Cassandra 托管实例指标。" border="true":::

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 Grafana 将仪表板配置为直观显示 Prometheus 中的指标。 若要深入了解 Azure 托管实例，请参阅以下文章：

* [适用于 Apache Cassandra 的 Azure 托管实例概述](introduction.md)
* [使用 Azure Databricks (预览版部署托管 Apache Spark 群集) ](deploy-cluster-databricks.md)
