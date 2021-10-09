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
ms.openlocfilehash: c45be7452d71002c2120e37999c2ecfacb8608b0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124799997"
---
# <a name="preview-azure-automanage-for-virtual-machines"></a>预览版：适用于虚拟机的 Azure Automanage

本文涵盖适用于虚拟机的 Azure Automanage 的相关信息，该服务具有以下优势：

- 通过智能地加入虚拟机来选择最佳做法 Azure 服务
- 根据 Azure 最佳做法自动配置每项服务
- 监视是否存在偏移，并在检测到偏移时予以纠正
- 提供简单的体验（指向、单击、设置、遗忘）

## <a name="overview"></a>概述

适用于虚拟机的 Azure Automanage 是一项服务，使用该服务就无需发现和了解如何在 Azure 中加入和配置对虚拟机有益的某些服务。 这些服务被视为 Azure 最佳做法服务，有助于增强虚拟机的可靠性、安全性和管理。 例如 [Azure 更新管理](../automation/update-management/overview.md)和 [Azure 备份](../backup/backup-overview.md)等服务。

将虚拟机加入到 Azure Automanage 后，每项最佳做法服务都被配置为建议的设置。 每项服务的最佳做法都不同。 例如 Azure 备份的最佳做法可能是每天进行一次虚拟机备份，并使保持期为六个月。

Azure Automanage 也会自动监视偏移情况，并在检测到偏移时予以纠正。 这意味着，如果你的虚拟机已加入到 Azure Automanage，我们不仅会根据 Azure 最佳做法配置它，还会监视它，以确保它在整个生命周期都继续遵循这些最佳做法。 如果虚拟机在这些做法上确实存在偏移或偏离（例如某项服务已登出），我们会进行更正并使虚拟机恢复到所需状态。

Automanage 只存储/处理 VM 所在的地理位置中的客户数据。 在东南亚地区，Automanage 只存储/处理东南亚地区范围的数据。

> [!NOTE]
> 可以在 Azure 虚拟机和已启用 Arc 的服务器上启用 Automanage。 Automanage 目前在美国政府云中不可用。

## <a name="prerequisites"></a>先决条件

尝试在虚拟机上启用 Azure Automanage 之前，需要考虑以下几个先决条件。

