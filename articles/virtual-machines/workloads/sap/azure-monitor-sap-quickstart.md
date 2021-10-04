---
title: 使用 Azure 门户部署 Azure Monitor for SAP Solutions
description: 了解如何使用浏览器方法来部署 Azure Monitor for SAP Solutions。
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 07/08/2021
ms.openlocfilehash: 85cfe6887ded3844e2143754c31a3c6efee5e132
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128579453"
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

   ```service/protectedwebmethods instruction
      SDEFAULT -GetQueueStatistic -ABAPGetWPTable -EnqGetStatistic -GetProcessList```

7. Go back and select **Profile** > **Save**.
8. After saving the changes for this parameter, please restart the SAPStartSRV service on each of the instances in the SAP system. (Restarting the services will not restart the SAP system; it will only restart the SAPStartSRV service (in Windows) or daemon process (in Unix/Linux))
   8a. On Windows systems, this can be done in a single window using the SAP Microsoft Management Console (MMC) / SAP Management Console(MC).  Right-click on each instance and choose All Tasks -> Restart Service.
![MMC](https://user-images.githubusercontent.com/75772258/126453939-daf1cf6b-a940-41f6-98b5-3abb69883520.png)

   8b. On Linux systems, use the below command where NN is the SAP instance number to restart the host which is logged into.
   
   ```RestartService
   sapcontrol -nr <NN> -function RestartService```
   
9. Once the SAP service is restarted, please check to ensure the updated web method protection exclusion rules have been applied for each instance by running the following command: 

**Logged as \<sidadm\>** 
   `sapcontrol -nr <NN> -function ParameterValue service/protectedwebmethods`

**Logged as different user** 
   `sapcontrol -nr <NN> -function ParameterValue service/protectedwebmethods -user "<adminUser>" "<adminPassword>"`

   The output should look like :-
   ![SS](https://user-images.githubusercontent.com/75772258/126454265-d73858c3-c32d-4afe-980c-8aba96a0b2a4.png)

10. To conclude and validate, a test query can be done against web methods to validate the connection by logging into each instance and running the following commands:

    - For all instances : `sapcontrol -nr <NN> -function GetProcessList`
    - For the ENQUE instance : `sapcontrol -nr <NN> -function EnqGetStatistic`
    - For ABAP instances : `sapcontrol -nr <NN> -function ABAPGetWPTable`
    - For ABAP/J2EE/JEE instances : `sapcontrol -nr <NN> -function GetQueueStatistic`

>[!Important] 
>It is critical that the sapstartsrv service is restarted on each instance of the SAP system for the SAPControl web methods to be unprotected.  These read-only SOAP API are required for the NetWeaver provider to fetch metric data from the SAP System and failure to unprotect these methods will lead to empty or missing visualizations on the NetWeaver metric workbook.
   
>[!Tip]
> Use an access control list (ACL) to filter the access to a server port. For more information, see [this SAP note](https://launchpad.support.sap.com/#/notes/1495075).

To install the NetWeaver provider on the Azure portal:

1. Make sure you've completed the earlier prerequisite steps and that the server has been restarted.
1. On the Azure portal, under **Azure Monitor for SAP Solutions**, select **Add provider**, and then:

   1. For **Type**, select **SAP NetWeaver**.

   1. For **Hostname**, enter the host name of the SAP system.

   1. For **Subdomain**, enter a subdomain if one applies.

   1. For **Instance No**, enter the instance number that corresponds to the host name you entered. 

   1. For **SID**, enter the system ID.
   
   ![Screenshot showing the configuration options for adding a SAP NetWeaver provider.](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

1.    When you're finished, select **Add provider**. Continue to add providers as needed, or select **Review + create** to complete the deployment.

>[!Important]
>If the SAP application servers (ie. virtual machines) are part of a network domain, such as one managed by Azure Active Directory, then it is critical that the corresponding subdomain is provided in the Subdomain text box.  The Azure Monitor for SAP collector VM that exists inside the Virtual Network is not joined to the domain and as such will not be able to resolve the hostname of instances inside the SAP system unless the hostname is a fully qualified domain name.  Failure to provide this will result in missing / incomplete visualizations in the NetWeaver workbook.
 
>For example, if the hostname of the SAP system has a fully qualified domain name of "myhost.mycompany.global.corp" then please enter a Hostname of "myhost" and provide a Subdomain of "mycompany.global.corp".  When the NetWeaver provider invokes the GetSystemInstanceList API on the SAP system, SAP returns the hostnames of all instances in the system.  The collector VM will use this list to make additional API calls to fetch metrics specific to each instance's features (e.g.  ABAP, J2EE, MESSAGESERVER, ENQUE, ENQREP, etc…). If specified, the collector VM will then use the subdomain  "mycompany.global.corp" to build the fully qualified domain name of each instance in the SAP system.  
 
>Please DO NOT specify an IP Address for the hostname field if the SAP system is a part of network domain.

   
### SAP HANA provider 

1. Select the **Providers** tab to add the providers you want to configure. You can add multiple providers one after another, or add them after you deploy the monitoring resource. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Screenshot showing the tab where you add providers." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

1. Select **Add provider**, and then:

   1. For **Type**, select **SAP HANA**. 

      > [!IMPORTANT]
      > Ensure that a SAP HANA provider is configured for the SAP HANA `master` node.

   1. For **IP address**, enter the private IP address for the HANA server.

   1. For **Database tenant**, enter the name of the tenant you want to use. You can choose any tenant, but we recommend using **SYSTEMDB** because it enables a wider array of monitoring areas. 

   1. For **SQL port**, enter the port number associated with your HANA database. It should be in the format of *[3]* + *[instance#]* + *[13]*. An example is **30013**. 

   1. For **Database username**, enter the username you want to use. Ensure the database user has the *monitoring* and *catalog read* roles assigned.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Screenshot showing configuration options for adding an SAP HANA provider." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

1. When you're finished, select **Add provider**. Continue to add providers as needed, or select **Review + create** to complete the deployment.

   
### Microsoft SQL Server provider

1. Before you add the Microsoft SQL Server provider, run the following script in SQL Server Management Studio to create a user with the appropriate permissions for configuring the provider.

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
   
1. 在 HA 群集导出程序终结点中输入终结点 URL，为群集的每个节点配置提供程序。 对于基于 SUSE 的群集，输入 http://\<IP  address\>:9664/metrics 。 对于基于 RHEL 的群集，输入 http://\<IP address\>:44322/metrics?names=ha_cluster 
 
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
> [监视 Azure 上的 SAP](monitor-sap-on-azure.md)
