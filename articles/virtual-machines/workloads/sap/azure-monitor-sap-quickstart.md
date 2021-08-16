---
title: 使用 Azure 门户部署 Azure Monitor for SAP Solutions
description: 使用 Azure 门户部署 Azure Monitor for SAP Solutions
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.date: 08/17/2020
ms.openlocfilehash: d9febb4efba85d47abe1cc11a3cb52dc0393c036
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672002"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>使用 Azure 门户部署 Azure Monitor for SAP Solutions

可以通过 [Azure 门户](https://azure.microsoft.com/features/azure-portal)创建 Azure Monitor for SAP Solutions 资源。 此方法提供基于浏览器的用户界面，以便于部署 Azure Monitor for SAP Solutions 并配置提供程序。

## <a name="sign-in-to-azure-portal"></a>登录到 Azure 门户

登录到 Azure 门户 (https://portal.azure.com)

## <a name="create-monitoring-resource"></a>创建监视资源

1. 从“Azure 市场”中选择“Azure Monitor for SAP Solutions” 。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="图像显示从“Azure 市场”中选择“Azure Monitor for SAP Solutions”。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. 在“基本信息”选项卡中，提供必填的值。 在适当的情况下，可以使用现有的 Log Analytics 工作区。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="显示 Azure 门户配置选项。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. 选择虚拟网络时，请确保可从该 VNET 中访问要监视的系统。 

   > [!IMPORTANT]
   > 选择“共享”时，数据将与 Microsoft 共享，这样支持团队即可以提供更多的支持。

## <a name="configure-providers"></a>配置提供程序

### <a name="sap-hana-provider"></a>SAP HANA 提供程序 

1. 选择“提供程序”选项卡，添加要配置的提供程序。 可以逐一添加多个提供程序，也可以在部署监视资源后添加它们。 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="显示用于向 Azure Monitor for SAP Solutions 添加其他提供程序的“提供程序”选项卡。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. 选择“添加提供程序”，然后从下拉列表中选择“SAP HANA” 。 

   > [!IMPORTANT]
   > 确保为 SAP HANA“主”节点配置了 SAP HANA 提供程序。

3. 输入 HANA 服务器的专用 IP。

4. 输入要使用的数据库租户的名称。 可以选择任意租户，但我们建议使用 SYSTEMDB，因为它支持的监视区域更广泛。 

5. 输入与 HANA 数据库关联的 SQL 端口号。 端口号的格式应为 [3] + [实例编号] + [13]  。 例如，30013。 

6. 输入要使用的数据库用户名。 确保该数据库用户已分配有“监视”和“目录读取” 角色。 

7. 完成后，选择“添加提供程序”。 根据需要继续添加更多提供程序，或者选择“查看 + 创建”完成部署。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="添加提供程序信息时的配置选项的图像。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>高可用性群集 (Pacemaker) 提供程序

1. 从下拉列表中选择“高可用性群集(Pacemaker)”。 

   > [!IMPORTANT]
   > 若要配置高可用性群集 (Pacemaker) 提供程序，请确保在每个节点中安装 ha_cluster_provider。 有关详细信息，请参阅 [HA 群集导出程序](https://github.com/ClusterLabs/ha_cluster_exporter#installation)

2. 按照 http://IP:9664/metrics 格式输入 Prometheus 终结点。 
 
3. 输入系统 ID (SID)、主机名和群集名称。

4. 完成后，选择“添加提供程序”。 根据需要继续添加更多提供程序，或者选择“查看 + 创建”完成部署。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="图像显示与 HA 群集 Pacemaker 提供程序相关的选项。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::


### <a name="os-linux-provider"></a>OS (Linux) 提供程序 

1. 从下拉列表中选择“OS (Linux)” 

> [!IMPORTANT]
> 若要配置 OS (Linux) 提供程序，请确保在每个 BareMetal 实例中安装 Node_Exporter。 有关详细信息，请参阅  [Node_Exporter](https://github.com/prometheus/node_exporter)

2. 输入一个名称，该名称将作为 BareMetal 实例的标识符。
3. 按照 http://IP:9100/metrics 格式输入节点导出程序终结点。
4. 完成后，选择“添加提供程序” **** 。 根据需要继续添加更多提供程序，或者选择“查看 + 创建”完成部署 ****  。 


### <a name="microsoft-sql-server-provider"></a>Microsoft SQL Server 提供程序

1. 添加 Microsoft SQL Server 提供程序之前，应在 SQL Server Management Studio 中运行以下脚本，以创建具有配置提供程序所需的相应权限的用户。

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

2. 选择“添加提供程序”，然后从下拉列表中选择“Microsoft SQL Server” 。 

3. 使用与 Microsoft SQL Server 相关联的信息填写字段。 

4. 完成后，选择“添加提供程序”。 根据需要继续添加更多提供程序，或者选择“查看 + 创建”完成部署。

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="图像显示与添加 Microsoft SQL Server 提供程序相关的信息。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

## <a name="next-steps"></a>后续步骤

详细了解 [Azure Monitor for SAP Solutions](azure-monitor-overview.md)
