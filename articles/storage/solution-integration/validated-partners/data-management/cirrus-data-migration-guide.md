---
title: 使用 Cirrus Migrate Cloud 将块数据迁移到 Azure
titleSuffix: Azure Storage
description: 提供有关实施 Cirrus Migrate Cloud 并将数据迁移到 Azure 的快速入门指南
author: dukicn
ms.author: nikoduki
ms.date: 09/06/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 69ee8dd0f7acf0959ccbfb47ebde01e6d17cfe21
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128698927"
---
# <a name="migrate-your-block-data-to-azure-with-cirrus-migrate-cloud"></a>使用 Cirrus Migrate Cloud 将块数据迁移到 Azure

使用 Cirrus Migrate Cloud (CMC) 可将磁盘从现有存储系统或云迁移到 Azure。 迁移是在原始系统仍在运行时执行的。 本文档将提供成功配置和执行迁移的方法。

## <a name="overview"></a>概述

该解决方案使用在每台允许主机到主机直接连接的主机上运行的分布式迁移代理。 主机到主机的每次迁移都是独立进行的，使解决方案无限可缩放，且不会给数据流造成中间瓶颈。 迁移使用 cMotion™ 技术，确保不会影响生产。 

## <a name="use-cases"></a>用例

本文档介绍一种常规迁移案例，它将应用程序从一个虚拟机（在本地或另一个云平台中运行）移到 Azure 中的虚拟机。 有关各种用例的更深入分步指南，请参阅以下链接：

