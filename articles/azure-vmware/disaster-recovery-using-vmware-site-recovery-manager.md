---
title: 使用 VMware Site Recovery Manager 部署灾难恢复
description: 在 Azure VMware 解决方案私有云中使用 VMware Site Recovery Manager (SRM) 部署灾难恢复。
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: 7c4f518623801e8cb489452dbeb3be76e1104e62
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122608063"
---
# <a name="deploy-disaster-recovery-with-vmware-site-recovery-manager"></a>使用 VMware Site Recovery Manager 部署灾难恢复

本文介绍如何为本地 VMware 虚拟机 (VM) 或基于 Azure VMware 解决方案的 VM 实施灾难恢复。  本文中的解决方案将 [VMware Site Recovery Manager (SRM)](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) 和 vSphere 复制与 Azure VMware 解决方案结合使用。 SRM 和复制服务器的实例部署在受保护站点和恢复站点上。       

SRM 是一种灾难恢复解决方案，旨在在发生灾难时，最大限度地减少 Azure VMware 解决方案环境中虚拟机的停机时间。 SRM 自动执行和协调故障转移和故障回复，确保在灾难中最大限度地减少停机时间。 此外，内置的无中断测试可确保满足恢复时间目标。 总体而言，SRM 通过自动化功能简化了管理，确保了快速且高度可预测的恢复时间。  

vSphere 复制是 VMware 用于 vSphere VM 的一种基于虚拟机监控程序的复制技术。 它可以保护 VM 免受部分或全部站点故障的影响。 此外，它还通过独立于存储、以 VM 为中心的复制简化了 DR 保护。 vSphere 复制基于每个 VM 进行配置，从而可以更好地控制要复制的 VM。

在本文中，你将为本地 VMware 虚拟机 (VM) 或基于 Azure VMware 解决方案的 VM 实施灾难恢复。


## <a name="supported-scenarios"></a>支持的方案

SRM 可帮助你在受保护的 vCenter 服务器站点和恢复 vCenter 服务器站点之间规划、测试和运行 VM 恢复。 你可以在以下两种 DR 方案中结合使用 SRM 和 Azure VMware 解决方案： 

- 本地 VMware 到 Azure VMware 解决方案私有云的灾难恢复 
- 主要 Azure VMware 解决方案到辅助 Azure VMware 解决方案私有云的灾难恢复 

该图显示了主要 Azure VMware 解决方案到辅助 Azure VMware 解决方案的部署。

:::image type="content" source="media/vmware-srm-vsphere-replication/vmware-site-recovery-manager-diagram.png" alt-text="显示 Azure VMware 解决方案中的 VMware Site Recovery Manager (SRM) 灾难恢复解决方案的图表。" border="false":::

你可以使用 SRM 来实施不同类型的恢复，例如：

- 当主要和辅助 Azure VMware 解决方案站点都在运行且功能齐全时，开始进行 **计划迁移**。 它以有序的方式将虚拟机从受保护的站点迁移到恢复站点，在这种情况下，迁移工作负载时不会丢失任何数据。 

- 当受保护的 Azure VMware 解决方案站点意外脱机时，可以调用使用 SRM 的 **灾难恢复**。 Site Recovery Manager 使用复制机制协调恢复过程，以最大限度地减少数据丢失和系统停机时间。

   在 Azure VMware 解决方案中，将 SRM 与 vSphere 复制结合使用只能保护主机上的单个 VM。

- **双向保护** 使用一组配对的 SRM 站点对 VM 进行双向保护。 每个站点可以同时作为受保护站点和恢复站点，但面向一组不同的 VM。

>[!IMPORTANT]
>Azure VMware 解决方案不支持： 
>
>- 基于阵列的复制和存储策略保护组 
>- VVOL 保护组 
>- 使用 SRM 命令行工具自定义 SRM IP
>- 一对多和多对一拓扑 
>- 自定义 SRM 插件标识符或扩展 ID


## <a name="deployment-workflow"></a>部署工作流

该工作流图显示了从主要 Azure VMware 解决方案到辅助解决方案的工作流。 此外，它还显示了在 Azure 门户和 Azure VMware 解决方案的 VMware 环境中采取的步骤，以实现对 VM 的端到端保护。 

:::image type="content" source="media/vmware-srm-vsphere-replication/site-recovery-manager-workflow.png" alt-text="显示 Azure VMware 解决方案上 VMware Site Recovery Manager 的部署工作流的图表。" border="false":::

