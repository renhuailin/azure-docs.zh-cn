---
title: 使用 Azure 门户部署 Azure Monitor for SAP Solutions
description: 了解如何使用浏览器方法来部署 Azure Monitor for SAP Solutions。
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 07/08/2021
ms.openlocfilehash: 33965b51895e38df67ac3a542e8b40d71cbc2912
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737395"
---
# <a name="deploy-azure-monitor-for-sap-solutions-by-using-the-azure-portal"></a>使用 Azure 门户部署 Azure Monitor for SAP Solutions

本文介绍如何从 [Azure 门户](https://azure.microsoft.com/features/azure-portal)部署 Azure Monitor for SAP Solutions。 使用门户基于浏览器的界面，可以部署 Azure Monitor for SAP Solutions 并配置提供程序。

## <a name="sign-in-to-the-portal"></a>登录门户

登录 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-monitoring-resource"></a>创建监视资源

1. 在“市场”下方，选择“Azure Monitor for SAP Solutions” 。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="显示从“Azure 市场”中选择“Azure Monitor for SAP Solutions”产品/服务的屏幕截图。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. 在“基本信息”选项卡中，提供必填的值。 在适当的情况下，可以使用现有的 Log Analytics 工作区。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="显示“基本信息”选项卡上配置选项的屏幕截图。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

   选择虚拟网络时，请确保可从该虚拟网络中访问要监视的系统。 

   > [!IMPORTANT]
   > 对于“与 Microsoft 支持团队共享数据”，选择“共享”以使我们的支持团队可以帮助你排除故障 。

## <a name="configure-providers"></a>配置提供程序

### <a name="sap-netweaver-provider"></a>SAP NetWeaver 提供程序

SAP 启动服务提供了许多服务，其中包括监视 SAP 系统。 我们使用的是“SAPControl”，这一 SOAP Web 服务接口可公开这些功能。 SAPControl Web 服务接口可区分[受保护和不受保护的](https://wiki.scn.sap.com/wiki/display/SI/Protected+web+methods+of+sapstartsrv) Web 服务方法。 

若要获取特定的指标，需要取消对当前版本的某些方法的保护。 对每个 SAP 系统执行以下步骤：

1. 打开与 SAP 服务器的 SAP GUI 连接。
2. 使用管理帐户登录。
3. 执行事务 RZ10。
4. 选择相应的配置文件 (DEFAULT.PFL)。
5. 依次选择“扩展维护” > “更改” 。 
6. 选择配置文件参数“service/protectedwebmethods”并将其修改为具有以下值，然后单击“复制”：  
 
SDEFAULT -GetQueueStatistic -ABAPGetWPTable -EnqGetStatistic -GetProcessList 

7. 返回并选择“配置文件” > “保存” 。
8. 保存对此参数的更改后，请在 SAP 系统中的每个实例上重新启动 SAPStartSRV 服务。 （重新启动服务不会重新启动 SAP 系统；它只会重新启动 SAPStartSRV 服务 (在 Windows 中) 或守护程序进程(在 Unix/Linux 中)）8a. 在 Windows 系统上，可以在单个窗口中使用 SAP Microsoft 管理控制台 (MMC)/SAP 管理控制台 (MC) 来完成此操作。  右键单击每个实例，然后选择“所有任务”->“重新启动服务”。
   ![MMC](https://user-images.githubusercontent.com/75772258/126453939-daf1cf6b-a940-41f6-98b5-3abb69883520.png) 8b. 在 Linux 系统上，使用命令：sapcontrol -nr <NN> -function RestartService，其中 NN 是用于重启登录到的主机的 SAP 实例编号。
9. 重新启动 SAP 服务后，请通过运行以下命令进行检查，以确保已为每个实例应用了更新后的 Web 方法保护排除规则：sapcontrol -nr <NN> -function ParameterValue service/protectedwebmethods -user“<adminUser>”“<adminPassword>”输出应如下所示：- ![SS](https://user-images.githubusercontent.com/75772258/126454265-d73858c3-c32d-4afe-980c-8aba96a0b2a4.png)
10. 若要进行总结和验证，可以通过登录到每个实例并运行以下命令，对 Web 方法执行测试查询来验证连接：对于所有实例：sapcontrol -nr <NN> -function GetProcessList 对于 ENQUE 实例：sapcontrol -nr <NN> -function EnqGetStatistic 对于 ABAP 实例：sapcontrol -nr <NN> -function ABAPGetWPTable 对于 ABAP/J2EE/JEE 实例：sapcontrol -nr <NN> -function GetQueueStatistic

>[!Important] 
>若要取消对 SAPControl Web 方法的保护，在 SAP 系统的每个实例上重新启动 sapstartsrv 服务非常重要。  NetWeaver 提供程序需要这些只读 SOAP API 才能从 SAP 系统中提取指标数据，并且无法取消对这些方法的保护将导致 NetWeaver 指标工作簿上出现空白或缺失的可视化效果。
   
>[!Tip]
> 使用访问控制列表 (ACL) 筛选对服务器端口的访问。 有关详细信息，请参阅[此 SAP 说明](https://launchpad.support.sap.com/#/notes/1495075)。

在 Azure 门户上安装 NetWeaver 提供程序：

1. 请确保已完成之前的先决条件步骤，并且服务器已重新启动。
1. 在 Azure 门户的“Azure Monitor for SAP Solutions”下方，选择“添加提供程序”，然后 ：

   1. 对于“类型”，选择“SAP NetWeaver” 。

   1. 对于“主机名”，输入 SAP 系统的主机名。

   1. 对于“子域”，输入子域（如果适用）。

   1. 对于“实例编号”，输入与输入的主机名对应的实例编号。 

   1. 对于“SID”，输入系统 ID。
   
   ![显示用于添加 SAP NetWeaver 提供程序的配置选项的屏幕截图。](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

1.  完成后，选择“添加提供程序”。 根据需要继续添加提供程序，或者选择“查看 + 创建”完成部署。

>[!Important]
>如果 SAP 应用程序服务器（即虚拟机）是网络域的一部分，例如 Azure Active Directory 管理的虚拟机，则在“子域”文本框中提供相应的子域非常重要。  虚拟网络内存在的适用于 SAP 收集器 VM 的 Azure Monitor 未加入域中，因此，除非主机名为完全限定的域名，否则将无法解析 SAP 系统内实例的主机名。  如果无法提供此项，将会导致 NetWeaver 工作簿中的可视化效果缺失/不完整。
 
>例如，如果 SAP 系统的主机名具有完全限定的域名“myhost.mycompany.global.corp”，请输入“myhost”作为主机名，并提供子域“mycompany.global.corp”。  NetWeaver 提供程序在 SAP 系统上调用 GetSystemInstanceList API 时，SAP 将返回系统中所有实例的主机名。  收集器 VM 将使用此列表进行额外的 API 调用来提取特定于每个实例功能的指标（例如 ABAP、J2EE、MESSAGESERVER、ENQUE、ENQREP 等）。 如果已指定，收集器 VM 将使用子域“mycompany.global.corp”来构建 SAP 系统中每个实例的完全限定的域名。  
 
>如果 SAP 系统是网络域的一部分，请不要为主机名字段指定 IP 地址。

   
### <a name="sap-hana-provider"></a>SAP HANA 提供程序 

1. 选择“提供程序”选项卡，添加要配置的提供程序。 可以逐一添加多个提供程序，也可以在部署监视资源后添加它们。 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="显示添加提供程序的选项卡的屏幕截图。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

1. 选择“添加提供程序”，然后进行以下操作：

   1. 对于“类型”，选择“SAP HANA” 。 

      > [!IMPORTANT]
      > 确保为 SAP HANA `master` 节点配置了 SAP HANA 提供程序。

   1. 对于“IP 地址”，输入 HANA 服务器的专用 IP 地址。

   1. 对于“数据库租户”，输入要使用的租户的名称。 可以选择任意租户，但我们建议使用 SYSTEMDB，因为它支持的监视区域更广泛。 

   1. 对于“SQL 端口”，输入与 HANA 数据库关联的端口号。 端口号的格式应为 [3] + [实例编号] + [13]  。 例如 30013。 

   1. 对于“数据库用户名”，输入要使用的用户名。 确保数据库用户已分配有“监视”和“目录读取”角色 。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="显示用于添加 SAP HANA 提供程序的配置选项的屏幕截图。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

1. 完成后，选择“添加提供程序”。 根据需要继续添加提供程序，或者选择“查看 + 创建”完成部署。

   
### <a name="microsoft-sql-server-provider"></a>Microsoft SQL Server 提供程序

1. 添加 Microsoft SQL Server 提供程序之前，请在 SQL Server Management Studio 中运行以下脚本，以创建具有配置提供程序所需的相应权限的用户。

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

1. 选择“添加提供程序”，然后进行以下操作：

   1. 对于“类型”，选择“Microsoft SQL Server” 。 

   1. 使用与 SQL Server 实例相关联的信息填写剩余字段。 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="显示用于添加 Microsoft SQL Server 提供程序的配置选项的屏幕截图。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

1. 完成后，选择“添加提供程序”。 根据需要继续添加提供程序，或者选择“查看 + 创建”完成部署。

### <a name="high-availability-cluster-pacemaker-provider"></a>高可用性群集 (Pacemaker) 提供程序

在为高可用性 (pacemaker) 群集添加提供程序之前，请为你的环境安装适当的代理。

对于基于 SUSE 的群集，请确保在每个节点中安装了 ha_cluster_provider。 请参阅如何安装 [HA 群集导出程序](https://github.com/ClusterLabs/ha_cluster_exporter#installation)。 支持的 SUSE 版本：SLES for SAP 12 SP3 及更高版本。  
   
对于基于 RHEL 的群集，请确保在每个节点中安装了性能共同试验 (PCP) 和 pcp-pmda-hacluster 子包。 请参阅如何安装 [PCP HACLUSTER 代理](https://access.redhat.com/articles/6139852) 。 支持的 RHEL 版本：8.2、8.4 及更高版本。
 
完成上述的必备组件安装后，请为每个群集节点创建一个提供程序。

1. 选择“添加提供程序”，然后进行以下操作：

1. 对于“类型”，选择“高可用性群集(Pacemaker)” 。 
   
1. 在 HA 群集导出程序终结点中输入终结点 URL，为群集的每个节点配置提供程序。 对于基于 SUSE 的群集，输入 http://<IP  address>:9664/metrics 。 对于基于 RHEL 的群集，输入 http://<IP address>:44322/metrics?names=ha_cluster 
 
1. 在相应的框中输入系统 ID、主机名和群集名称。
   
   > [!IMPORTANT]
   > 主机名指 VM 中的实际主机名。 对于基于 SUSE 和 RHEL 的群集，请使用“hostname -s”命令。  

1. 完成后，选择“添加提供程序”。 根据需要继续添加提供程序，或者选择“查看 + 创建”完成部署。

### <a name="os-linux-provider"></a>OS (Linux) 提供程序 

1. 选择“添加提供程序”，然后进行以下操作：

   1. 对于“类型”，选择“OS(Linux)” 。 

      >[!IMPORTANT]
      > 若要配置 OS (Linux) 提供程序，请确保在要监视的每个主机（BareMetal 或虚拟机）中安装[最新版本的 node_exporter](https://prometheus.io/download/#node_exporter)。 [了解详细信息](https://github.com/prometheus/node_exporter)。

   1. 对于“名称”，输入将作为 BareMetal 实例的标识符的名称。

   1. 对于“节点导出程序终结点”，输入 http://IP:9100/metrics 。

      >[!IMPORTANT]
      >使用 Linux 主机的专用 IP 地址。 确保主机和 Azure Monitor for SAP 资源位于同一虚拟网络中。 
      >
      >应在 Linux 主机上打开防火墙端口 9100。 如果使用的是 `firewall-cmd`，请运行以下命令： 
      >
      >`firewall-cmd --permanent --add-port=9100/tcp`
      >
      >`firewall-cmd --reload`
      >
      >如果使用的是 `ufw`，请运行以下命令：
      >
      >`ufw allow 9100/tcp`
      >
      >`ufw reload`
      >
      > 如果 Linux 主机是 Azure 虚拟机 (VM)，请确保所有适用的网络安全组都允许从 `VirtualNetwork` 进入端口 9100 的流量作为源。
 
1. 完成后，选择“添加提供程序” **** 。 根据需要继续添加提供程序，或者选择“查看 + 创建”完成部署 ****  。 


## <a name="next-steps"></a>后续步骤

详细了解 Azure Monitor for SAP Solutions。

> [!div class="nextstepaction"]
> [Azure Monitor for SAP Solutions](azure-monitor-overview.md)
