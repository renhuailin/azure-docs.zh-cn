---
title: 教程 - 使用 Azure Site Recovery 为 Linux VM 设置灾难恢复
description: 了解如何使用 Azure Site Recovery 服务为 Linux VM 设置到其他 Azure 区域的灾难恢复。
author: rayne-wiselman
ms.service: virtual-machines
ms.collection: linux
ms.subservice: recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: fa43f40d4849a8e773241fa17a1e1787ce86a8ff
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564741"
---
# <a name="tutorial-set-up-disaster-recovery-for-linux-virtual-machines"></a>教程：为 Linux 虚拟机设置灾难恢复


本教程介绍如何为运行 Linux 的 Azure VM 设置灾难恢复。 在本文中，将学习以下内容：

> [!div class="checklist"]
> * 为 Linux VM 启用灾难恢复
> * 运行灾难恢复演练
> * 进行演练后停止复制 VM

为 VM 启用复制时，将在 VM 上安装 Site Recovery 出行服务扩展并将其注册到 [Azure Site Recovery](../../site-recovery/site-recovery-overview.md)。 在复制过程中，系统会将 VM 磁盘写入发送到源区域中的缓存存储帐户。 数据从那里发送到目标区域，并根据数据生成恢复点。  在灾难恢复过程中将 VM 故障转移到其他区域时，将使用恢复点来还原目标区域中的 VM。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="prerequisites"></a>先决条件

1. 检查 Azure 订阅是否允许在目标区域中创建 VM。 如果你刚刚创建了免费 Azure 帐户，那么你就是订阅的管理员，并拥有所需的权限。
2. 如果你不是订阅管理员，请联系管理员为你分配：
    - 虚拟机参与者内置角色或特定权限，用于：
        - 在所选虚拟网络中创建 VM。
        - 写入 Azure 存储帐户。
        - 写入 Azure 托管磁盘。
     - Site Recovery 参与者内置角色，用于管理保管库中的 Site Recovery 操作。 
