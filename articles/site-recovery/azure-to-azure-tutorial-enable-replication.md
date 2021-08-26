---
title: 教程：使用 Azure Site Recovery 设置 Azure VM 灾难恢复
description: 在本教程中，使用 Site Recovery 服务将 Azure VM 的灾难恢复设置到其他 Azure 区域。
ms.topic: tutorial
ms.date: 07/25/2021
ms.custom: mvc
ms.openlocfilehash: 67dcbaf555de14c445f041b200ead48c4deac5ee
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742978"
---
# <a name="tutorial-set-up-disaster-recovery-for-azure-vms"></a>教程：为 Azure VM 设置灾难恢复

本教程介绍如何使用 [Azure Site Recovery](site-recovery-overview.md) 为 Azure VM 设置灾难恢复。 在本文中，学习如何：

> [!div class="checklist"]
> * 验证 Azure 设置和权限
> * 准备要复制的 VM
> * 创建恢复服务保管库
> * 启用 VM 复制

为 VM 启用复制以设置灾难恢复时，将在 VM 上安装 Site Recovery 出行服务扩展并使用 Azure Site Recovery 注册它。 在复制过程中，系统会将 VM 磁盘写入发送到源区域中的缓存存储帐户。 数据从那里发送到目标区域，并根据数据生成恢复点。 在灾难恢复过程中对 VM 进行故障转移时，将使用恢复点来还原目标区域中的 VM。

> [!NOTE]
> 教程提供了最简单的默认设置的说明。 如果要使用自定义的设置来设置 Azure VM 灾难恢复，请查看[这篇文章](azure-to-azure-how-to-enable-replication.md)。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

开始本教程前，请执行以下操作：

