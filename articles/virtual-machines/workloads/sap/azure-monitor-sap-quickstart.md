---
title: 使用 Azure 门户部署 Azure Monitor for SAP Solutions
description: 使用 Azure 门户部署 Azure Monitor for SAP Solutions
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.date: 08/17/2020
ms.openlocfilehash: a9208101777cd88f0237e661a414550759a069b0
ms.sourcegitcommit: aaba99b8b1c545ad5d19f400bcc2d30d59c63f39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2021
ms.locfileid: "108007374"
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

### <a name="sap-netweaver-provider"></a>SAP NetWeaver 提供程序

#### <a name="prerequisites-for-adding-netweaver-provider"></a>添加 NetWeaver 提供程序的先决条件

“SAP 启动服务”提供了许多服务，包括监视 SAP 系统。 我们使用的是“SAPControl”，这一 SOAP Web 服务接口可公开这些功能。 此 SAPControl Web 服务接口可区分[受保护和不受保护的](https://wiki.scn.sap.com/wiki/display/SI/Protected+web+methods+of+sapstartsrv) Web 服务方法。 若要获取特定指标，需要取消对某些方法的保护。 若要取消对当前版本所需方法的保护，请针对每个 SAP 系统执行以下步骤：

1. 打开与 SAP 服务器的 SAP GUI 连接
2. 使用管理帐户登录
3. 执行事务 RZ10
4. 选择相应的配置文件 (DEFAULT.PFL)
5. 选择“扩展维护”，并单击“更改” 
6. 将受影响参数“service/protectedwebmethods = SDEFAULT -GetQueueStatistic –ABAPGetWPTable –EnqGetStatistic –GetProcessList”的值修改为建议设置，然后单击“复制”
7. 返回并选择“配置文件”->“保存”
8. 重启系统以使参数生效

>[!Tip]
> 使用访问控制列表 (ACL) 筛选对服务器端口的访问。 请参阅此 [SAP 说明](https://launchpad.support.sap.com/#/notes/1495075)

#### <a name="installing-netweaver-provider-on-the-azure-portal"></a>在 Azure 门户上安装 NetWeaver 提供程序
1.  请确保已完成先决条件步骤且已重启服务器
2.  在 Azure 门户的“AMS”下，选择“添加提供程序”，并从下拉菜单中选择“SAP NetWeaver”
3.  输入 SAP 系统和子域的主机名（如适用）
4.  输入与已输入的主机名对应的实例编号 
5.  输入系统 ID (SID)
6.  完成后，选择“添加提供程序”
7.  根据需要继续添加其他提供程序，或者选择“查看”和“创建”以完成部署

![image](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

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

   >[!IMPORTANT]
   > 若要配置 OS (Linux) 提供程序，请确保在要监视的每个主机（BareMetal 或 VM）中已安装最新版本的 Node_Exporter。 请安装[最新版本的节点导出程序](https://prometheus.io/download/#node_exporter)。 [了解详细信息](https://github.com/prometheus/node_exporter)

2. 输入一个名称，该名称将作为 BareMetal 实例的标识符。
3. 按照 http://IP:9100/metrics 格式输入节点导出程序终结点。

   >[!IMPORTANT]
   >请使用 Linux 主机的专用 IP 地址。 请确保主机和 AMS 资源位于同一 VNET 中。 

   >[!Note]
   > 应在 Linux 主机上打开防火墙端口“9100”。
   >如果使用 firewall-cmd，请使用以下命令：firewall-cmd --permanent --add-port=9100/tcp firewall-cmd --reload。如果使用 ufw，请使用以下命令：ufw allow 9100/tcp ufw reload

    >[!Tip]
    > 如果 Linux 主机是 Azure VM，请确保所有适用的 NSG 都允许来自“VirtualNetwork”的端口 9100 上的入站流量作为源。
 
5. 完成后，选择“添加提供程序” **** 。 根据需要继续添加更多提供程序，或者选择“查看 + 创建”完成部署 ****  。 


## <a name="next-steps"></a>后续步骤

详细了解 [Azure Monitor for SAP Solutions](azure-monitor-overview.md)
