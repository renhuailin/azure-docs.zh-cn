---
title: Windows Server Azure Edition 的热补丁（预览版）
description: 了解 Windows Server Azure Edition 的热补丁的工作原理以及如何启用
author: ju-shim
ms.service: virtual-machines
ms.subservice: hotpatch
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2f7af8ebc054b49df03a7f03c512db08a5098f2b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723021"
---
# <a name="hotpatch-for-new-virtual-machines-preview"></a>新虚拟机的热补丁（预览版）

> [!IMPORTANT]
> 适用于 Windows Server 的 Automanage 服务目前为公共预览版。 需要执行一个选用过程才能使用下面所述的热补丁功能。
> 此预览版不附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

> [!NOTE]
> 可在以下 Windows Server Azure Edition 映像之一找到热修补功能：Windows Server 2019 Datacenter：Azure Edition (Core)、Windows Server 2022 Datacenter：Azure Edition (Core)

热修补是在受支持的 Windows Server Azure Edition 虚拟机 (VM) 上安装更新的一种新方式，安装后无需重新启动。 本文介绍有关适用于受支持的 Windows Server Azure Edition VM 的热补丁的信息，热补丁具有以下优势：
* 减少重新启动的次数，降低对工作负载的影响
* 更快地部署更新，因为包更小，安装速度更快，并且可以使用 Azure 更新管理器更轻松地协调补丁安装
* 提供更好的保护，因为热补丁更新包只包括安装速度较快且无需重新启动的 Windows 安全更新

## <a name="how-hotpatch-works"></a>热补丁的工作原理

热补丁的工作原理是：首先使用 Windows 更新最新的累积更新来建立基线。 热补丁在该基线的基础上定期发布（例如，每月的第二个星期二）。 热补丁将包含不要求重新启动的更新。 定期（开始是每隔三个月）用新的“最新累积更新”刷新基线。

:::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="热补丁示例计划。":::

有两种类型的基线：“计划基线”和“计划外基线”。 
*  “计划基线”会定期发布，其中热补丁版本为中间版本。  计划基线在该月的“最新累积更新”中包含所有更新，并且需要重新启动。
    * 上面的示例计划显示了一个日历年中的 4 个计划基线发布（图中总共为 5 个）以及 8 个热补丁版本。
* 发布重要更新（例如零日修补程序）时，将发布计划外基线，并且该特定更新不能作为“热补丁”发布。  发布计划外基线时，热补丁版本将替换为该月中的计划外基线。  计划外基线在该月的“最新累积更新”中也包含所有更新，并且也需要重新启动。
    * 上面的示例计划显示了两个计划外基线，这些基线将替代这几个月的热补丁版本（一年中计划外基线的实际数量是无法提前预知的）。

## <a name="regional-availability"></a>区域可用性
热补丁在所有全球 Azure 区域中提供（预览版）。 预览版不支持 Azure 政府区域。

## <a name="how-to-get-started"></a>如何开始使用

