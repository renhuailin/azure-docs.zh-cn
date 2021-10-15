---
title: Azure 虚拟桌面灾难恢复计划
description: 为 Azure 虚拟桌面部署制定灾难恢复计划以保护数据。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 54202b49e2881d1d128136b876cbf88c2bc87400
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359762"
---
# <a name="azure-virtual-desktop-disaster-recovery"></a>Azure 虚拟桌面灾难恢复

为确保组织数据的安全，可能需要采用业务连续性和灾难恢复 (BCDR) 策略。 合理的 BCDR 策略可以让应用和工作负载在执行计划内和计划外维护或 Azure 中断期间仍能保持正常运行。

Azure 虚拟桌面为 Azure 虚拟桌面服务提供 BCDR，以便在中断期间保留客户元数据。 当某个区域发生服务中断时，服务基础结构组件会故障转移到次要位置，并继续像正常一样运行。 你仍可以访问与服务相关的元数据，并且用户仍可以连接到可用的主机。 只要租户环境或主机保持可访问状态，最终用户连接就将保持联机状态。

为确保用户在区域服务中断期间仍可以连接，需要在某个不同的位置复制他们的虚拟机 (VM)。 在中断期间，主站点将故障转移到次要位置中复制的 VM。 用户则可以继续从次要位置访问应用，而不会出现中断。 在 VM 复制的基础上，需要保持用户标识在次要位置处于可访问状态。 因此，如果要使用配置文件容器，则还需要复制它们。 最后，请确保依赖于主要位置数据的业务应用可与其余数据一起进行故障转移。

总而言之，要使用户在中断期间仍保持连接状态，需要按以下顺序执行下列操作：

- 将 VM 复制到某个次要位置。
- 如果使用的是配置文件容器，请在次要位置设置数据复制。
- 确保在主要位置设置的用户标识在次要位置中可用。
- 确保依赖于主要位置中数据的任何业务线应用程序会故障转移到次要位置。

## <a name="vm-replication"></a>VM 复制

首先，需要将 VM 复制到次要位置。 执行此操作的选项取决于 VM 的配置方式：

- 可以通过 Azure Site Recovery 为共用和个人主机池配置所有 VM。 利用此方法，只需设置一个主机池及其相关的应用组和工作区。
- 可以在故障转移区域创建新的主机池，同时使故障转移位置的所有资源保持关闭状态。 对于此方法，需要在故障转移区域设置新的应用组和工作区。 然后，可以使用 Azure Site Recovery 计划打开主机池。
- 可以创建一个填充主要区域和故障转移区域内置 VM 的主机池，同时保持故障转移区域中的 VM 处于关闭状态。 这种情况下，只需设置一个主机池及其相关的应用组和工作区。 对于此方法，可以使用 Azure Site Recovery 计划开启主机池。