3. 检查 Linux VM 是否正在运行[支持的操作系统](../../site-recovery/azure-to-azure-support-matrix.md#linux)。
4. 如果 VM 出站连接使用基于 URL 的代理，请确保其可以访问这些 URL。 不支持使用经过身份验证的代理。

    **Name** | **公有云** | **政府云** | **详细信息**
    --- | --- | --- | ---
    存储 | `*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`| 将数据从 VM 写入源区域中的缓存存储帐户。 
    Azure AD  | `login.microsoftonline.com` | `login.microsoftonline.us`| 授权并验证 Site Recovery 服务 URL。 
    复制 | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`  |VM 与 Site Recovery 服务进行通信。 
    服务总线 | `*.servicebus.windows.net` | `*.servicebus.usgovcloudapi.net` | VM 写入 Site Recovery 以监视和诊断数据。 

4. 如果使用网络安全组 (NSG) 限制 VM 的网络流量，请创建相关 NSG 规则，允许使用这些服务标记（IP 地址组）为 VM 进行出站连接 (HTTPS 443)。 首先尝试使用测试 NSG 上的规则。

    **标记** | **允许** 
    --- | --- 
    存储标记 | 允许将数据从 VM 写入缓存存储帐户。
    Azure AD 标记 | 允许访问与 Azure AD 对应的所有 IP 地址。
    EventsHub 标记 | 允许访问 Site Recovery 监视。
    AzureSiteRecovery 标记 | 允许在任何区域访问 Site Recovery 服务。
    GuestAndHybridManagement | 如果要自动升级在为复制启用的 VM 上运行的 Site Recovery 移动代理，请使用此标记。
5. 确保 VM 具有最新的根证书。 在 Linux VM 上，请遵循 Linux 分销商提供的指导，在 VM 上获取最新的受信任根证书和证书吊销列表。

## <a name="enable-disaster-recovery"></a>启用灾难恢复

1. 在 Azure 门户中，打开 VM 属性页。
2. 在“操作”中，选择“灾难恢复” 。
3. 在“基本信息” > “目标区域”中，选择要将 VM 复制到其中的区域 。 源区域和目标区域都必须属于同一 Azure Active Directory 租户。
4. 单击“查看 + 开始复制”。

    :::image type="content" source="./media/tutorial-disaster-recovery/disaster-recovery.png" alt-text="在 VM 属性“灾难恢复”页上启用复制。":::

5. 在“查看 + 开始复制”中，验证设置：

    - **目标设置**。 默认情况下，Site Recovery 镜像源设置以创建目标资源。
    - **存储设置 - 缓存存储帐户**。 Recovery 使用源区域中的一个存储帐户。 在将“源 VM 更改”复制到目标位置之前，会将其缓存在此帐户中。
    - **存储设置 - 副本磁盘**。 默认情况下，Site Recovery 在目标区域中创建副本托管磁盘，以生成存储类型一致（标准或高级）的源 VM 托管磁盘的镜像磁盘。
    - **复制设置**。 显示保管库详细信息，并指示由 Site Recovery 创建的恢复点将保留 24 小时。
    - **扩展设置**。 指示 Site Recovery 将管理所复制的 VM 上安装的 Site Recovery 出行服务扩展的更新。 指示的 Azure 自动化帐户管理更新过程。

    :::image type="content" source="./media/tutorial-disaster-recovery/settings-summary.png" alt-text="显示目标和复制设置的摘要的页面。":::

2. 选择“启动复制”。 开始部署，然后 Site Recovery 开始创建目标资源。 可以在通知中监视复制进度。

    :::image type="content" source="./media/tutorial-disaster-recovery/notifications.png" alt-text="复制进度通知。":::

## <a name="check-vm-status"></a>检查 VM 状态

复制作业完成后，可以检查 VM 复制状态。

1. 打开 VM 属性页。
2. 在“操作”中，选择“灾难恢复” 。
3. 展开“概要”部分，查看有关保管库、复制策略和目标设置的默认设置。
4. 在“运行状况和状态”中，获取有关 VM 的复制状态、代理版本、故障转移准备情况以及最新恢复点的信息。 

    :::image type="content" source="./media/tutorial-disaster-recovery/essentials.png" alt-text="VM 灾难恢复的“概要”视图。":::

5. 在“基础结构视图”中，获取源和目标 VM、托管磁盘和缓存存储帐户的直观概述。

    :::image type="content" source="./media/tutorial-disaster-recovery/infrastructure.png" alt-text="VM 灾难恢复的基础结构视觉对象地图。":::


## <a name="run-a-drill"></a>运行演练

运行演练，确保灾难恢复按预期方式进行。 运行测试故障转移时，它将创建 VM 的副本，但不会影响正在进行的复制或生产环境。

1. 在 VM 灾难恢复页面中，选择“测试故障转移”。
2. 在“测试故障转移”中，保留恢复点的默认“最新处理(低 RPO)”设置 。

   此选项提供最低的恢复点目标 (RPO)，通常可以在目标区域中最快地启动 VM。 它会首先处理已发送到 Site Recovery 服务的所有数据，为每个 VM 创建恢复点，然后将其故障转移到该恢复点。 触发故障转移后，此恢复点的所有数据将复制到 Site Recovery。

3. 选择故障转移后 VM 所处的虚拟网络。 

     :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-settings.png" alt-text="设置测试故障转移选项的页面。":::

4. 测试故障转移过程开始。 可在通知中监视进度。

    :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-notification.png" alt-text="测试故障转移通知。"::: 
    
   测试故障转移完成后，VM 在“概要”页面上处于“清理测试故障转移挂起”状态。 
  
## <a name="clean-up-resources"></a>清理资源

进行演练后，将通过 Site Recovery 自动清理 VM。 
 
1. 若要开始自动清理，请选择“清理测试故障转移”。

    :::image type="content" source="./media/tutorial-disaster-recovery/start-cleanup.png" alt-text="在“概要”页上启动清理。"::: 

6. 在“测试故障转移清理”中，键入要为故障转移记录的所有说明，然后选择”测试完成。删除测试故障转移虚拟机”。 然后选择“确定”  。

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test.png" alt-text="用于记录说明并删除测试 VM 的页面。"::: 

7. 删除过程开始。 可在通知中监视进度。

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test-notification.png" alt-text="监视删除测试 VM 的通知。"::: 


### <a name="stop-replicating-the-vm"></a>停止复制 VM

完成灾难恢复演练后，建议继续尝试完整的故障转移。 如果不想执行完整的故障转移，则可以禁用复制。 这样会执行以下操作：

- 从复制的计算机的 Site Recovery 列表中删除 VM。
- 停止对 VM 的 Site Recovery 计费。
- 自动清理源复制设置。

请按如下所述停止复制：

1. 在“VM 灾难恢复”页面中，选择“禁用复制”。
2. 在“禁用复制”中，选择要禁用复制的原因。 然后选择“确定”  。

    :::image type="content" source="./media/tutorial-disaster-recovery/disable-replication.png" alt-text="可禁用复制并提供原因的页面。"::: 


在复制过程中安装在 VM 上的 Site Recovery 扩展不会自动删除。 如果为 VM 禁用复制，且不想以后再次复制，则可以手动删除 Site Recovery 扩展，如下所示： 

1. 转到 VM >“设置” > “扩展” 。
2. 在“扩展”页中，为 Linux 选择每个“Microsoft.Azure.RecoveryServices”条目。
3. 在扩展的属性页中，选择“卸载”。


## <a name="next-steps"></a>后续步骤

在本教程中，为 Azure VM 配置了灾难恢复，并运行了灾难恢复演练。 现在，可以对 VM 执行完整的故障转移了。

> [!div class="nextstepaction"]
> [将 VM 故障转移到另一个区域](../../site-recovery/azure-to-azure-tutorial-dr-drill.md)