- [查看支持的区域](azure-to-azure-support-matrix.md#region-support)。 
- 需要一个或多个 Azure VM。 验证 [Windows](azure-to-azure-support-matrix.md#windows) 或 [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) VM 是否受支持。
- 查看 VM [计算](azure-to-azure-support-matrix.md#replicated-machines---compute-settings)、[存储](azure-to-azure-support-matrix.md#replicated-machines---storage)和[网络](azure-to-azure-support-matrix.md#replicated-machines---networking)要求。
- 本教程假定 VM 未加密。 如果要为加密的 VM 设置灾难恢复，请[按照本文](azure-to-azure-how-to-enable-replication-ade-vms.md)进行操作。

## <a name="check-azure-settings"></a>检查 Azure 设置

检查目标区域中的权限和设置。

### <a name="check-permissions"></a>检查权限

Azure 帐户需要某些权限才能创建恢复服务保管以及在目标区域中创建 VM。

- 如果你刚刚创建了免费的 Azure 订阅，则你是帐户管理员，无需执行任何其他操作。
- 如果你不是管理员，请联系管理员获取所需的权限。
    - **创建保管库**：针对订阅的管理员或所有者权限。
    - **管理保管库中的 Site Recovery 操作**：Site Recovery 参与者内置的 Azure 角色。
    - **在目标区域中创建 Azure VM**：内置参与者虚拟机角色或特定权限，用于：
        - 在所选虚拟网络中创建 VM。
        - 写入 Azure 存储帐户。
        - 写入 Azure 托管磁盘。

### <a name="verify-target-settings"></a>验证目标设置

在灾难恢复过程中，从源区域进行故障转移时，将在目标区域中创建 VM。

检查订阅在目标区域中是否有足够的资源。 需要能够创建大小与源区域中的 VM 匹配的 VM。 设置灾难恢复时，Site Recovery 会为目标 VM 选择相同的大小（或尽可能接近的大小）。


## <a name="prepare-vms"></a>准备 VM

确保 VM 具有出站连接和最新的根证书。


### <a name="set-up-vm-connectivity"></a>设置 VM 连接

要复制的 VM 需要出站网络连接。

> [!NOTE]
> Site Recovery 不支持使用身份验证代理来控制网络连接。

#### <a name="outbound-connectivity-for-urls"></a>URL 的出站连接

如果使用基于 URL 的防火墙代理来控制出站连接，请允许访问以下 URL：

| **名称**                  | 商用                               | 政府                                 | **说明** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| 存储                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | 允许将数据从 VM 写入源区域中的缓存存储帐户。 |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | 向 Site Recovery 服务 URL 提供授权和身份验证。 |
| 复制               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | 允许 VM 与 Site Recovery 服务进行通信。 |
| 服务总线               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | 允许 VM 写入 Site Recovery 监视和诊断数据。 |

#### <a name="outbound-connectivity-for-ip-address-ranges"></a>IP 地址范围的出站连接

如果使用网络安全组 (NSG) 来控制连接，请创建基于服务标记的 NSG 规则，这些规则允许这些[服务标记](../virtual-network/service-tags-overview.md#available-service-tags)（IP 地址组）的 HTTPS 出站连接到端口 443：

**标记** | **允许**
--- | ---
存储标记  |允许将数据从 VM 写入缓存存储帐户。
Azure AD 标记 | 允许访问与 Azure AD 对应的所有 IP 地址。
EventsHub 标记 | 允许访问 Site Recovery 监视。
AzureSiteRecovery 标记 | 允许在任何区域访问 Site Recovery 服务。
GuestAndHybridManagement 标记 | 如果要自动升级在为复制启用的 VM 上运行的 Site Recovery 移动代理，请使用此标记。

[详细了解](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)所需的标记和标记示例。

### <a name="verify-vm-certificates"></a>验证 VM 证书

检查 VM 是否具有最新的根证书。 否则，由于安全限制，无法使用 Site Recovery 注册 VM。

- **Windows VM**：在 VM 上安装所有最新的 Windows 更新，使所有受信任的根证书保留在该计算机上。 在离线环境中，请遵循 Windows 更新和证书更新的标准过程。
- **Linux VM**：按照 Linux 分销商提供的指导，获取最新的受信任的根证书和证书吊销列表 (CRL)。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

在任何区域中创建恢复服务保管库，但要从中复制 VM 的源区域除外。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在搜索框中，键入“recovery”。 在“服务”下，选择“恢复服务保管库” 。

    ![搜索恢复服务保管库](./media/azure-to-azure-tutorial-enable-replication/search.png)

3. 在“恢复服务保管库”中，选择“添加” 。
4. 在“创建恢复服务保管库” > “基础”中，选择要在其中创建保管库的订阅 。
5. 在“资源组”中，为保管库选择现有的资源组，或创建新的资源组。
6. 在“保管库名称”中，指定一个易记名称以标识该保管库。
7. 在“区域”中，选择要在其中放置保管库的 Azure 区域。 [检查支持的区域](https://azure.microsoft.com/pricing/details/site-recovery/)。
8. 选择“查看 + 创建”。

   ![页面上用于创建新保管库的保管库设置](./media/azure-to-azure-tutorial-enable-replication/vault-basics.png)

9. 在“查看 + 创建”中，选择“创建” 。

10. 开始部署保管库。 在通知中跟踪进度。
11. 部署保管库后，选择“固定到仪表板”以保存该保管库，以供快速参考。 选择“转到资源”，打开新的保管库。

    ![用于在部署后打开保管库并将其固定到仪表板的按钮](./media/azure-to-azure-tutorial-enable-replication/vault-deploy.png)

### <a name="enable-site-recovery"></a>启用 Site Recovery

在保管库设置中，选择“启用 Site Recovery”。

![选择在保管库中启用 Site Recovery](./media/azure-to-azure-tutorial-enable-replication/enable-site-recovery.png)

## <a name="enable-replication"></a>启用复制

选择源设置，然后启用 VM 复制。

### <a name="select-source-settings"></a>选择源设置

1. 在保管库 >“Site Recovery”页面的“Azure 虚拟机”下，选择“启用复制”  。

    ![用于为 Azure VM 启用复制的选项](./media/azure-to-azure-tutorial-enable-replication/enable-replication.png)

2. 在“源”> “源位置”中，选择当前正在运行 VM 的源 Azure 区域 。
3. 在“Azure 虚拟机部署模型”中，保留默认的“资源管理器”设置 。
4. 在“源订阅”中，选择 VM 正在运行的订阅。 可以选择与保管库位于同一 Azure Active Directory (AD) 租户中的任何订阅。
5. 在“源资源组”中，选择包含 VM 的资源组。
6. 在“可用性区域之间的灾难恢复”中，保留默认的“否”设置 。

     ![设置源](./media/azure-to-azure-tutorial-enable-replication/source.png)

7. 选择“下一步”。

### <a name="select-the-vms"></a>选择 VM

Site Recovery 检索与所选订阅/资源组关联的 VM。

1. 在“虚拟机”中，选择要为灾难恢复启用的 VM。

     ![用于选择 VM 进行复制的页面](./media/azure-to-azure-tutorial-enable-replication/select-vm.png)

2. 选择“下一步”。

### <a name="review-replication-settings"></a>查看复制设置

1. 在“复制设置”中，查看设置。 Site Recovery 会为目标区域创建默认设置/策略。 出于本教程的目的，我们使用默认设置。
2. 选择“启用复制”  。

    ![用于自定义设置和启用复制的页面](./media/azure-to-azure-tutorial-enable-replication/enable-vm-replication.png)   

3. 在通知中跟踪复制进度。

     ![在通知中跟踪进度](./media/azure-to-azure-tutorial-enable-replication/notification.png) ![跟踪成功的复制通知](./media/azure-to-azure-tutorial-enable-replication/notification-success.png)

4. 启用的 VM 将显示在“保管库”>“复制的项”页面上。

    ![“复制的项”页面上的 VM](./media/azure-to-azure-tutorial-enable-replication/replicated-items.png)


## <a name="next-steps"></a>后续步骤

在本教程中，为 Azure VM 启用了灾难恢复。 现在，运行演练，检查故障转移是否按预期方式工作。

> [!div class="nextstepaction"]
> [运行灾难恢复演练](azure-to-azure-tutorial-dr-drill.md)