建议使用 [Azure Site Recovery](../site-recovery/site-recovery-overview.md) 来管理其他 Azure 位置中的复制 VM，如 [Azure 到 Azure 灾难恢复体系结构](../site-recovery/azure-to-azure-architecture.md)中所述。 对于个人主机池，特别推荐使用 Azure Site Recovery，因为 Azure Site Rhuecovery 支持[基于服务器和基于客户端的 SKU](../site-recovery/azure-to-azure-support-matrix.md#replicated-machine-operating-systems)。

如果使用 Azure Site Recovery，则无需再手动注册这些 VM。 次要 VM 中的 Azure 虚拟桌面代理将自动使用最新的安全令牌连接到最靠近它的服务实例。 次要位置的 VM（会话主机）将自动成为主机池的一部分。 在此过程中，最终用户必须重新连接，但除此之外，再无其他手动操作。

如果在中断期间已存在用户连接，则在管理员开始故障转移到次要区域之前，需要先结束当前区域的用户连接。

要在 Azure 虚拟桌面（经典版）中断开用户连接，请运行以下 cmdlet：

```powershell
Invoke-RdsUserSessionLogoff
```

要在 Azure 集成版本的 Azure 虚拟桌面中断开用户连接，请运行以下 cmdlet：

```powershell
Remove-AzWvdUserSession
```

在主要区域中注销所有用户后，则可以对主要区域的 VM 进行故障转移，让用户连接到次要区域中的 VM。 有关此过程工作原理的详细信息，请参阅[将 Azure VM 复制到其他 Azure 区域](../site-recovery/azure-to-azure-how-to-enable-replication.md)。

## <a name="virtual-network"></a>虚拟网络

接下来，考虑中断期间的网络连接。 需要确保已在次要区域设置虚拟网络 (VNET)。 如果用户需要访问本地资源，则需要配置此 VNET 来访问它们。 可以使用 VPN、ExpressRoute 或虚拟 WAN 建立本地连接。

建议使用 Azure Site Recovery 在故障转移区域设置 VNET，因为它会保留主网络的设置，并且不需要对等互连。

## <a name="user-identities"></a>用户标识

接下来，确保域控制器在次要位置可用。 

可以通过三种方式来使域控制器保持可用：

   - 在次要位置设置 Active Directory 域控制器
   - 使用本地 Active Directory 域控制器
   - 使用 [Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) 复制 Active Directory 域控制器

## <a name="user-and-app-data"></a>用户和应用数据

如果使用的是配置文件容器，接下来则要在次要位置设置数据复制。 存储 FSLogix 配置文件的五种选项如下：

   - 存储空间直通 (S2D)
   - 网络驱动器（具备额外硬盘的 VM）
   - Azure 文件
   - Azure NetApp 文件
   - 通过云缓存进行复制

有关详细信息，请查阅 [Azure 虚拟桌面中 FSLogix 配置文件容器的存储选项](store-fslogix-profile.md)。

如果要为配置文件设置灾难恢复，可选择以下选项：

   - 设置本机 Azure 复制（例如，Azure 文件标准存储帐户复制、Azure NetApp 文件复制或适用于文件服务器的 Azure 文件同步）。
    
     >[!NOTE]
     >首次设置 NetApp 后，NetApp 复制会自动进行。 利用 Azure Site Recovery 计划，可以添加预安装脚本和补充脚本来故障转移非 VM 资源以复制 Azure 存储资源。

   - 对应用和用户数据设置 FSLogix 云缓存。
   - 仅对应用数据设置灾难恢复，以确保始终能够访问业务关键数据。 利用此方法，在服务中断后仍可检索用户数据。

让我们看一看如何配置 FSLogix 来为每个选项设置灾难恢复。

### <a name="fslogix-configuration"></a>FSLogix 配置

如果为 FSLogix 配置了注册表项，则 FSLogix 代理可以支持多个配置文件位置。

配置注册表项：

1. 打开 **注册表编辑器**。
2. 导航到“计算机” > “HKEY_LOCAL_MACHINE” > “SOFTWARE” > “FSLogix” > “配置文件”。
   
     > [!div class="mx-imgBorder"]
     > ![注册表编辑器中“配置文件”窗口的屏幕截图。 已选择 VHDLocation。](media/regedit-profiles.png)

3. 右键单击“VHDLocations”，然后选择“编辑多字符串”。

     > [!div class="mx-imgBorder"]
     > ![“编辑多字符串”窗口的屏幕截图。 值数据列出了“美国中部”和“美国东部”位置。](media/multi-string-edit.png)

4. 在“值数据”字段中，输入要使用的位置。
5. 完成后，请选择“确定”。

如果第一个位置不可用，则 FSLogix 代理将自动故障转移到第二个位置，依此类推。

建议在主要区域为 FSLogix 代理配置一个连接到次要位置的路径。 一旦主要位置关闭，FLogix 代理将作为 VM Azure Site Recovery 复制的一部分进行复制。 在复制的 VM 准备就绪后，该代理将自动尝试通过路径连接到次要区域。

例如，假设主要会话主机 VM 位于美国中部区域，但出于性能考虑，配置文件容器设在美国中部区域。

在这种情况下，可以为 FSLogix 代理配置一个在美国中部进行存储的路径。 需要将会话主机 VM 配置为在美国西部进行复制。 一旦连接美国中部的路径失败，代理将尝试创建一个新路径，改为在美国西部进行存储。

### <a name="s2d"></a>S2D

由于 S2D 可在内部跨区域处理复制，因此无需手动设置次要路径。

### <a name="network-drives-vm-with-extra-drives"></a>网络驱动器（具备额外硬盘的 VM）

如果使用 Azure Site Recovery（例如，会话主机 VM）来复制网络存储 VM，则恢复将保留相同的路径，这意味着无需重新配置 FSlogix。

### <a name="azure-files"></a>Azure 文件

Azure 文件支持跨区域异步复制，在创建存储帐户时可指定此选项。 如果 Azure 文件的异步特性已经涵盖灾难恢复目标，则无需再进行其他配置。

如果需要同步复制以最大程度地减少数据丢失，则建议改为使用 FSLogix 云缓存。

>[!NOTE]
>本部分不介绍 Azure 文件的故障转移身份验证机制。

### <a name="azure-netapp-files"></a>Azure NetApp 文件

有关 Azure NetApp 文件的详细信息，请参阅“[为 Azure Netapp 文件创建复制对等互连](../azure-netapp-files/cross-region-replication-create-peering.md)”。

## <a name="app-dependencies"></a>应用依赖项

最后，请确保依赖于主要区域中数据的任何业务应用均可故障转移到次要位置。 此外，确保配置好应用在新位置需要使用的设置。 例如，如果其中一款应用依赖于 SQL 后端，则务必确保在次要位置复制 SQL。 应将应用使用次要位置配置为故障转移进程的一部分或默认配置。 可以模仿 Azure Site Recovery 计划中的应用依赖项。 有关详细信息，请参阅[恢复计划](../site-recovery/recovery-plan-overview.md)。

## <a name="disaster-recovery-testing"></a>灾难恢复测试

设置完灾难恢复后，需要测试计划以确保其正常运行。

下面是有关如何测试计划的一些建议：

- 如果测试 VM 具有联网权限，它们将接管现有的任何会话主机进行新的连接，但与原始会话主机的所有现有连接仍将保持活动状态。 确保运行测试的管理员在测试计划前先注销所有活动用户。 
- 只应在维护时段执行完全的灾难恢复测试，以免干扰用户。 还可以在验证环境下使用主机池进行测试。 
- 请确保测试涵盖了所有业务关键应用。
- 建议一次最多对 100 个 VM 进行故障转移。 如果 VM 数量超过 100，建议分批对它们进行故障转移，每批间隔 10 分钟。

## <a name="next-steps"></a>后续步骤

如果对如何确保数据安全以及服务中断计划存有疑问，请参阅我们的[安全指南](security-guide.md)。