---
title: 适用于虚拟机的 Azure Automanage
description: 了解适用于虚拟机的 Azure Automanage。
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 1d3b2174df5dd83852ce120ec6693ae187a3e795
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101643504"
---
# <a name="azure-automanage-for-virtual-machines"></a>适用于虚拟机的 Azure Automanage

本文介绍有关 Azure Automanage for 虚拟机的信息，这些信息具有以下优势：

- 智能地加入虚拟机以选择最佳实践 Azure 服务
- 每个 Azure 最佳做法自动配置每个服务
- 监视是否有偏移并在检测到时进行纠正
- 提供简单的体验 (点，单击、设置、忘记) 


## <a name="overview"></a>概述

适用于虚拟机的 Azure Automanage 是一项服务，无需发现、了解如何载入，以及如何在 Azure 中配置某些服务来使你的虚拟机受益。 这些服务被视为 Azure 最佳做法服务，有助于增强对虚拟机的可靠性、安全性和管理。 示例服务包括 [azure 更新管理](../automation/update-management/overview.md) 和 [azure 备份](../backup/backup-overview.md)。

在将虚拟机载入 Azure Automanage 后，每个最佳实践服务都将配置为其建议的设置。 对于每个服务，最佳做法都是不同的。 例如，Azure 备份可能是 Azure 备份，其中最好的做法是每天备份一次虚拟机，保持期为6个月。

Azure Automanage 还自动监视是否有偏移，并在检测到它时纠正。 这意味着，如果你的虚拟机载入 Azure Automanage，我们将不只针对 Azure 最佳做法对其进行配置，但我们将监视你的计算机，以确保它继续遵从其整个生命周期中的最佳实践。 如果你的虚拟机确实会偏离或偏离这些实践 (例如，如果某个服务是 offboarded) 的，我们会更正此错误，并将你的计算机恢复到所需状态。

## <a name="prerequisites"></a>先决条件

在虚拟机上尝试启用 Azure Automanage 之前，需要考虑几个先决条件。