- 支持的 [Windows Server 版本](automanage-windows-server.md#supported-windows-server-versions)和 [Linux 发行版](automanage-linux.md#supported-linux-distributions-and-versions)
- VM 必须位于受支持的区域（如下所示）
- 用户必须拥有正确的权限（如下所示）
- Automanage 目前不支持沙盒订阅
- Automanage 目前不支持 Windows 10

### <a name="supported-regions"></a>支持的区域
Automanage 仅支持位于以下区域的 VM：
* 西欧
* 北欧
* Central US
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
* 东南亚

### <a name="required-rbac-permissions"></a>必需的 RBAC 权限
你的帐户需要的 RBAC 角色会略有不同，具体取决于是不是使用新 Automanage 帐户启用的 Automanage。

如果是使用新 Automanage 帐户启用 Automanage，需要：
* 含 VM 的订阅的“所有者”角色，或者
* 含 VM 的订阅的“参与者”和“用户访问管理员”角色 

如果是使用现有 Automanage 帐户启用 Automanage，需要：
* 含 VM 的资源组的“参与者”角色

Automanage 帐户将被授予“参与者”和“资源策略参与者”权限，使其可在 Automanage 管理的计算机上执行操作 。

> [!NOTE]
> 如果希望在连接到位于其他订阅中的工作区的 VM 上使用 Automanage，在每个订阅上都必须拥有上述权限。

## <a name="participating-services"></a>参与服务

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services-1.png" alt-text="智能地加入服务。":::

有关参与的 Azure 服务的完整列表及其支持的环境，请参阅以下内容：
- [适用于 Linux 的 Automanage](automanage-linux.md)
- [适用于 Windows Server 的 Automanage](automanage-windows-server.md)

 我们会将你自动加入这些参与服务。 它们对于我们的最佳做法白皮书至关重要，你可以在我们的[云采用框架](/azure/cloud-adoption-framework/manage/azure-server-management)中找到这些最佳做法。

对于所有这些服务，我们将自动加入、自动配置、监视偏移，并在检测到偏移时进行协调。


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>在 Azure 门户中启用适用于 VM 的 Automanage

在 Azure 门户中，你可以在现有虚拟机上或在新建虚拟机时启用 Automanage。 有关此过程的简明步骤，请查看[适用于虚拟机的 Automanage 快速入门](quick-create-virtual-machines-portal.md)。

如果你是首次为 VM 启用 Automanage，可以在 Azure 门户中搜索“Automanage - Azure 虚拟机最佳做法”。 单击“在现有 VM 上启用”，选择想加入的 VM，然后依次单击“选择”、“启用”即可完成操作  。

只有在我们尝试修正你的 VM 但操作失败时，你可能才需要与此 VM 交互，以管理这些服务。 如果成功修正 VM，我们会将其恢复到合规性状态，甚至不需要向你发出警报。 如需了解详情，请参阅 [VM 的状态](#status-of-vms)。

## <a name="enabling-automanage-for-vms-using-azure-policy"></a>使用 Azure Policy 为 VM 启用 Automanage
还可使用内置的 Azure Policy 在 VM 上大规模启用 Automanage。 该策略具有 DeployIfNotExists 效果，这意味着位于该策略范围内的所有符合条件的 VM 都将自动加入 Automanage VM 最佳做法。

[此处](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3)是该策略的直接链接。

### <a name="how-to-apply-the-policy"></a>如何应用策略
1. 查看策略定义时单击“分配”按钮
1. 选择要应用策略的范围（可以是管理组、订阅或资源组）
1. 在“参数”下，指定 Automanage 帐户的参数、配置文件和效果（效果通常应为 DeployIfNotExists）
    1. 如果没有 Automanage 帐户，必须[创建一个](./automanage-account.md)。
1. 在“修正”下，选中“单击修正任务”复选框。 这将执行到 Automanage 的加入。
1. 单击“查看 + 创建”，并确保所有设置正确无误。
1. 单击“创建”。

## <a name="environment-configuration"></a>环境配置

如果要为虚拟机启用 Automanage，环境是必需的。 环境是此项服务的基础。 它们定义我们将虚拟机加入到哪些服务，并且在某种程度上，还可以定义这些服务的配置。

### <a name="default-environments"></a>默认环境

当前有两个环境可用。

- 开发/测试环境专为开发/测试计算机而设计。
- 生产环境用于生产。

这样区分是为了根据运行的工作负载来建议使用特定的服务。 例如，在生产计算机中，我们会将你自动加入到 Azure 备份。 然而，对于开发/测试计算机，备份服务则是不必要的成本，因为开发/测试计算机通常对业务影响较低。

### <a name="customizing-an-environment-using-preferences"></a>通过首选项自定义环境

除了加入到标准服务外，你还能配置特定的首选项子集。 允许在一系列配置选项范围内使用这些首选项。 例如，如果使用 Azure 备份，你可以定义备份频率和备份发生在星期几。

> [!NOTE]
> 在开发/测试环境中，我们根本不会备份 VM。

你可以通过首选项调整默认环境的设置。 点击[此处](virtual-machines-custom-preferences.md)了解如何创建首选项。

> [!NOTE]
> 启用 Automanage 后，不能更改 VM 上的环境配置。 你需要为该 VM 禁用 Automanage，然后使用所需的环境和首选项重新启用 Automanage。

有关参与的 Azure 服务的完整列表，以及它们是否支持首选项，请参阅以下内容：
- [适用于 Linux 的 Automanage](automanage-windows-server.md)
- [适用于 Windows Server 的 Automanage](automanage-windows-server.md)


## <a name="automanage-account"></a>Automanage 帐户

“Automanage 帐户”是执行自动化操作的安全性上下文或标识。 通常不需要选择“Automanage 帐户”选项，但如果存在你想划分资源自动化管理（可能是在两个系统管理员之间划分）的委派情况，可以使用启用流中的“Automanage 帐户”选项为这两个管理员各定义一个 Azure 标识。

要详细了解 Automanage 帐户以及如何创建一个帐户，请访问 [Automanage 帐户文档](./automanage-account.md)。

## <a name="status-of-vms"></a>VM 的状态

在 Azure 门户中，转到“Automanage – Azure 虚拟机最佳做法”页，该页列出了自动管理的所有 VM。 在这里，你将看到每台虚拟机的总体状态。

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="已配置的虚拟机的列表。":::

对于列出的每台 VM，将显示以下详细信息：名称、环境、配置首选项、状态、操作系统、帐户、订阅和资源组。

“状态”列可显示以下状态：
- 正在进行 - 刚启用且正在配置 VM
- 已配置 - 已配置 VM，且未检测到偏移
- 失败 - VM 出现偏移，且无法修正
- 挂起 - VM 当前未运行，并且 Automanage 将在 VM 下次运行时尝试加入或修正该 VM

如果“状态”显示为“失败”，你可以通过 VM 所在的资源组排除部署故障。 转到“资源组”，选择你的资源组，单击“部署”，然后会看到显示的“失败”状态和错误详细信息 。


## <a name="disabling-automanage-for-vms"></a>为 VM 禁用 Automanage

有一天，你可能会决定在特定 VM 上禁用 Automanage。 例如，你的计算机正在运行一些超级敏感的安全工作负载，你需要对它执行比 Azure 自然锁定更进一步的锁定，因此你需要在 Azure 最佳做法外配置该计算机。

如果要在 Azure 门户中执行该操作，请转到“Automanage – Azure 虚拟机最佳做法”页，该页列出了自动管理的所有 VM。 选择要从 Automanage 禁用的虚拟机旁边的复选框，然后单击“禁用自动管理”按钮。

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="在虚拟机上禁用自动管理。":::

在同意“禁用”之前，请仔细阅读生成的弹出窗口中的消息。

> [!NOTE]
> 在 VM 中禁用自动管理会导致以下行为：
>
> - 不会更改 VM 的配置和它要加入到的服务。
> - 由这些服务产生的任何费用仍可计费，并将继续产生费用。
> - Automanage 偏移监视会立即停止。


首先，我们不会将虚拟机从已加入到的且已配置的任何服务中登出。 因此，由这些服务产生的任何费用都将继续保持可计费状态。 如有必要，你需要从这些服务登出。 任何 Automanage 行为都会立即停止。 例如，我们不会再监视 VM 的偏移。

## <a name="automanage-and-azure-disk-encryption"></a>Automanage 和 Azure 磁盘加密
Automanage 与启用 Azure 磁盘加密 (ADE) 的 VM 兼容。

如果使用生产环境，你还将加入 Azure 备份。 要成功使用 ADE 和 Azure 备份，需要满足一个先决条件：
* 在将启用 ADE 的 VM 加入 Automanage 的生产环境之前，请确保你已按照[本文档](../backup/backup-azure-vms-encryption.md#before-you-start)的“准备工作”部分中的步骤进行操作。

## <a name="next-steps"></a>后续步骤

在本文中，你已了解到：使用适用于虚拟机的 Automanage，你便无需了解、加入和配置最佳做法 Azure 服务。 此外，如果已加入到适用于虚拟机的 Automanage 的计算机偏离环境设置，我们会自动将其恢复到合规性状态。

尝试在 Azure 门户中启用适用于虚拟机的 Automanage。

> [!div class="nextstepaction"]
> [在 Azure 门户中启用适用于虚拟机的 Automanage](quick-create-virtual-machines-portal.md)
