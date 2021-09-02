---
title: 在 Azure 中的 RHEL 上设置 Pacemaker | Microsoft Docs
description: 在 Azure 中的 Red Hat Enterprise Linux 上设置 Pacemaker
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.custom: subject-rbac-steps
ms.date: 08/26/2021
ms.author: radeltch
ms.openlocfilehash: 412bbd6f7414cdeaab1c116210b511bc8000c270
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123109795"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>在 Azure 中的 Red Hat Enterprise Linux 上设置 Pacemaker

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


请先阅读以下 SAP 说明和文档：

* SAP 说明 [1928533]，其中包含：
  * SAP 软件部署支持的 Azure VM 大小的列表。
  * Azure VM 大小的重要容量信息。
  * 支持的 SAP 软件、操作系统 (OS) 和数据库组合。
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 内核版本。
* SAP 说明 [2015553] 列出了在 Azure 中 SAP 支持的 SAP 软件部署的先决条件。
* SAP 说明 [2002167] 包含适用于 Red Hat Enterprise Linux 的建议 OS 设置
* SAP 说明 [2009879] 包含适用于 Red Hat Enterprise Linux 的 SAP HANA 准则
* SAP 说明 [2178632] 包含为 Azure 中的 SAP 报告的所有监控指标的详细信息。
* SAP 说明 [2191498] 包含 Azure 中的 Linux 所需的 SAP 主机代理版本。
* SAP 说明 [2243692] 包含 Azure 中的 Linux 上的 SAP 许可的相关信息。
* SAP 说明 [1999351] 包含适用于 SAP 的 Azure 增强型监视扩展的其他故障排除信息。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含适用于 Linux 的所有必需 SAP 说明。
* [适用于 Linux 上的 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机部署（本文）][deployment-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* [SAP HANA system replication in pacemaker cluster](https://access.redhat.com/articles/3004101)（Pacemaker 群集中的 SAP HANA 系统复制）
* 通用 RHEL 文档
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)（高可用性附加产品概述）
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)（高可用性附加产品管理）
  * [High Availability Add-On 参考](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [RHEL 高可用性群集的支持策略 - sbd 和 fence_sbd](https://access.redhat.com/articles/2800691)
* Azure 特定的 RHEL 文档：
  * [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341)（RHEL 高可用性群集的支持策略 - Microsoft Azure 虚拟机作为群集成员）
  * [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491)（在 Microsoft Azure 上安装和配置 Red Hat Enterprise Linux 7.4 [及更高版本] 高可用性群集）
  * [采用 RHEL 8 的注意事项 - 高可用性和群集](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/considerations_in_adopting_rhel_8/high-availability-and-clusters_considerations-in-adopting-rhel-8)
  * [在 RHEL 7.6 的 Pacemaker 中将 SAP S/4HANA ASCS/ERS 配置为 Standalone Enqueue Server 2 (ENSA2)](https://access.redhat.com/articles/3974941)
  * [Azure 上的 RHEL for SAP 产品/服务](https://access.redhat.com/articles/5456301)

## <a name="cluster-installation"></a>群集安装

![RHEL 上的 Pacemaker 概述](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> Red Hat 不支持软件仿真的监视程序。 Red Hat 不支持云平台上的 SBD。 有关详细信息，请参阅 [RHEL 高可用性群集的支持策略 - sbd 和 fence_sbd](https://access.redhat.com/articles/2800691)。
> Azure 上 Pacemaker Red Hat Enterprise Linux 群集唯一支持的隔离机制是 Azure 隔离代理。  

以下各项带有前缀 [A] - 适用于所有节点、[1] - 仅适用于节点 1，或 [2] - 仅适用于节点 2  。

1. [A] 注册。 如果使用已启用 RHEL SAP HA 的映像，则不需要执行此步骤。  

   注册虚拟机，将其附加到包含适用于 RHEL 7 的存储库的池。

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   通过将池附加到 Azure 市场 PAYG RHEL 映像，可以有效地对 RHEL 使用情况进行双重计费：一次是对 PAYG 映像进行计费，另一次是对附加池中的 RHEL 权利进行计费。 为了缓解这种情况，Azure 现在提供了 BYOS RHEL 映像。 有关详细信息，请参阅 [Red Hat Enterprise Linux 自带订阅 Azure 映像](../redhat/byos.md)。

1. [A] 启用 RHEL for SAP 存储库。 如果使用已启用 RHEL SAP HA 的映像，则不需要执行此步骤。  

   为了安装所需的包，启用以下存储库。

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** 安装 RHEL HA 附加产品

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > 如果资源停止故障或者群集节点无法互相通信，我们建议客户使用以下 Azure 隔离代理版本（或更高版本）以获取更快的故障转移时间：  
   > RHEL 7.7 或更高版本使用最新可用版本的 fence-agents 包  
   > RHEL 7.6：fence-agents-4.2.1-11.el7_6.8  
   > RHEL 7.5：fence-agents-4.0.11-86.el7_5.8  
   > RHEL 7.4：fence-agents-4.0.11-66.el7_4.12  
   > 有关详细信息，请参阅 [Azure VM running as a RHEL High Availability cluster member take a very long time to be fenced, or fencing fails / times-out before the VM shuts down](https://access.redhat.com/solutions/3408711)（隔离作为 RHEL 高可用性群集成员运行的 Azure VM 需要很长时间，或者在 VM 关闭之前隔离失败/超时）。

   检查 Azure 隔离代理的版本。 如有必要，请将其更新为上述版本或更高版本。

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > 如果需要使用自定义角色更新 Azure 隔离代理，请确保更新自定义角色来将关闭操作包含在内。 有关详细信息，请参阅[为隔离代理创建自定义角色](#1-create-a-custom-role-for-the-fence-agent)。  

1. [A] 设置主机名称解析

   可以使用 DNS 服务器，或修改所有节点上的 /etc/hosts。 此示例演示如何使用 /etc/hosts 文件。
   请替换以下命令中的 IP 地址和主机名。  

   >[!IMPORTANT]
   > 如果在群集配置中使用主机名，则必须具有可靠的主机名解析。 如果名称不可用，可能导致故障转移延迟，群集通信就会失败。
   > 使用 /etc/hosts 的好处是群集可以独立于 DNS（也可能会成为单一故障点）。  

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   将以下行插入 /etc/hosts。 根据环境更改 IP 地址和主机名

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. [A] 将 hacluster 密码更改为相同的密码

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** 为 Pacemaker 添加防火墙规则

   向群集节点之间的所有群集通信添加以下防火墙规则。

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** 启用基本群集服务

   运行以下命令以启用并启动 Pacemaker 服务。

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** 创建 Pacemaker 群集

   运行以下命令以验证节点并创建群集。 将令牌设置为 30000，以允许内存保留维护。 有关详细信息，请参阅这篇[适用于 Linux][virtual-machines-linux-maintenance] 的文章。  
   
   如果在 RHEL 7.x 上构建群集，请使用以下命令：  
   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all
   </code></pre>

   如果在 RHEL 8.X 上构建群集，请使用以下命令：  
   <pre><code>sudo pcs host auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> totem token=30000
   sudo pcs cluster start --all
   </code></pre>

   执行以下命令验证群集状态：  
   <pre><code> # Run the following command until the status of both nodes is online
   sudo pcs status
   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. [A] 设置预期投票。 
   
   <pre><code># Check the quorum votes 
    pcs quorum status
    # If the quorum votes are not set to 2, execute the next command
    sudo pcs quorum expected-votes 2
   </code></pre>

   >[!TIP]
   > 如果构建多节点群集（即群集的节点超过两个），请勿将投票设置为 2。    

1. [1] 允许并发隔离操作

   <pre><code>sudo pcs property set concurrent-fencing=true
   </code></pre>

## <a name="create-stonith-device"></a>创建 STONITH 设备

STONITH 设备使用服务主体对 Microsoft Azure 授权。 请按照以下步骤创建服务主体。

1. 转到 <https://portal.azure.com>
1. 打开“Azure Active Directory”边栏选项卡  
   转到“属性”并记下目录 ID。 这是“租户 ID”。
1. 单击“应用注册”
1. 单击“新建注册”
1. 输入名称，选择“仅限此组织目录中的帐户” 
2. 选择“Web”作为应用程序类型，输入登录 URL（例如 http:\//localhost），然后单击“添加”  
   不会使用登录 URL，可为它输入任何有效的 URL
1. 选择“证书和机密”，然后单击“新建客户端机密”
1. 输入新密钥的说明，选择“永不过期”，并单击“添加”
1. 记下该值。 此值用作服务主体的 **密码**
1. 选择“概述”。 记下应用程序 ID。 此 ID 用作服务主体的用户名（以下步骤中的“登录 ID”）

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** 为隔离代理创建自定义角色

默认情况下，服务主体无权访问 Azure 资源。 需要为服务主体授予启动和停止（关闭）群集所有虚拟机的权限。 如果尚未创建自定义角色，可以使用 [PowerShell](../../../role-based-access-control/role-assignments-powershell.md) 或 [Azure CLI](../../../role-based-access-control/role-assignments-cli.md) 来创建它

将以下内容用于输入文件。 你需要调整内容以适应你的订阅，也就是说，将 c276fc76-9cd4-44c9-99a7-4fd71546436e 和 e91d47c4-76f3-4271-a796-21b4ecfe3624 替换为你的订阅的 ID。 如果只有一个订阅，请删除 AssignableScopes 中的第二个条目。

```json
{
      "Name": "Linux Fence Agent Role",
      "description": "Allows to power-off and start virtual machines",
      "assignableScopes": [
              "/subscriptions/e663cc2d-722b-4be1-b636-bbd9e4c60fd9",
              "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
      ],
      "actions": [
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/powerOff/action",
              "Microsoft.Compute/virtualMachines/start/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** 向服务主体分配自定义角色

将在最后一章中创建的自定义角色“Linux 隔离代理角色”分配给服务主体。 不要再使用“所有者”角色！ 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../../role-based-access-control/role-assignments-portal.md)。   
请确保为两个群集节点分配角色。    
      
### <a name="1-create-the-stonith-devices"></a>**[1]** 创建 STONITH 设备

编辑虚拟机的权限后，可以在群集中配置 STONITH 设备。

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

> [!NOTE]
> 如果 RHEL 主机名和 Azure VM 名称不相同，则命令仅需要“pcmk_host_map”选项。 以 hostname:vm-name 格式指定映射。
> 请参阅命令中的粗体部分。 有关详细信息，请参阅[应使用哪种格式在 pcmk_host_map 中指定节点到 stonith 设备的映射](https://access.redhat.com/solutions/2619961)

对于 RHEL 7.X，请使用以下命令配置隔离设备：    
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 pcmk_delay_max=15 \
op monitor interval=3600
</code></pre>

对于 RHEL 8.X，请使用以下命令配置隔离设备：  
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm username="<b>login ID</b>" password="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 pcmk_delay_max=15 \
op monitor interval=3600
</code></pre>

> [!TIP]
> 仅在双节点 Pacemaker 群集中配置 `pcmk_delay_max` 属性。 若要详细了解如何在双节点 Pacemaker 群集中防止隔离争用，请参阅[在双节点群集中延迟隔离以防止“隔离死亡”方案的隔离争用](https://access.redhat.com/solutions/54829)。 
 

> [!IMPORTANT]
> 对监视和隔离操作进行反序列化。 因此，如果存在运行时间较长的监视操作和同时发生的隔离事件，则群集故障转移不会延迟，因为监视操作已经在运行。  

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** 启用 STONITH 设备

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

> [!TIP]
>Azure 隔离代理要求与[使用标准 ILB 的 VM 的公共终结点连接](./high-availability-guide-standard-load-balancer-outbound-connections.md)中所述的公共终结点建立出站连接并提供可能的解决方案。  


## <a name="optional-stonith-configuration"></a>可选的 STONITH 配置  

> [!TIP]
> 本部分仅适用于需要配置特殊隔离设备 `fence_kdump` 的情况。  

如果需要在 VM 内收集诊断信息，则根据隔离代理 `fence_kdump` 配置额外的 STONITH 设备可能会很有用。 在调用其他隔离方法之前，`fence_kdump` 代理可检测进入 kdump 故障恢复的节点，并允许故障恢复服务完成。 请注意，在使用 Azure VM 时，`fence_kdump` 无法替代传统的隔离机制，例如 Azure 隔离代理。   

> [!IMPORTANT]
> 请注意，当 `fence_kdump` 被配置为第一级 stonith 时，它将分别引入隔离操作延迟和应用程序资源故障转移延迟。  
> 
> 如果成功检测到故障转储，隔离将延迟到故障恢复服务完成。 如果故障节点无法访问或未响应，则将按配置的迭代次数和 `fence_kdump` 超时确定的时间延迟隔离。 有关更多详细信息，请参阅[如何在 Red Hat Pacemaker 群集中配置 fence_kdump](https://access.redhat.com/solutions/2876971)。  
> 建议的 fence_kdump 超时时间可能需要根据具体环境进行调整。
>     
> 建议仅在需要收集 VM 内的诊断信息时才配置 `fence_kdump` stonith，并且始终与 Azure 隔离代理等传统隔离方法结合使用。   

以下 Red Hat KB 包含有关配置 `fence_kdump` stonith 的重要信息：

* [如何在 Red Hat Pacemaker 群集中配置 fence_kdump](https://access.redhat.com/solutions/2876971)
* [如何使用 Pacemaker 在 RHEL 群集中配置/管理 STONITH 级别](https://access.redhat.com/solutions/891323)
* [在 kexec-tools 版本低于 2.0.14 的 RHEL 6 或 7 HA 群集中，fence_kdump 失败并显示“X 秒后超时”](https://access.redhat.com/solutions/2388711)
* 若要了解如何更改默认超时，请参阅[如何配置 kdump 以与 RHEL 6、7、8 HA 加载项一起使用](https://access.redhat.com/articles/67570)
* 若要了解如何在使用 `fence_kdump` 时减少故障转移延迟，请参阅[添加 fence_kdump 配置时是否可减少故障转移的预期延迟](https://access.redhat.com/solutions/5512331)
   
除了 Azure 隔离代理配置，还可执行以下可选步骤来添加 `fence_kdump` 作为第一级 STONITH 配置。 


1. **[A]** 验证 kdump 是否处于活动状态并已配置。  
    ```
    systemctl is-active kdump
    # Expected result
    # active
    ```
2. **[A]** 安装 `fence_kdump` 隔离代理。  
    ```
    yum install fence-agents-kdump
    ```
3. **[1]** 在群集中创建 `fence_kdump` stonith 设备。   
    <pre><code>
    pcs stonith create rsc_st_kdump fence_kdump pcmk_reboot_action="off" <b>pcmk_host_list="prod-cl1-0 prod-cl1-1</b>" timeout=30
    </code></pre>

4. **[1]** 配置 stonith 级别，以便首先启用 `fence_kdump` 隔离机制。  
    <pre><code>
    pcs stonith create rsc_st_kdump fence_kdump pcmk_reboot_action="off" <b>pcmk_host_list="prod-cl1-0 prod-cl1-1</b>"
    pcs stonith level add 1 <b>prod-cl1-0</b> rsc_st_kdump
    pcs stonith level add 1 <b>prod-cl1-1</b> rsc_st_kdump
    pcs stonith level add 2 <b>prod-cl1-0</b> rsc_st_azure
    pcs stonith level add 2 <b>prod-cl1-1</b> rsc_st_azure
    # Check the stonith level configuration 
    pcs stonith level
    # Example output
    # Target: <b>prod-cl1-0</b>
    # Level 1 - rsc_st_kdump
    # Level 2 - rsc_st_azure
    # Target: <b>prod-cl1-1</b>
    # Level 1 - rsc_st_kdump
    # Level 2 - rsc_st_azure
    </code></pre>

5. **[A]** 允许 `fence_kdump` 所需的端口通过防火墙
    ```
    firewall-cmd --add-port=7410/udp
    firewall-cmd --add-port=7410/udp --permanent
    ```

6. **[A]** 确保 `initramfs` 映像文件包含 `fence_kdump` 和 `hosts` 文件。 有关详细信息，请参阅[如何在 Red Hat Pacemaker 群集中配置 fence_kdump](https://access.redhat.com/solutions/2876971)。   
    ```
    lsinitrd /boot/initramfs-$(uname -r)kdump.img | egrep "fence|hosts"
    # Example output 
    # -rw-r--r--   1 root     root          208 Jun  7 21:42 etc/hosts
    # -rwxr-xr-x   1 root     root        15560 Jun 17 14:59 usr/libexec/fence_kdump_send
    ```

7. **[A]** 在 `/etc/kdump.conf` 中执行 `fence_kdump_nodes` 配置，避免在某些 `kexec-tools` 版本中 `fence_kdump` 失败并显示超时。 有关详细信息，请参阅 [kexec-tools 2.0.15 或更高版本没有指定 fence_kdump_nodes 时，fence_kdump 超时](https://access.redhat.com/solutions/4498151)和[在 kexec-tools 版本低于 2.0.14 的 RHEL 6 或 7 高可用性群集中，fence_kdump 失败并显示“X 秒后超时”](https://access.redhat.com/solutions/2388711)。 双节点群集的示例配置如下所示。 在 `/etc/kdump.conf` 中进行更改后，必须重新生成 kdump 映像。 这可通过重启 `kdump` 服务来实现。  

    <pre><code>
    vi /etc/kdump.conf
    # On node <b>prod-cl1-0</b> make sure the following line is added
    fence_kdump_nodes  <b>prod-cl1-1</b>
    # On node <b>prod-cl1-1</b> make sure the following line is added
    fence_kdump_nodes  <b>prod-cl1-0</b>

    # Restart the service on each node
    systemctl restart kdump
    </code></pre>

8. 通过使节点故障来测试配置。 有关详细信息，请参阅[如何在 Red Hat Pacemaker 群集中配置 fence_kdump](https://access.redhat.com/solutions/2876971)。  

    > [!IMPORTANT]
    > 如果群集已在高效使用中，请相应地计划测试，因为节点故障会影响应用程序。   

    ```
    echo c > /proc/sysrq-trigger
    ```
## <a name="next-steps"></a>后续步骤

* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* 若要了解如何在 Azure VM 上建立 SAP HANA 高可用性以及规划灾难恢复，请参阅 [Azure 虚拟机 (VM) 上的 SAP HANA 高可用性][sap-hana-ha]