- 受支持的 [Windows Server 版本](automanage-windows-server.md#supported-windows-server-versions) 和 [Linux 发行版](automanage-linux.md#supported-linux-distributions-and-versions)
- Vm 必须位于受支持的区域中 (如下所示) 
- 用户必须具有正确的权限 (如下所示) 
- Automanage 目前不支持沙盒订阅

### <a name="supported-regions"></a>支持的区域
Automanage 仅支持位于以下区域的 Vm：
* 西欧
* 北欧
* 美国中部
* 美国东部
* 美国东部 2
* 美国西部
* 美国西部 2
* 加拿大中部
* 美国中西部
* 美国中南部
* 日本东部
* 英国南部
* 澳大利亚东部
* 澳大利亚东南部

### <a name="required-rbac-permissions"></a>必需的 RBAC 权限
你的帐户将需要略有不同的 RBAC 角色，具体取决于是否使用新的 Automanage 帐户来启用 Automanage。

如果要使用新的 Automanage 帐户启用 Automanage，请执行以下操作：
* 订阅的 **所有者** 角色 (s) 包含 vm， _**或**_
* 订阅上的 **参与者** 和 **用户访问管理员** 角色 (s) 包含 vm

如果要使用现有的 Automanage 帐户启用 Automanage：
* 包含 Vm 的资源组上的 **参与者** 角色

> [!NOTE]
> 如果要在连接到不同订阅中的工作区的 VM 上使用 Automanage，则必须具有上述每个订阅的权限。

## <a name="participating-services"></a>参与服务

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services.png" alt-text="智能地内置服务。":::

有关参与 Azure 服务的完整列表以及其受支持的环境，请参阅以下内容：
- [适用于 Linux 的 Automanage](automanage-linux.md)
- [适用于 Windows Server 的 Automanage](automanage-windows-server.md)

 我们会自动将你带到这些参与服务。 它们对于我们的最佳实践白皮书至关重要，你可以在我们的 [云采用框架](/azure/cloud-adoption-framework/manage/azure-server-management)中找到它。

对于所有这些服务，我们将自动载入、自动配置、监视偏移，并在检测到偏差时进行监视。


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>为 Azure 门户中的 Vm 启用 Automanage

在 Azure 门户中，可以在现有虚拟机上或在创建新虚拟机时启用 Automanage。 有关此过程的简明步骤，请查看 [Automanage for virtual 机器快速入门](quick-create-virtual-machines-portal.md)。

如果你是第一次为 VM 启用 Automanage，则可以在 Azure 门户中搜索 **Automanage – Azure 虚拟机最佳实践**。 **在现有 VM 上** 单击 "启用"，选择要登记的 vm，单击 "**选择**"，单击 "**启用**"，即可完成操作。

只有在尝试修正 VM 的情况下，你可能需要与此 VM 交互以管理这些服务，但无法执行此操作。 如果已成功修正 VM，我们会将其恢复为符合性，甚至无需发出警报。 有关更多详细信息，请参阅 [vm 的状态](#status-of-vms)。


## <a name="environment-configuration"></a>环境配置

为虚拟机启用 Automanage 时，需要一个环境。 环境是此服务的基础。 它们定义了将计算机加入到哪些服务，以及这些服务的配置。

### <a name="default-environments"></a>默认环境

目前有两个可用的环境。

- **开发/测试** 环境设计用于开发/测试计算机。
- **生产** 环境适用于生产环境。

这种区别的原因是，根据工作负荷的运行，建议使用某些服务。 例如，在生产计算机中，会自动将你载入 Azure 备份。 但对于开发/测试计算机，备份服务是一种不必要的成本，因为开发/测试计算机通常会降低业务影响。

### <a name="customizing-an-environment-using-preferences"></a>使用首选项自定义环境

除了我们所支持的标准服务外，我们还允许您配置首选项子集。 允许在一系列配置选项中使用这些首选项。 例如，在 Azure 备份的情况下，我们将允许你定义备份的频率和发生时间。

> [!NOTE]
> 在开发/测试环境中，我们根本不会备份 VM。

可以通过首选项调整默认环境的设置。 了解如何在 [此处](virtual-machines-custom-preferences.md)创建首选项。

> [!NOTE]
> 启用 Automanage 后，不能更改 VM 上的 enivonrment 配置。 需要为该 VM 禁用 Automanage，然后使用所需的环境和首选项重新启用 Automanage。

有关参与 Azure 服务的完整列表，以及它们是否支持首选项，请参阅以下内容：
- [适用于 Linux 的 Automanage](automanage-windows-server.md)
- [适用于 Windows Server 的 Automanage](automanage-windows-server.md)


## <a name="automanage-account"></a>Automanage 帐户

Automanage 帐户是安全上下文或用于执行自动操作的标识。 通常情况下，你无需选择 Automanage 帐户选项，但如果存在一个委托方案，其中你要将资源的自动管理划分 (可能在两个系统管理员) 之间，则此选项允许你为这些管理员定义 Azure 标识。

在 Azure 门户体验中，当你在 Vm 上启用 Automanage 时，" **启用 AZURE VM 最佳做法** " 边栏选项卡上提供了一个高级下拉列表，可用于分配或手动创建 Automanage 帐户。

将向订阅 (的订阅授予 " **参与者** " 和 " **资源策略参与者** " 角色，) 包含已加入 Automanage) 的计算机 (。 你可以在多个订阅中的计算机上使用相同的 Automanage 帐户，这将授予该 Automanage 帐户 **参与者** 和对所有订阅的 **资源策略参与者** 权限。

如果 VM 连接到其他订阅中的 Log Analytics 工作区，则还会向 Automanage 帐户授予该其他订阅中的 **参与者** 和 **资源策略参与者** 。

如果要使用新的 Automanage 帐户启用 Automanage，则需要对订阅拥有以下权限： **所有者** 角色或 **参与者** 以及 **用户访问管理员** 角色。

如果要使用现有的 Automanage 帐户启用 Automanage，则需要对包含 Vm 的资源组具有 " **参与者** " 角色。

> [!NOTE]
> 禁用 Automanage 最佳实践时，会保留 Automanage 帐户对任何关联订阅的权限。 请通过转到订阅的 IAM 页手动删除权限或删除 Automanage 帐户。 如果 Automanage 帐户仍在管理任何计算机，则无法将其删除。


## <a name="status-of-vms"></a>Vm 的状态

在 Azure 门户中，请参阅 **Automanage – Azure 虚拟机最佳实践** 页，其中列出了所有自动管理的 vm。 在这里，你将看到每个虚拟机的总体状态。

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="已配置虚拟机的列表。":::

对于列出的每个 VM，将显示以下详细信息：名称、环境、配置首选项、状态、操作系统、帐户、订阅和资源组。

" **状态** " 列可以显示以下状态：
- *正在进行* -VM 刚启用并且正在配置
- *已配置* -VM 已配置，未检测到偏移
- *失败* -VM 有偏移，无法修正
- *挂起* -vm 当前未在运行，并且 Automanage 将在下一次运行时尝试载入或修正 vm

如果 **状态** 显示为 " *失败*"，则可以通过 VM 所在的资源组对部署进行故障排除。 中转到 " **资源组**"，选择资源组，单击 " **部署** "， *并在其中* 显示错误详细信息。


## <a name="disabling-automanage-for-vms"></a>为 Vm 禁用 Automanage

你可以选择一天来禁用某些 Vm 上的 Automanage。 例如，你的计算机运行的是一些超级敏感的安全工作负荷，并且你需要将其锁定，甚至在 Azure 中仍会进行锁定，因此你需要在 Azure 最佳做法的外部配置计算机。

若要在 Azure 门户中执行此操作，请参阅 **Automanage – Azure 虚拟机最佳实践** 页面，其中列出了所有自动管理的 vm。 选择要从 Automanage 禁用的虚拟机旁边的复选框，然后单击 " **禁用 automanagment** " 按钮。

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="在虚拟机上禁用自动管理。":::

在同意“禁用”之前，请仔细阅读生成的弹出窗口中的消息。

> [!NOTE]
> 禁用 VM 中的 automanagement 会导致以下行为：
>
> - VM 的配置和要载入的服务不会更改。
> - 这些服务产生的任何费用都将保持计费，并继续进行。
> - 任何 Automanage 行为会立即停止。


首先，我们不会将虚拟机从我们载入并配置的任何服务中脱离。 因此，这些服务产生的任何费用仍将继续计费。 如有必要，你将需要离开板。 任何 Automanage 行为将立即停止。 例如，我们将不再监视 VM 的偏差。

## <a name="next-steps"></a>后续步骤

在本文中，你已了解虚拟机的 Automanage 提供了一种方法，你无需知道、载入和配置最佳实践 Azure 服务。 此外，如果在环境设置中从虚拟机偏离载入到 Automanage 的计算机，我们会自动将其恢复为符合性。

尝试在 Azure 门户中为虚拟机启用 Automanage。

> [!div class="nextstepaction"]
> [在 Azure 门户中为虚拟机启用 Automanage](quick-create-virtual-machines-portal.md)