- [使用 cMotion 将工作负载移到 Azure](https://support.cirrusdata.cloud/en/article/howto-cirrus-migrate-cloud-on-premises-to-azure-1xo3nuf/)
- [从高级磁盘移到超级磁盘](https://support.cirrusdata.cloud/en/article/howto-cirrus-migrate-cloud-migration-between-azure-tiers-sxhppt/)
- [从 AWS 移到 Azure](https://support.cirrusdata.cloud/en/article/howto-cirrus-migrate-cloud-migration-from-aws-to-azure-weegd9/.)

## <a name="components"></a>组件

Cirrus Migrate Cloud 由多个组件构成：

- CMC 的 cMotion™ 功能执行从源到目标云的存储级切换，且不会造成源主机停机。 cMotion™ 用于将工作负载从原始 FC 或 iSCSI 源磁盘转移到新的目标 Azure 托管磁盘。
- 基于 Web 的管理门户是基于 Web 的管理即服务。 用户可以使用该门户来管理迁移和保护任何块存储。 基于 Web 的管理门户为所有 CMC 应用程序配置、管理和管理任务提供界面。

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-web-portal.jpg" alt-text="CMC 门户的屏幕截图":::

## <a name="implementation-guide"></a>实施指南

用户应遵循 Azure 最佳做法来实施新虚拟机。 如果你不熟悉该过程，请阅读[快速入门指南](/azure/virtual-machines/windows/quick-create-portal)了解详细信息。

在开始迁移之前，请确保满足以下先决条件：

- 验证 Azure 中的 OS 是否已获得适当的许可
- 验证对 Azure 虚拟机的访问权限
- 检查应用程序/数据库许可证是否可在 Azure 中运行
- 检查用于自动分配目标磁盘大小的权限
- 确保托管磁盘的大小与源磁盘相同或更大 
- 确保源或目标虚拟机上已打开了端口，以允许建立 H2H 连接。

1. 准备 Azure 虚拟机。 本文档假设已完全实施虚拟机。 这样，一旦迁移了数据磁盘，目标主机就可以立即启动应用程序并将其联机。 数据状态与源在几秒钟前关闭时的状态相同。 CMC 不会将 OS 磁盘从源迁移到目标。

1. 在 Azure 虚拟机中准备应用程序。 在此示例中，源是 Linux 主机。 它可以运行任何访问相应 BSD 存储的用户应用程序。 我们将使用源上运行的某个数据库应用程序，该应用程序使用 1 GiB 磁盘作为源存储设备。 不过，可以改用任何应用程序。 在 Azure 中设置一个随时可用作目标虚拟机的虚拟机。 确保资源配置和操作系统与应用程序兼容，并已准备好通过 CMC 门户从源迁移。 在迁移过程中，将自动分配和创建目标块存储设备。

1. 注册 CMC 帐户。 若要获取 CMC 帐户，请按照支持页面中有关如何获取帐户的具体说明进行操作。 可在[此处](https://support.cirrusdata.cloud/en/article/licensing-m4lhll/)了解更多详细信息。

1. 创建一个迁移项目，该项目反映具体的迁移特征、类型、迁移所有者，以及定义操作所需的任何详细信息。 

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-create-project.jpg" alt-text="创建新项目的屏幕截图":::

1. 定义迁移项目参数。 使用 CMC 基于 Web 的门户，通过定义源、目标参数和其他参数来配置迁移。

1. 在源和目标主机上安装迁移 CMC 代理。 使用 CMC 基于 Web 的管理门户，选择“部署 Cirrus Migrate Cloud”以获取用于“全新安装”的 curl 命令 。 在源和目标命令行接口上运行该命令。

1. 在源和目标主机之间创建双向连接。 使用 CMC 基于 Web 的管理门户中的“H2H”选项卡，选择“创建新连接”按钮 。 选择应用程序使用的设备，而不要选择 Linux 操作系统使用的设备。

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-1.jpg" alt-text="显示已部署的主机列表的屏幕截图":::

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-2.jpg" alt-text="显示主机到主机连接列表的屏幕截图":::

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-3.jpg" alt-text="显示已迁移的设备列表的屏幕截图":::

1. 使用 CMC 基于 Web 的管理门户中的“迁移主机卷”开始迁移到目标虚拟机 。 按照有关远程位置的说明进行操作。 在屏幕右侧使用 CMC 门户自动分配目标卷。 
 
1. 接下来，需要使用 CMC 门户上的“集成”选项卡添加 Azure 凭据，以允许进行连接和磁盘预配。 使用你所在公司专用于 Azure 的值填写必填字段：“集成名称”、“租户 ID”、“客户端/应用程序 ID”和“机密”   。 按“保存”  。 

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-4.jpg" alt-text="显示输入 Azure 凭据的屏幕截图":::

    有关创建 Azure AD 应用程序的详细信息，请查看我们的[分步说明](https://support.cirrusdata.cloud/en/article/creating-an-azure-service-account-for-cirrus-data-cloud-tw2c9n/)。 通过为 CMC 创建并注册 Azure AD 应用程序，可以在目标虚拟机上自动创建 Azure 托管磁盘。

    >[!NOTE]
    >由于你在上一步骤中选择了“自动分配目标卷”，因此请不要再次按下该按钮来进行新的分配。 否则会输出错误。 请改为按下“继续”。

## <a name="migration-guide"></a>迁移指南

在上一步骤中按下“保存”后，将出现“新建迁移会话”窗口 。 填写字段：
   - 会话说明：提供有意义的说明
   - 自动重新同步间隔：启用迁移计划 
   - 使用 iQoS 来选择迁移对生产造成的影响：
     - “最小”会将迁移速率限制为可用带宽的 25%
     - “适中”会将迁移速率限制为可用带宽的 50%
     - “激进”会将迁移速率限制为可用带宽的 75%
     - “无限制”不会限制迁移。

       :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-iqos.jpg" alt-text="显示 iQoS 设置选项的屏幕截图":::

按下“创建会话”开始迁移。

从迁移初始同步开始到 cMotion 启动为止，用户都不需要与 CMC 交互。  唯一的例外是在监视进度时。 可以使用仪表板监视当前状态、会话量并跟踪更改。 

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-1.jpg" alt-text="显示监视进度的屏幕截图":::

在迁移过程中，可以通过按下“已更改数据映射”来观察源设备上已更改的块。  

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-2.jpg" alt-text="显示“已更改数据映射”的屏幕截图":::

有关 iQoS 的详细信息将显示已同步的块和迁移状态。 其中还会表明对生产 IO 没有造成影响。

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-3.jpg" alt-text="显示 iQoS 详细信息的屏幕截图":::

## <a name="moving-the-workload-to-azure-with-cmotion"></a>使用 cMotion 将工作负载移到 Azure

初始同步完成后，我们准备使用 cMotion™ 将工作负载从源磁盘移到目标 Azure 托管磁盘。

### <a name="start-cmotion"></a>启动 cMotion™

此时，系统已准备好进行 cMotion™ 迁移切换。 

1. 在 CMS 门户中，使用“会话”选择“触发 cMotion™”，以将工作负载从源磁盘切换到目标磁盘。 若要检查是否已完成该过程，可以使用 iostat 或等效的命令。 转到 Azure 虚拟机中的终端，运行 iostat /dev/<device_name>（例如 /dev/sdc），然后观察应用程序在 Azure 云中的目标磁盘上写入的 IO。

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-4.jpg" alt-text="显示当前监视状态的屏幕截图":::

在此状态下，工作负载随时可以在源磁盘和目标磁盘之间来回切换。 若要还原生产虚拟机，请使用“会话操作”按钮，然后选择“还原 cMotion™”选项 。 当应用程序在源主机/VM 上运行时，我们可以来回切换任意次数。

如果需要最终切换到目标虚拟机，请执行以下步骤：
1. 选择“会话操作”
2. 单击“完成切换”选项以“锁定”为切换到新 Azure 虚拟机，并禁用用于删除源磁盘的选项。 停止源主机中运行的任何其他应用程序，以完成最终的主机切换。 

### <a name="move-the-application-to-the-destination-virtual-machine"></a>将应用程序移到目标虚拟机

切换完成后，需要将应用程序切换到新虚拟机。 为此，请执行以下步骤：

1. 停止应用程序
2. 卸载已迁移的设备
3. 在 Azure 虚拟机中装载新的已迁移设备。 
4. 在新的已迁移磁盘上的 Azure 虚拟机中启动同一应用程序。 
 
通过在源主机中运行 iostat 命令，观察是否没有任何 IO 进入源主机设备。 在 Azure 虚拟机中运行 iostat 会显示 IO 正在 Azure 虚拟机终端上执行。

### <a name="complete-the-migration-session-in-cmc-gui"></a>在 CMC GUI 中完成迁移会话 

在触发 cMotion™ 后，如果所有 IO 已重定向到目标设备，则意味着迁移步骤已完成。 现在可以使用“会话操作”关闭会话。 单击“删除会话”即可关闭迁移会话。 最后一步是，从源主机和 Azure 虚拟机中删除 Cirrus Migrate Cloud 代理。 若要执行卸载，请通过“部署 Cirrus Migrate Cloud”按钮获取“卸载 curl 命令” 。 门户的“主机”部分提供了选项。 

删除代理后，迁移即告彻底完成。 现在，源应用程序正在生产环境中具有本地装载磁盘的目标 Azure 虚拟机上运行。    

## <a name="support"></a>支持

### <a name="how-to-open-a-case-with-azure"></a>如何向 Azure 提出案例

在 [Azure 门户](https://portal.azure.com)的顶部搜索栏中搜索“支持”。 选择“帮助 + 支持” -> “新建支持请求” 。

### <a name="engaging-cirrus-support"></a>联系 Cirrus 支持部门

在 CMC 门户中选择“帮助中心”选项卡以联系 Cirrus Data Solutions 支持部门，或者访问 [CDSI 网站](https://support.cirrusdata.cloud/en/)并提交支持请求。

## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure 虚拟机](/azure/virtual-machines/windows/overview)
- 详细了解 [Azure 托管磁盘](/azure/virtual-machines/managed-disks-overview)
- 详细了解[存储迁移](/azure/storage/common/storage-migration-overview)
- [Cirrus Data 网站](https://www.cirrusdata.com/)
- [cMotion](https://support.cirrusdata.cloud/en/category/howtos-1un623w/) 分步指南
