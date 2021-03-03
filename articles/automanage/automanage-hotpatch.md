---
title: 'Hotpatch for Windows Server Azure Edition (预览版) '
description: 了解 Windows Server Azure 版本 Hotpatch 的工作原理以及如何启用它
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: bdd5a379afb9603c8966320d85c778632948cfd0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661819"
---
# <a name="hotpatch-for-windows-server-azure-edition-preview"></a>Hotpatch for Windows Server Azure Edition (预览版) 

热修补是在新的 Windows Server Azure Edition 虚拟机上安装更新的一种新方法， (Vm) 不需要在安装后重新启动。 本文介绍有关适用于 Windows Server Azure Edition Vm 的 Hotpatch 的信息，它们具有以下优势：
* 更低的重启降低工作负荷的影响
* 更快地部署更新，因为包更小，安装速度更快，使用 Azure 更新管理器可以更轻松地进行修补业务流程
* 更好的保护，因为 Hotpatch 更新包的作用域为 Windows 安全更新，这些更新在无需重新启动的情况下安装

## <a name="how-hotpatch-works"></a>Hotpatch 的工作原理

Hotpatch 的工作原理是：首先使用 Windows 更新最新的累积更新来建立基线。 Hotpatches 定期发布 (例如，在每月的第二个星期二) 该基线上生成的。 Hotpatches 将包含不需要重新启动的更新。 每三个月定期 (启动) ，将使用新的最新累积更新刷新基线。

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Hotpatch Sample Schedule.":::

有两种类型的基线： **计划的基线** 和 **计划外基线**。
*  **计划的基线** 会定期发布，其中 Hotpatch 的版本介于之间。  计划的基线在该月的 _最新累积更新_ 中包含所有更新，并且需要重新启动。
    * 上面的示例计划显示了一个日历年中的四个计划的基线发布， (关系图中的五个总计) ，以及8个 hotpatch 版本。
* 当重要更新 (（例如零天修复) ），并且无法将特定更新作为 Hotpatch 发布时，将释放未 **计划的基线**。  当发布未计划的基线时，hotpatch 版本将替换为该月中的计划外基线。  计划外基线还会在该月的 _最新累积更新_ 中包含所有更新，并且还需要重新启动。
    * 上面的示例计划显示了两个计划外的基准，它们将替换这些月的 hotpatch 版本， (一年中不事先) 计划外基线的实际数量。

## <a name="regional-availability"></a>区域可用性
预览中的所有全球 Azure 区域均提供 Hotpatch。 预览版不支持 Azure 政府区域。

## <a name="how-to-get-started"></a>如何开始使用