> [!NOTE]
> 在预览阶段，只能使用[此链接](https://aka.ms/AutomanageWindowsServerPreview)在 Azure 门户中开始。

要开始在新 VM 上使用热补丁，请执行以下步骤：
1.  启用预览访问
    * 每个订阅都需要一次性预览访问启用。
    * 可以通过 API、PowerShell 或 CLI 启用预览访问权限，如下一节所述。
1.  通过 Azure 门户创建 VM
    * 预览期间，你将需要使用[此链接](https://aka.ms/AutomanageWindowsServerPreview)开始创建。
1.  提供 VM 详细信息
    * 确保在映像下拉列表中选择要使用的受支持的 Windows Server Azure Edition 映像。  本文顶部会列出受支持的映像。
    * 在“管理”选项卡步骤中，向下滚动到“来宾操作系统更新”部分。 你将看到“热修补”设置为“开启”，并且“补丁安装”默认为“Azure 编排的修补”。
    * 默认情况下，将启用“自动管理 VM 最佳做法”
1. 创建新 VM

## <a name="enabling-preview-access"></a>启用预览版访问

### <a name="rest-api"></a>REST API

以下示例说明如何为订阅启用预览版：

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/ InGuestHotPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

功能注册最多可能需要 15 分钟。 若要检查注册状态，请使用以下命令：

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestHotPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```

为订阅注册该功能后，通过将更改传播到计算资源提供程序来完成选用过程。

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell

使用 ```Register-AzProviderFeature``` cmdlet 为订阅启用预览版。

``` PowerShell
Register-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

功能注册最多可能需要 15 分钟。 若要检查注册状态，请使用以下命令：

``` PowerShell
Get-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

为订阅注册该功能后，通过将更改传播到计算资源提供程序来完成选用过程。

``` PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI

使用 ```az feature register``` 为订阅启用预览版。

```
az feature register --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

功能注册最多可能需要 15 分钟。 若要检查注册状态，请使用以下命令：
```
az feature show --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

为订阅注册该功能后，通过将更改传播到计算资源提供程序来完成选用过程。

```
az provider register --namespace Microsoft.Compute
```

## <a name="patch-installation"></a>补丁安装

在预览期间，系统会自动为使用受支持的 Windows Server Azure Edition 映像创建的所有 VM 启用[自动 VM 来宾修补](../virtual-machines/automatic-vm-guest-patching.md)。 启用自动 VM 来宾修补时：
* 在 VM 上自动下载并应用已分类为“关键”或“安全性”的补丁。 
* 在 VM 所在时区的非高峰期应用补丁。
* 由 Azure 管理补丁业务流程，遵循[可用性优先原则](../virtual-machines/automatic-vm-guest-patching.md#availability-first-updates)应用补丁。
* 监视虚拟机运行状况（通过平台运行状况信号来确定），以检测修补失败的情况。

### <a name="how-does-automatic-vm-guest-patching-work"></a>自动 VM 来宾修补的工作原理是什么？

如果在 VM 上启用了[“自动 VM 来宾修补”](../virtual-machines/automatic-vm-guest-patching.md)，则会自动下载并应用分类为“关键”和“安全”的补丁。 在每个月发布新的补丁时，此过程将自动启动。 补丁评估和安装是自动的，该过程会根据需要重新启动 VM。

在 Windows Server Azure Edition VM 上启用热补丁后，系统将使用无需重新启动的热补丁提供每月的大多数安全更新。 在计划基线或计划外基线月份发送的最新累积更新则要求重新启动 VM。 还可能会定期提供额外的“关键”或“安全”类修补程序，这可能需要重新启动 VM。

VM 每隔数天自动评估，并在任意 30 天周期内多次评估，以确定适用于该 VM 的补丁。 这种自动评估确保能够尽早发现任何缺失的补丁。

在发布每月补丁后的 30 天内，将按照[可用性优先原则](../virtual-machines/automatic-vm-guest-patching.md#availability-first-updates)安装补丁。 只会根据 VM 的时区在 VM 的非高峰期安装补丁。 VM 在非高峰期必须正在运行，才能自动安装补丁。 如果 VM 在定期评估期间已关闭，则会评估 VM，并在 VM 打开后的下一次定期评估期间自动安装适用的补丁。 下一次定期评估通常会在几天内发生。

定义更新以及未分类为“关键”或“安全”的其他补丁不会通过自动 VM 来宾修补予以安装。

## <a name="understanding-the-patch-status-for-your-vm"></a>了解 VM 的修补状态

要查看 VM 的修补状态，请导航到 Azure 门户中 VM 的“来宾 + 主机更新”部分。 在“来宾操作系统更新”部分下，单击“转到热补丁（预览）”以查看你的 VM 的最新补丁状态。

可以在这个屏幕上看到 VM 的热补丁状态。 你还可以查看是否存在任何已经可用但尚未安装的 VM 补丁。 如上文“补丁安装”部分中所述，所有安全性和重要更新将使用 [自动 VM 来宾补丁](../virtual-machines/automatic-vm-guest-patching.md) 自动安装在你的 VM 上，并且不需要任何额外的操作。 不会自动安装具有其他更新分类的补丁。 但可以在“更新符合性”选项卡下的可用补丁列表中查看。还可以通过“更新历史记录”查看 VM 上的更新部署历史记录。 将显示过去 30 天的更新历史记录，以及补丁安装详细信息。


:::image type="content" source="media\automanage-hotpatch\hotpatch-management-ui.png" alt-text="热补丁管理。":::

通过自动 VM 来宾修补，会定期自动评估你的 VM 是否有可用的更新。 这些定期评估确保检测到可用的补丁。 你可以在上述“更新”屏幕上查看评估结果，包括上次评估的时间。 还可以选择使用“立即评估”选项随时触发虚拟机的按需补丁评估，并在评估完成后查看结果。

与按需评估类似，你还可以使用“立即安装更新”选项为 VM 按需安装补丁。 在这里，可以选择安装特定补丁类别下的所有更新。 还可以通过提供单个知识库文章的列表来指定要包含或排除的更新。 按需安装的补丁并不是根据“可用性优先”原则来安装的，并且可能需要更多次的重新启动和更长的 VM 停机时间才能安装更新。

## <a name="supported-updates"></a>支持的更新

热补丁涵盖了 Windows 安全更新，并与在常规（非热补丁）Windows 更新通道中发布的安全更新的内容相同。

运行已经启用热补丁的 Windows Server Azure Edition VM 时，需要注意一些重要事项。 安装热补丁程序中未包含的更新时，仍然需要重新启动。 安装新的基线后，也需要定期重新启动。 重新启动会使 VM 与最新的累积更新中包含的非安全补丁保持同步。
* 当前未包含在热补丁程序中的补丁有为 Windows 发布的非安全更新和非 Windows 更新（如 .NET 修补程序）。  此类补丁需要在基线月份内安装，并且需要重新启动。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="what-is-hotpatching"></a>什么是热修补？

* 热修补是一种在 Azure Windows Server Azure Edition VM 上安装更新的新方法，而且安装后无需重新启动。 热修补的工作方式是在不需要重新启动进程的情况下，修补正在运行的进程的内存中代码。

### <a name="how-does-hotpatching-work"></a>热修补的工作方式

* 热修补的工作方式是使用最新的累积更新建立 Windows 更新基线，然后在该基线上构建不需要重新启动即可生效的更新。  将使用新的累积更新定期更新基线。 累积更新包括所有安全更新和质量更新，并且需要重新启动。

### <a name="why-should-i-use-hotpatch"></a>为何应使用热补丁？

* 当你在受支持的 Windows Server Azure Edition 映像中使用热补丁时，你的 VM 将具有更高的可用性（重启次数更少）和更快的更新（安装包更小，安装速度更快，并且需要重新启动）。 该过程会导致 VM 始终处于最新且安全的状态。

### <a name="what-types-of-updates-are-covered-by-hotpatch"></a>热补丁涵盖了哪些类型的更新？

* 热补丁目前涵盖了 Windows 安全更新。

### <a name="when-will-i-receive-the-first-hotpatch-update"></a>何时会收到第一次热补丁更新？

* 热补丁更新通常在每月的第二个星期二发布。 有关详细信息，请参阅以下内容。

### <a name="what-will-the-hotpatch-schedule-look-like"></a>热补丁计划如下所示？

* 热修补的工作方式是使用最新的累积更新建立 Windows 更新基线，然后在该基线上构建，同时热补丁更新每月发布。  在预览期间，将每隔三个月发布一次基线。 请参阅下图，以了解年度三个月计划的示例（包括由于零日修复而导致的计划外基线示例）。

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="热补丁示例计划。":::

### <a name="are-reboots-still-needed-for-a-vm-enrolled-in-hotpatch"></a>对于在热补丁中注册的 VM，是否仍需要重新启动？

* 安装热补丁程序中未包含的更新时，仍然需要重新启动，并且在安装了基线（Windows 更新最新累积更新）之后定期需要重新启动。 重新启动后，将使 VM 与累积更新中包含的所有补丁保持同步。 基线（需要重新启动）将以三个月的节奏开始，并逐渐增加。

### <a name="are-my-applications-affected-when-a-hotpatch-update-is-installed"></a>安装热补丁更新时应用程序是否受到影响？

* 由于热补丁会修补正在运行的进程的内存中代码，而无需重新启动进程，因此，修补过程不会影响应用程序。 请注意，这不同于补丁本身的任何潜在性能和功能问题。

### <a name="can-i-turn-off-hotpatch-on-my-vm"></a>是否可以在 VM 上关闭热补丁？

* 可以通过 Azure 门户关闭 VM 上的热补丁。  关闭热补丁将从热补丁中取消注册 VM，这会将 VM 恢复为 Windows Server 的典型更新行为。  从 VM 上取消注册热补丁后，可以在发布下一个热补丁基线后重新注册该 VM。

### <a name="can-i-upgrade-from-my-existing-windows-server-os"></a>是否可以从现有的 Windows Server 操作系统升级？

* 当前支持从 Windows Server 的现有版本（即 Windows Server 2016 或 2019 的非 Azure 版本）升级至 Windows Server 2022 Datacenter：Azure Edition。 当前不支持升级到 Windows Server 2019 Datacenter：Azure Edition。

### <a name="can-i-use-hotpatch-for-production-workloads-during-the-preview"></a>在预览期间，是否可以将热补丁用于生产工作负载？

* 预览版仅用于测试目的，不能在生产环境中使用。

### <a name="will-i-be-charged-during-the-preview"></a>预览期间是否会向我收费？

* 在预览期间，Windows Server Azure Edition 的许可证免费提供。 但是，为 VM 设置的任何底层基础结构的成本（存储、计算、网络等）仍会向你的订阅计费。

### <a name="how-can-i-get-troubleshooting-support-for-hotpatching"></a>如何获取热修补的故障排除支持？

* 您可以提交[技术支持案例票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。 对于“服务”选项，在“计算”下搜索并选择“运行 Windows 的虚拟机”。 对于问题类型，选择“Azure 功能”，对于问题子类型，选择“自动 VM 来宾修补”。 

## <a name="next-steps"></a>后续步骤

* 在[此处](../automation/update-management/overview.md)详细了解 Azure 更新管理。
* [在此处](../virtual-machines/automatic-vm-guest-patching.md)详细了解自动 VM 来宾修补
