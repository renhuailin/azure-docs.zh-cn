---
title: 配置 Grafana 以可视化 Azure Managed Instance for Apache Cassandra 发出的指标
description: 了解如何在 VM 中安装并配置 Grafana，以可视化 Azure Managed Instance for Apache Cassandra 群集发出的指标。
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: ed0ff343595429a4cb81fef280203f1180eeb098
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101744446"
---
# <a name="configure-grafana-to-visualize-metrics-emitted-from-the-managed-instance-cluster"></a>配置 Grafana 以可视化托管实例群集发出的指标

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra 目前以公共预览版形式提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

当你部署 Azure Managed Instance for Apache Cassandra 群集时，该服务将预配一个服务器用于托管可供各种客户端工具使用的 [Prometheus](https://prometheus.io/)。 Prometheus 是一个开源的监视解决方案。 托管实例将发出指标，并保留 10 分钟的数据或 10 GB 数据（以先达到阈值者为准）。 本文介绍如何配置 Grafana 以可视化托管实例群集发出的指标。 需要完成以下任务才能可视化指标：

* 在托管实例所在的 Azure 虚拟网络中部署一个 Ubuntu 虚拟机。
* 安装开源的 [Grafana 工具](https://grafana.com/grafana/)，以构建仪表板并可视化 Prometheus 发出的指标。

## <a name="deploy-a-ubuntu-server"></a>部署 Ubuntu 服务器

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 导航到托管实例群集所在的资源组。 选择“添加”并搜索“Ubuntu Server 18.04 LTS”映像 ：

   :::image type="content" source="./media/visualize-prometheus-grafana/select-ubuntu-image.png" alt-text="在 Azure 门户中查找 Ubuntu Server 映像。" border="true":::

1. 选择该映像，然后选择“创建”。

1. 在“创建虚拟机”边栏选项卡中输入以下字段的值，可为其他字段保留默认值：

   * 虚拟机名称 - 输入 VM 的名称。
   * 区域 - 选择部署了你的虚拟网络的同一区域。

   :::image type="content" source="./media/visualize-prometheus-grafana/create-vm-ubuntu.png" alt-text="填写表单以使用 Ubuntu Server 映像创建 VM。" border="true":::

1. 在“网络”选项卡中，选择托管实例所部署到的虚拟网络：

   :::image type="content" source="./media/visualize-prometheus-grafana/configure-networking-details.png" alt-text="配置 Ubuntu Server 的网络设置。" border="true":::

1. 最后，选择“查看 + 创建”以创建 Grafana 服务器。

## <a name="install-grafana"></a>安装 Grafana

1. 在 Azure 门户中，打开部署了托管实例和 Grafana 服务器的虚拟网络。 应会看到名为“cassandra-jump (实例 0)”的虚拟机规模集实例。 此 Prometheus 指标托管在此虚拟机规模集中。 请记下此实例的 IP 地址：

   :::image type="content" source="./media/visualize-prometheus-grafana/prometheus-instance-address.png" alt-text="获取 Prometheus 实例的 IP 地址。" border="true":::

1. 使用 [Azure CLI](../virtual-machines/linux/ssh-from-windows.md#ssh-clients) 或你偏好的客户端工具通过 SSH 连接到新建的 Ubuntu Server。

1. 连接到 VM 后，必须安装并配置 Grafana 以连接到托管指标的虚拟机规模集。 打开命令提示符，并输入 `nano` 命令打开 Nano 文本编辑器。 将以下脚本粘贴到文本编辑器中（请务必将 `<prometheus IP address>` 替换为在上一步骤中记下的 IP 地址）：

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

1. 键入 `ctrl + X` 保存文件。 可将该文件命名为 `grafana.sh`。

1. 在命令提示符下输入 `./grafana.sh` 命令安装 Grafana。

1. 安装完成后，可以使用服务器 IP 地址加上端口 3000 访问 Grafana，如以下屏幕截图所示：

   :::image type="content" source="./media/visualize-prometheus-grafana/open-grafana-port.png" alt-text="通过端口 3000 运行 Grafana。" border="true":::

1. 可以在 Grafana 中选择为 Apache Cassandra 创建的不同开源仪表板，例如 [cluster-overview](https://github.com/TheovanKraay/cassandra-exporter/blob/master/grafana/instaclustr/cluster-overview.json) JSON 文件。 将仪表板的 JSON 定义下载并导入到 Grafana 中：

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-import.png" alt-text="导入 Grafana JSON 定义。" border="true":::

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-upload-json.png" alt-text="上传 Grafana JSON 定义。" border="true":::

1. 然后，可以通过所选的仪表板监视 Cassandra 托管实例群集：

   :::image type="content" source="./media/visualize-prometheus-grafana/monitor-cassandra-metrics.gif" alt-text="在仪表板中查看 Cassandra 托管实例指标。" border="true":::

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何使用 Grafana 配置仪表板以可视化 Prometheus 中的指标。 请参阅以下文章详细了解 Azure Managed Instance for Apache Cassandra：

* [Azure Managed Instance for Apache Cassandra 概述](introduction.md)
* [使用 Azure Databricks 部署托管的 Apache Spark 群集（预览版）](deploy-cluster-databricks.md)