> [!NOTE]
> 在预览阶段，只能在 Azure 门户中使用 [此链接](https://aka.ms/AzureAutomanageHotPatch)开始。

若要开始在新 VM 上使用 Hotpatch，请执行以下步骤：
1.  启用预览访问
    * 每个订阅都需要一次性预览访问启用。
    * 可以通过 API、PowerShell 或 CLI 启用预览访问权限，如下一节所述。
1.  从 Azure 门户创建 VM
    * 预览期间，你将需要开始使用 [此链接](https://aka.ms/AzureAutomanageHotPatch)。
1.  提供 VM 详细信息
    * 确保在 "映像" 下拉列表中选择了 " _Windows Server 2019 Datacenter： Azure 版本_ ") 
    * 在 "管理" 选项卡步骤中，向下滚动到 "来宾操作系统更新" 部分。 你将看到 "热修补设置为" 和 "修补安装默认为 Azure 协调的修补"。
    * 默认情况下，将启用 Automanage VM 最佳做法
1. 创建新 VM

## <a name="enabling-preview-access"></a>启用预览版访问

### <a name="rest-api"></a>REST API

下面的示例说明如何为订阅启用预览：

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/ InGuestHotPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

功能注册可能最多需要15分钟。 若要检查注册状态，请使用以下命令：

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestHotPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```

为订阅注册此功能后，通过将更改传播到计算资源提供程序来完成选择加入过程。

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell

使用 ```Register-AzProviderFeature``` cmdlet 为你的订阅启用预览。

``` PowerShell
Register-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

功能注册可能最多需要15分钟。 若要检查注册状态，请使用以下命令：

``` PowerShell
Get-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

为订阅注册此功能后，通过将更改传播到计算资源提供程序来完成选择加入过程。

``` PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI

使用 ```az feature register``` 启用订阅的预览。

```
az feature register --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

功能注册可能最多需要15分钟。 若要检查注册状态，请使用以下命令：
```
az feature show --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

为订阅注册此功能后，通过将更改传播到计算资源提供程序来完成选择加入过程。

```
az provider register --namespace Microsoft.Compute
```

## <a name="patch-installation"></a>修补程序安装

在预览期间，会自动为通过 _Windows Server 2019 Datacenter： Azure 版本_ 创建的所有 Vm 启用 [自动 VM 来宾修补](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching)。 启用自动 VM 来宾修补后：
* 分类为 "关键" 或 "安全" 的修补程序会自动下载并应用于 VM。
* 修补程序在 VM 时区的非高峰时段内应用。
* 修补业务流程由 Azure 管理，修补程序应用于 [可用性优先原则](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching#availability-first-patching)。
* 虚拟机运行状况由平台运行状况信号确定，用于检测修补故障。

### <a name="how-does-automatic-vm-guest-patching-work"></a>自动 VM 来宾修补如何工作？

当在 VM 上启用 [自动 VM 来宾修补](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching) 时，将自动下载并应用可用的关键修补程序和安全修补程序。 此过程将在每个月发布新的修补程序时自动启动。 修补程序评估和安装是自动的，该过程包括根据需要重新启动 VM。

在 _Windows Server 2019 Datacenter_ 上启用了 Hotpatch： Azure Edition vm，大多数每月的安全更新都作为 hotpatches 提供，不需要重新启动。 在计划的或计划外的基准月发送的最新累积更新需要重新启动 VM。 还可能会定期提供额外的关键或安全修补程序，这可能需要重新启动 VM。

VM 每隔几天自动评估一次，并在30天内进行多次评估，以确定适用于该 VM 的修补程序。 这种自动评估可确保以尽可能早的机会发现任何缺失的修补程序。

修补程序在每月修补程序版本的30天内安装，遵循 [可用性优先原则](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching#availability-first-patching)。 仅在非高峰时间为 VM 安装修补程序，具体取决于 VM 的时区。 VM 必须在非高峰时段运行，才能自动安装修补程序。 如果 VM 在定期评估期间关闭，则将评估 VM，并在下一次定期评估期间自动安装适用的修补程序。 下一次定期评估通常会在几天内发生。

不会通过自动 VM 来宾修补来安装未分类为关键或安全的定义更新和其他修补程序。

## <a name="understanding-the-patch-status-for-your-vm"></a>了解 VM 的修补程序状态

若要查看 VM 的修补状态，请导航到 Azure 门户中 VM 的 " **来宾 + 主机更新** " 部分。 在 " **来宾 OS 更新** " 部分中，单击 "中转到 Hotpatch (Preview) " 以查看 VM 的最新修补程序状态。

在此屏幕上，可看到 VM 的 Hotpatch 状态。 你还可以查看是否存在尚未安装的 VM 的可用修补程序。 如以上 "修补程序安装" 一节中所述，所有安全更新和关键更新都将使用 [自动 VM 来宾修补](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching) 自动安装到 vm 上，无需执行其他操作。 具有其他更新分类的修补程序不会自动安装。 相反，它们可在 "更新符合性" 选项卡下的可用修补程序列表中查看。还可以通过 "更新历史记录" 查看 VM 上的更新部署历史记录。 显示过去30天的更新历史记录，以及修补程序安装详细信息。


    :::image type="content" source="media\automanage-hotpatch\hotpatch-management-ui.png" alt-text="Hotpatch Management.":::

通过自动 VM 来宾修补，你的 VM 会定期自动评估可用的更新。 这些定期评估确保检测到可用的修补程序。 你可以在上述更新屏幕上查看评估结果，包括上次评估的时间。 你还可以选择使用 "立即评估" 选项随时触发 VM 的按需修补程序评估并在评估完成后查看结果。

与按需评估类似，你还可以使用 "立即安装更新" 选项为 VM 按需安装修补程序。 在此处，你可以选择在 "特定修补程序分类" 下安装所有更新。 还可以通过提供单个知识库文章的列表来指定要包含或排除的更新。 按需安装的修补程序不使用可用性优先的原则进行安装，并且可能需要更多的重新启动和 VM 停机时间才能安装更新。

## <a name="supported-updates"></a>支持的更新

Hotpatch 包含 Windows 安全更新，并与在常规 (非 Hotpatch) Windows 更新通道中颁发的安全更新的内容保持奇偶校验。

在启用了 Hotpatch 的情况运行 Windows Server Azure edition VM 时，有一些重要的注意事项。 安装不包含在 Hotpatch 程序中的更新时，仍然需要重新启动。 安装新的基线后，还会定期需要重新启动。 这些重启会使 VM 与最新的累积更新中包含的非安全修补程序保持同步。
* 当前未包含在 Hotpatch 程序中的修补程序包括为 Windows 发布的非安全更新和非 Windows 更新 (如 .NET 修补程序) 。  此类修补程序需要在基准月内安装，并且需要重新启动。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="what-is-hotpatching"></a>什么是热修补？

* 热修补是在 Windows Server 2019 Datacenter 上安装更新的一种新方法： Azure 中的 Azure Edition VM，安装后不需要重新启动。 它的工作方式是在不需要重新启动进程的情况下，修补正在运行的进程的内存中代码。

### <a name="how-does-hotpatching-work"></a>热修补如何工作？

* 通过使用最新的累积更新建立 Windows 更新基线来实现热修补工作，然后在该基线上构建不需要重新启动即可生效的更新。  使用新的累积更新定期更新基线。 累积更新包括所有安全更新和质量更新，并且需要重新启动。

### <a name="why-should-i-use-hotpatch"></a>为什么要使用 Hotpatch？

* 当你在 Windows Server 2019 Datacenter： Azure 版本中使用 Hotpatch 时，你的 VM 将具有更高的可用性 (更少的重启) 和更快的更新 (更快安装的较小包，而无需重新启动进程) 。 此过程会导致 VM 始终处于最新和最新状态。

### <a name="what-types-of-updates-are-covered-by-hotpatch"></a>Hotpatch 涵盖了哪些类型的更新？

* Hotpatch 当前包含 Windows 安全更新。

### <a name="when-will-i-receive-the-first-hotpatch-update"></a>何时会收到第一次 Hotpatch 更新？

* Hotpatch 更新通常在每月的第二个星期二发布。 有关详细信息，请参阅以下内容。

### <a name="what-will-the-hotpatch-schedule-look-like"></a>Hotpatch 计划如下所示？

* 热修补的工作原理是通过使用 Windows 更新最新的累积更新来构建基线，然后使用每月发布的 Hotpatch 更新基于该基线进行构建。  在预览期间，将每隔三个月发布一次基线。 请参阅下面的图像，获取每年三个月的计划 (，其中包括) 零天修复的示例计划外基线。

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Hotpatch 示例计划。":::

### <a name="are-reboots-still-needed-for-a-vm-enrolled-in-hotpatch"></a>对于在 Hotpatch 中注册的 VM，是否仍需要重新启动？

* 安装不包含在 Hotpatch 程序中的更新时，仍然需要重新启动，并在基线 (Windows 更新已安装最新累积更新) 后定期需要重新启动。 此重新启动将使 VM 与累积更新中包含的所有修补程序同步。 需要重新启动) 的基线 (将在三个月的时间段开始，并随着时间的推移增加6到6个月。

### <a name="are-my-applications-affected-when-a-hotpatch-update-is-installed"></a>安装 Hotpatch 更新时应用程序是否受到影响？

* 由于 Hotpatch 会修补正在运行的进程的内存中代码，而无需重新启动此过程，因此，应用程序不会受到修补过程的影响。 请注意，这不同于修补程序本身的任何潜在性能和功能问题。

### <a name="can-i-turn-off-hotpatch-on-my-vm"></a>是否可以在 VM 上关闭 Hotpatch？

* 可以通过 Azure 门户关闭 VM 上的 Hotpatch。  关闭 Hotpatch 将从 Hotpatch 取消注册 VM，这会将 VM 恢复为 Windows Server 的典型更新行为。  从 VM 上的 Hotpatch 取消注册后，可以在发布下一个 Hotpatch 基线后重新注册该 VM。

### <a name="can-i-upgrade-from-my-existing-windows-server-os"></a>是否可以从现有的 Windows Server 操作系统升级？

* 当前不支持从现有版本的 Windows Server (即 Windows Server 2016 或2019非 Azure 版本) 。 将支持升级到未来版本的 Windows Server Azure 版本。

### <a name="can-i-use-hotpatch-for-production-workloads-during-the-preview"></a>在预览期间，是否可以将 Hotpatch 用于生产工作负荷？

* 预览版仅用于测试目的，不能在生产环境中使用。

### <a name="will-i-be-charged-during-the-preview"></a>预览期间是否会向我收费？

* 在预览版期间，Windows Server Azure 版本的许可证是免费的。 但是，为 VM 设置的任何底层基础结构的成本 (存储、计算、网络等 ) 仍将向你的订阅收取费用。

### <a name="how-can-i-get-troubleshooting-support-for-hotpatching"></a>如何获取热修补的故障排除支持？

* 您可以使用 [技术支持案例](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。 对于 "服务" 选项，请在 "计算" 下搜索和选择 **运行 Windows 的虚拟机** 。 为问题类型选择 **Azure 功能** ，并为问题子类型选择 **自动 VM 来宾修补** 。

## <a name="next-steps"></a>后续步骤

* [在此处](https://docs.microsoft.com/azure/automation/update-management/overview)了解 Azure 更新管理。
* [在此处](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching)了解有关自动 VM 来宾修补的详细信息