## <a name="prerequisites"></a>必备条件

### <a name="scenario-on-premises-to-azure-vmware-solution"></a>方案：本地到 Azure VMware 解决方案 

- 部署为次要区域的 Azure VMware 解决方案私有云。

- 对本地 SRM 和虚拟云设备的 [DNS 解析](configure-dns-azure-vmware-solution.md)。

   >[!NOTE]
   >对于 2021 年 7 月 1 日或之后创建的私有云，可以配置专用 DNS 解析。 对于 2021 年 7 月 1 日之前创建的需要专用 DNS 解析的私有云，请创建[支持请求](https://rc.portal.azure.com/#create/Microsoft.Support)以请求专用 DNS 配置。

- 本地和 Azure VMware 解决方案之间的 ExpressRoute 连接 - 2 Gbps。

### <a name="scenario-primary-azure-vmware-solution-to-secondary"></a>方案：主要 Azure VMware 解决方案到辅助解决方案

- Azure VMware 解决方案私有云必须部署在主要和次要区域中。

   :::image type="content" source="media/vmware-srm-vsphere-replication/two-private-clouds-different-regions.png" alt-text="显示不同区域中的两个 Azure VMware 解决方案私有云的屏幕截图。":::
 
- 源和目标 Azure VMware 解决方案私有云之间的连接，如 ExpressRoute Global Reach。

   :::image type="content" source="media/vmware-srm-vsphere-replication/global-reach-connectity-to-on-premises.png" alt-text="显示源和目标私有云之间的连接的屏幕截图。":::

 
## <a name="install-srm-in-azure-vmware-solution"></a>在 Azure VMware 解决方案中安装 SRM

1. 在本地数据中心安装 VMware SRM 和 vSphere。

   >[!NOTE]
   >使用[每个 PSC 一个 vCenter 服务器实例的双站点拓扑](https://docs.vmware.com/en/Site-Recovery-Manager/8.4/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)部署模型。 此外，请确保已打开[所需的 vSphere 复制网络端口](https://kb.VMware.com/s/article/2087769)。

1. 在 Azure VMware 解决方案私有云中，在“管理”下，选择“加载项” > “灾难恢复”。

   Azure VMware 解决方案私有云中的默认 CloudAdmin 用户没有足够的权限来安装 VMware SRM 或 vSphere 复制。 安装过程涉及[先决条件](#prerequisites)部分中所述的多个步骤。 相反，你可以将 VMware SRM 与 vSphere 复制一起作为 Azure VMware 解决方案私有云中的加载项服务进行安装。

   :::image type="content" source="media/VMware-srm-vsphere-replication/disaster-recovery-add-ons.png" alt-text="Azure VMware 解决方案私有云将 VMware SRM 与 vSphere 复制一起作为加载项进行安装的屏幕截图" border="true" lightbox="media/VMware-srm-vsphere-replication/disaster-recovery-add-ons.png":::

1. 从“灾难恢复解决方案”下拉列表中，选择“VMware Site Recovery Manager (SRM) – vSphere 复制”。 

   :::image type="content" source="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-add-on.png" alt-text="显示“加载项”下“灾难恢复”选项卡（选择了“VMware Site Recovery Manager (SRM) – vSphere 复制”）的屏幕截图。" border="true" lightbox="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-add-on.png":::

1. 提供许可证密钥，选择同意条款和条件，然后选择“安装”。

   >[!NOTE]
   >如果未提供许可证密钥，则会在评估模式下安装 SRM。 该许可证仅用于启用 VMware SRM。
   
   :::image type="content" source="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-licence.png" alt-text="显示“加载项”下“灾难恢复”选项卡（选择了“许可证密钥”字段）的屏幕截图。" border="true" lightbox="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-licence.png":::


## <a name="install-the-vsphere-replication-appliance"></a>安装 vSphere 复制设备

成功安装 SRM 设备后，需要安装 vSphere 复制设备。 每个复制服务器最多可容纳 200 个受保护的 VM。 请根据需要进行横向缩减或横向扩展。 

1. 从“灾难恢复”选项卡的“复制使用”下拉菜单中，选择“vSphere 复制”。

   :::image type="content" source="media/vmware-srm-vsphere-replication/vsphere-replication-1.png" alt-text="显示为“复制使用”选项选择“vSphere 复制”的屏幕截图。":::

1. 根据要保护的 VM 数量，移动 vSphere 服务器滑块以指示所需的复制服务器数量。 然后选择“安装”。

   :::image type="content" source="media/vmware-srm-vsphere-replication/vsphere-replication-2.png" alt-text="显示如何增加或减少复制服务器数量的屏幕截图。":::

1. 安装后，验证是否安装了 SRM 和 vSphere 复制设备。

   >[!TIP]
   >“卸载”按钮表示当前安装了 SRM 和 vSphere 复制设备。

   :::image type="content" source="media/vmware-srm-vsphere-replication/vsphere-replication-3.png" alt-text="显示 SRM 和复制设备均已安装的屏幕截图。":::
  

## <a name="configure-site-pairing-in-vcenter"></a>在 vCenter 中配置站点配对

安装 VMware SRM 和 vSphere 复制后，需要在 vCenter 中完成配置和站点配对。

1. 以 cloudadmin@vsphere.local 的身份登录 vCenter。

1. 导航到“Site Recovery”，检查 vSphere 复制和 VMware SRM 的状态，然后选择“打开 Site Recovery”以启动客户端。

   :::image type="content" source="media/vmware-srm-vsphere-replication/open-site-recovery.png" alt-text="显示 vSphere 客户端的屏幕截图，其中 vSphere 复制和 Site Recovery Manager 的安装状态为“正常”。" border="true":::


1. 在打开的新选项卡中的 Site Recovery (SR) 客户端中选择“新建站点对”。
   
   :::image type="content" source="media/vmware-srm-vsphere-replication/new-site-pair.png" alt-text="显示 vSphere 客户端的屏幕截图，其中为 Site Recovery 选择了“新建站点对”按钮。" border="true":::

1. 输入远程站点详细信息，然后选择“下一步”。

   >[!NOTE]
   >Azure VMware 解决方案私有云使用嵌入式平台服务控制器 (PSC) 运行，因此只能选择一个本地 vCenter。 如果远程 vCenter 使用嵌入式平台服务控制器 (PSC)，请使用 vCenter 的 FQDN（或其 IP 地址）和端口来指定 PSC。 
   >
   >远程用户必须有足够的权限来执行配对。 若要确保这一点，一种简单的方法是向该用户授予远程 vCenter 中的 VRM 管理员和 SRM 管理员角色。 对于远程 Azure VMware 解决方案私有云，cloudadmin 配置有这些角色。

   :::image type="content" source="media/vmware-srm-vsphere-replication/pair-the-sites-specify-details.png" alt-text="显示新站点对的站点详细信息的屏幕截图。" border="true" lightbox="media/vmware-srm-vsphere-replication/pair-the-sites-specify-details.png":::

1. 选择“连接”以接受远程 vCenter 的证书。

   此时，客户端应发现两端的 VRM 和 SRM 设备作为要配对的服务。

1. 选择要配对的设备，然后选择“下一步”。
   
   :::image type="content" source="media/vmware-srm-vsphere-replication/pair-the-sites-new-site.png" alt-text="显示新站点对的 vCenter 服务器和服务详细信息的屏幕截图。" border="true" lightbox="media/vmware-srm-vsphere-replication/pair-the-sites-new-site.png":::

1. 选择“连接”以接受远程 VMware SRM 和远程 vCenter（再一次）的证书。

1. 选择“连接”以接受本地 VMware SRM 和本地 vCenter 的证书。

1. 检查设置，然后选择“完成”。

   如果成功，客户端会显示另一个配对面板。 但是，如果失败，将报告警报。

1. 在底部的右上角，选择双向上箭头展开面板，以显示“最新任务”和“警报”。

   >[!NOTE]
   >SR 客户端有时需要很长时间才能刷新。 如果某个操作似乎耗时过长或出现“停滞”，请选择菜单栏上的刷新图标。 

1. 选择“查看详细信息”以打开远程站点配对面板，这会打开一个用于登录远程 vCenter 的对话框。

   :::image type="content" source="media/vmware-srm-vsphere-replication/view-details-remote-pairing.png" alt-text="显示 Site Recovery Manager 和 vSphere 复制的新站点对详细信息的屏幕截图。" border="true" lightbox="media/vmware-srm-vsphere-replication/view-details-remote-pairing.png":::

1. 输入具有足够权限进行复制和站点恢复的用户名，然后选择“登录”。 

   对于配对，登录名（通常是不同的用户）是建立配对的一次性操作。 每次启动 SR 客户端以进行配对时，客户端都需要此登录名。

   >[!NOTE] 
   >具有足够权限的用户应在远程 vCenter 中被授予“VRM 管理员”和“SRM 管理员”角色。 用户还应有权访问远程 vCenter 清单，例如文件夹和数据存储。 对于远程 Azure VMware 解决方案私有云，cloudadmin 用户具有适当的权限和访问权限。 
   
   :::image type="content" source="media/vmware-srm-vsphere-replication/sign-into-remote-vcenter.png" alt-text="显示 vCenter 服务器凭据的屏幕截图。" border="true":::

   你将看到一条警告消息，指示本地 VRM 中的嵌入式 VRS 未运行。  这是因为 Azure VMware 解决方案未在 Azure VMware 解决方案私有云中使用嵌入式 VRS，  而是使用 VRS 设备。 

   :::image type=" content" source=" media/vmware-srm-vsphere-replication/pair-the-sites-summary.png" alt-text="显示 Site Recovery Manager 和 vSphere 复制的站点对摘要的屏幕截图。" border="true" lightbox="media/vmware-srm-vsphere-replication/pair-the-sites-summary.png":::

## <a name="srm-protection-reprotection-and-failback"></a>SRM 保护、重新保护和故障回复

创建站点配对后，请按照下面提到的 VMware 文档，从 Azure 门户对 VM 进行端到端保护。

- [将 vSphere 复制与 Site Recovery Manager 结合使用 (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-2C77C830-892D-45FF-BA4F-80AC10085DBE.html)

- [基于阵列的复制保护组和 vSphere 复制保护组的清单映射 (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-2E2B4F84-D388-456B-AA3A-57FA8D47063D.html)

- [关于占位虚拟机 (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-EFE73B20-1C68-4D2C-8C86-A6E3C6214F07.html)

- [vSphere 复制保护组 (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-CCF2E768-736E-4EAA-B3BE-50182635BC49.html)

- [创建、测试和运行恢复计划 (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-AF6BF11B-4FB7-4543-A873-329FDF1524A4.html)

- [配置恢复计划 (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-FAC499CE-2994-46EF-9164-6D97EAF52C68.html)

- [为虚拟机自定义 IP 属性 (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-25B33730-14BE-4268-9D88-1129011AFB39.html)

- [Site Recovery Manager 如何使用 vSphere 复制重新保护虚拟机 (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-1DE0E76D-1BA7-44D8-AEA2-5B2218E219B1.html)

- [执行故障回复 (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-556E84C0-F8B7-4F9F-AAB0-0891C084EDE4.html)



## <a name="ongoing-management-of-your-srm-solution"></a>持续管理 SRM 解决方案

虽然 Microsoft 旨在简化 Azure VMware 解决方案私有云上的 VMware SRM 和 vSphere 复制安装，但你需要负责管理许可证和灾难恢复解决方案的日常操作。 

## <a name="scale-limitations"></a>缩放限制

每个私有云都有缩放限制。

| 配置 | 限制 |
| --- | --- |
| 受保护虚拟机的数量  | 1000  |
| 每个恢复计划的虚拟机数量  | 1000  |
| 每个恢复计划的保护组数量  | 250  |
| RPO 值  | 5 分钟或更高*  |
| 每个保护组的虚拟机总数  | 500  |
| 恢复计划总数  | 250  |

\*有关恢复点目标 (RPO) 低于 15 分钟的信息，请参阅《VSphere 复制管理指南》中的 [5 分钟恢复点目标的工作原理](https://docs.vmware.com/en/vSphere-Replication/8.3/com.vmware.vsphere.replication-admin.doc/GUID-9E17D567-A947-49CD-8A84-8EA2D676B55A.html)。



## <a name="srm-licenses"></a>SRM 许可证

你可以使用评估许可证或生产许可证安装 VMware SRM。  评估许可证的有效期为 60 天。 评估期过后，需要获得 VMware SRM 的生产许可证。 

不能将预先存在的本地 VMware SRM 许可证用于 Azure VMware 解决方案私有云。 请与销售团队和 VMware 合作，获取新的基于期限的 VMware SRM 生产许可证。 

获取 SRM 的生产许可证后，就可以通过 Azure VMware 解决方案门户使用新的生产许可证来更新 SRM。 


## <a name="uninstall-srm"></a>卸载 SRM 

如果不再需要 SRM，则必须以干净的方式卸载它。 在卸载 SRM 之前，必须以正确的顺序从两个站点中删除所有 SRM 配置。 如果在卸载 SRM 之前没有删除所有配置，某些 SRM 组件（例如占位 VM）可能会保留在 Azure VMware 解决方案基础结构中。

1. 在 vSphere 客户端或 vSphere Web 客户端中，选择“Site Recovery” > “打开 Site Recovery”。

2. 在“Site Recovery”主页选项卡上，选择一个站点对，然后选择“查看详细信息”。

3. 选择“恢复计划”选项卡，右键单击恢复计划并选择“删除”。

   >[!NOTE]
   >无法删除正在运行的恢复计划。

4. 选择“保护组”选项卡，选择一个保护组，然后选择“虚拟机”选项卡。

5. 突出显示所有虚拟机，右键单击并选择“删除保护”。

   从 VM 中删除保护会从恢复站点中删除占位 VM。 对所有保护组重复此操作。

6. 在“保护组”选项卡中，右键单击保护组并选择“删除”。

   >[!NOTE] 
   >无法删除包含在恢复计划中的保护组。 无法删除包含仍配置了保护的虚拟机的 vSphere 复制保护组。

7. 选择“站点对” > “配置”并删除所有清单映射。

   a. 选择每个“网络映射”、“文件夹映射”和“资源映射”选项卡。

   b. 在每个选项卡中，选择站点，右键单击映射并选择“删除”。

8. 对于这两个站点，选择“占位数据存储”，右键单击占位数据存储，然后选择“删除”。

9. 选择“站点对” > “摘要”，然后选择“中断站点对”。

   >[!NOTE] 
   >中断站点配对时，会删除与将 Site Recovery Manager 注册到远程站点上的 Site Recovery Manager、vCenter 服务器和 Platform Services Controller 相关的所有信息。

10. 在私有云中，在“管理”下，选择“加载项” > “灾难恢复”，然后选择“卸载复制设备”。

11. 卸载复制设备后，从“灾难恢复”选项卡中选择“为 Site Recovery Manager 卸载”。

12. 在辅助 Azure VMware 解决方案站点上重复这些步骤。


## <a name="support"></a>支持 

VMware SRM 是 VMware 的灾难恢复解决方案。  

Microsoft 仅支持在 Azure VMware 解决方案中安装/卸载 SRM 和 vSphere Replication Manager，以及纵向扩展/纵向缩减 vSphere 复制设备。 

对于所有其他问题，例如配置和复制，请联系 VMware 获取支持。

VMware 和 Microsoft 支持团队将根据需要相互交流，以排查 Azure VMware 解决方案上的 SRM 问题。


## <a name="references"></a>参考

- [VMware Site Recovery Manager 文档](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
- [VMware Site Recovery Manager 8.3 的兼容性矩阵](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/rn/srm-compat-matrix-8-3.html)
- [VMware SRM 8.3 发行说明](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/rn/srm-releasenotes-8-3.html)
- [VMware vSphere 复制文档](https://docs.vmware.com/en/vSphere-Replication/index.html)
- [vSphere 复制 8.3 的兼容性矩阵](https://docs.vmware.com/en/vSphere-Replication/8.3/rn/vsphere-replication-compat-matrix-8-3.html)
- [Site Recovery Manager 8.3 的操作限制](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-3AD7D565-8A27-450C-8493-7B53F995BB14.html)
- [vSphere 复制 8.3 的操作限制](https://docs.vmware.com/en/vSphere-Replication/8.3/com.vmware.vsphere.replication-admin.doc/GUID-E114BAB8-F423-45D4-B029-91A5D551AC47.html)
- [计算 vSphere 复制的带宽](https://docs.vmware.com/en/vSphere-Replication/8.3/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
- [SRM 安装和配置](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-B3A49FFF-E3B9-45E3-AD35-093D896596A0.html)
- [vSphere 复制管理](https://docs.vmware.com/en/vSphere-Replication/8.3/com.vmware.vsphere.replication-admin.doc/GUID-35C0A355-C57B-430B-876E-9D2E6BE4DDBA.html)
- [SRM 安装的先决条件和最佳做法](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
- [SRM 的网络端口](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-499D3C83-B8FD-4D4C-AE3D-19F518A13C98.html)
- [vSphere 复制的网络端口](https://kb.vmware.com/s/article/2087769)
