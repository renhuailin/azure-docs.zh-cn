---
title: 从 Azure 自动化更新管理连接计算机
description: 本文介绍了如何将混合计算机连接到由自动化更新管理进行管理的 Azure Arc。
ms.date: 09/14/2021
ms.topic: conceptual
ms.openlocfilehash: fc5de9146ea07e490e69760397a661f4bbb99eec
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699018"
---
# <a name="connect-hybrid-machines-to-azure-from-automation-update-management"></a>从自动化更新管理将混合计算机连接到 Azure

你可以为一个或多个托管在本地或其他云环境中且可以使用 Azure 自动化更新管理进行管理的 Windows 或 Linux 虚拟机或物理服务器启用已启用 Azure Arc 的服务器。 此加入过程会自动下载和安装 [Connected Machine Agent](agent-overview.md)。 为了将计算机连接到已启用 Azure Arc 的服务器，会使用一个 Azure Active Directory [服务主体](../../active-directory/develop/app-objects-and-service-principals.md)，而不使用特权标识[以交互方式连接](onboard-portal.md)计算机。 此服务主体是在这些计算机的加入过程中自动创建的。

在开始之前，请务必查看[先决条件](agent-overview.md#prerequisites)，并验证你的订阅和资源是否符合要求。 有关支持的区域和其他相关注意事项的信息，请参阅 [支持的 Azure 区域](overview.md#supported-regions)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="how-it-works"></a>工作原理

启动加入过程后，会在租户中创建 Active Directory [服务主体](../../active-directory/fundamentals/service-accounts-principal.md)。 

为了在目标计算机上安装并配置 Connected Machine Agent，将在 Azure 沙盒中运行名为 Add-AzureConnectedMachines 的主 Runbook。 根据计算机上检测到的操作系统，主 Runbook 会调用名为 Add-AzureConnectedMachineWindows 或 Add-AzureConnectedMachineLinux 的子 Runbook，该子 Runbook 直接在计算机中以系统[混合 Runbook 辅助角色](../../automation/automation-hybrid-runbook-worker.md)运行。 Runbook 作业输出会写入作业历史记录，你可以通过 [Azure 门户](../../automation/manage-runbooks.md#view-statuses-in-the-azure-portal)或 [Azure PowerShell](../../automation/manage-runbooks.md#retrieve-job-statuses-using-powershell) 查看其[状态摘要](../../automation/automation-runbook-execution.md#job-statuses)，或深入了解特定 Runbook 作业的详细信息。 在 Azure 自动化中执行 runbook 会在自动化帐户的活动日志中写入详细信息。 有关如何使用日志的详细信息，请参阅[从活动日志中检索详细信息](../../automation/manage-runbooks.md#retrieve-details-from-activity-log)。

最后一步是使用服务主体通过 `azcmagent` 命令将计算机注册为 Azure 中的资源，以建立与 Azure Arc 的连接。

## <a name="prerequisites"></a>先决条件

此方法要求你是[自动化作业操作员](../../automation/automation-role-based-access-control.md#automation-job-operator)角色或更高级别角色的成员，这样你才能在自动化帐户中创建 Runbook 作业。 

如果已启用 Azure Policy 来[管理 Runbook 执行](../../automation/enforce-job-execution-hybrid-worker.md)，并强制 Runbook 执行以混合 Runbook 辅助角色组为目标，则必须禁用此策略。 否则，将计算机加入已启用 Arc 的服务器的 Runbook 作业会失败。 

## <a name="add-machines-from-the-azure-portal"></a>从 Azure 门户添加计算机

请执行以下步骤来为混合计算机配置已启用 Arc 的服务器。 服务器或计算机必须开机并联机才能使该过程成功完成。

1. 在浏览器中转到 [Azure 门户](https://portal.azure.com)。

1. 导航到“服务器 - Azure Arc”页，然后选择左上角的“添加” 。

1. 在“选择方法”页上，选择“从更新管理(预览)添加托管服务器”磁贴，然后选择“添加服务器”  。

1. 在“基本信息”页上，配置以下内容：

    1. 在“资源组”下拉列表中，选择要从中管理计算机的资源组。
    1. 在“区域”下拉列表中，选择用于存储服务器元数据的 Azure 区域。
    1. 如果计算机是通过代理服务器连接到 Internet 进行通信的，请指定计算机用来与代理服务器通信的代理服务器 IP 地址或名称以及端口号。 按格式 `http://<proxyURL>:<proxyport>` 输入值。
    1. 选择“下一步: 计算机”。

1. 在“计算机”页上，从下拉列表中选择“订阅”和“自动化帐户”，后者已启用更新管理功能并包含你要将其加入到已启用 Azure Arc 的服务器的计算机。  

   指定自动化帐户后，以下列表会返回由该自动化帐户的更新管理进行管理的非 Azure 计算机。 Windows 和 Linux 计算机都会列出。对于每台计算机，请选择“添加”。

   可以通过选择“查看所选内容”来查看你所选的内容。如果要删除计算机，请从“操作”列下选择“删除”。   

   确认所选内容后，请选择“下一步: 标记”。

1. 在“标记”页上，指定一个或多个“名称/值”对以支持你的标准  。 选择“下一步: 查看 + 添加”。

1. 在“查看 + 添加”页上查看摘要信息，然后选择“添加计算机”。  如果仍需进行更改，请选择“上一页”。

## <a name="verify-the-connection-with-azure-arc"></a>验证是否与 Azure Arc 连接

安装代理并将其配置为连接到已启用 Azure Arc 的服务器后，请转到 Azure 门户，验证是否已成功连接服务器。 在 [Azure 门户](https://aka.ms/hybridmachineportal)中查看计算机。

![服务器连接成功](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>后续步骤

- 在 [Connected Machine 代理故障排除指南](troubleshoot-agent-onboard.md)中可以找到故障排除信息。

- 查看[规划和部署指南](plan-at-scale-deployment.md)，以便对按任意规模部署启用了 Azure Arc 的服务器进行规划，并实现集中管理和监视。

- 了解如何使用 [Azure Policy](../../governance/policy/overview.md) 管理计算机，例如，进行 VM [来宾配置](../../governance/policy/concepts/guest-configuration.md)、验证计算机是否向预期的 Log Analytics 工作区报告、使用 [VM 见解](../../azure-monitor/vm/vminsights-enable-policy.md)启用监视，等等。