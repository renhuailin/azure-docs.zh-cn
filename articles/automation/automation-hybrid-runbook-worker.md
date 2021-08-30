---
title: Azure 自动化混合 Runbook 辅助角色概述
description: 本文概述了混合 Runbook 辅助角色，可以使用这些辅助角色在本地数据中心或云提供商的计算机上运行 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 07/22/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9600a4c38fa2a6f4956e9b1bceb730580c6ce382
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114653072"
---
# <a name="hybrid-runbook-worker-overview"></a>混合 Runbook 辅助角色概述

Azure 自动化中的 Runbook 可能无权访问其他云或本地环境中的资源，因为它们在 Azure 云平台中运行。 利用 Azure 自动化的混合 Runbook 辅助角色功能，既可以直接在托管角色的计算机上运行 Runbook，也可以对环境中的资源运行 Runbook，从而管理这些本地资源。 Runbook 在 Azure 自动化中进行存储和管理，然后发送到一台或多台指定的计算机。

## <a name="runbook-worker-types"></a>Runbook 辅助角色类型

有两种类型的 Runbook 辅助角色 - 系统和用户。 下表描述了它们之间的差异。

|类型 | 说明 |
|-----|-------------|
|**系统** |支持由更新管理功能使用的一组隐藏 Runbook，这些 Runbook 专门用于在 Windows 和 Linux 计算机上安装用户指定的更新。<br> 此类型的混合 Runbook 辅助角色不是混合 Runbook 辅助角色组的成员，因此不会运行面向 Runbook 辅助角色组的 Runbook。 |
|**用户** |支持预期在 Windows 和 Linux 计算机上直接运行的用户定义的 Runbook，这些 Runbook 是一个或多个 Runbook 辅助角色组的成员。 |

混合 Runbook 辅助角色可在 Windows 或 Linux 操作系统上运行，并且此角色依赖于向 Azure Monitor [Log Analytics 工作区](../azure-monitor/logs/design-logs-deployment.md)进行报告的 [Log Analytics 代理](../azure-monitor/agents/log-analytics-agent.md)。 该工作区不仅用于监视计算机是否运行支持的操作系统，还可以用于下载安装混合 Runbook 辅助角色所需的组件。

启用 Azure 自动化[更新管理](./update-management/overview.md)后，连接到 Log Analytics 工作区的任何计算机都会自动配置为系统混合 Runbook 辅助角色。 若要将其配置为用户 Windows 混合 Runbook 辅助角色，请参阅[部署 Windows 混合 Runbook 辅助角色](automation-windows-hrw-install.md)；对于 Linux，请参阅[部署 Linux 混合Runbook 辅助角色](automation-linux-hrw-install.md)。

## <a name="runbook-worker-limits"></a>Runbook 辅助角色限制

下表显示自动化帐户中系统和用户混合 runbook 辅助角色的最大数量。 如果要管理的计算机超过 4,000 台，建议创建其他自动化帐户。

|辅助角色类型| 每个自动化帐户支持的最大数量。|
|---|---|
|System|4000|
|用户 |4000|

## <a name="how-does-it-work"></a>它是如何工作的？

![混合 Runbook 辅助角色概述](media/automation-hybrid-runbook-worker/automation.png)

每个用户混合 Runbook 辅助角色都是你在安装该辅助角色时指定的混合 Runbook 辅助角色组的成员。 一个组可以只包含一个辅助角色，但也可以在一个组中包含多个辅助角色，以实现高可用性。 每台计算机都可以托管一个向单个自动化帐户报告的混合 Runbook 辅助角色；你无法跨多个自动化帐户注册混合辅助角色。 混合辅助角色只能侦听单个自动化帐户中的作业。 对于托管系统混合 Runbook 辅助角色（由更新管理进行管理）的计算机，可以将其添加到混合 Runbook 辅助角色组。 但必须对更新管理和混合 Runbook 辅助角色组成员身份使用同一自动化帐户。

在用户混合 Runbook 辅助角色中启动 Runbook 时，可以指定该辅助角色会在其中运行的组。 组中的每个辅助角色都会轮询 Azure 自动化以查看是否有可用作业。 如果作业可用，获取作业的第一个辅助角色将执行该作业。 作业队列的处理时间取决于混合辅助角色硬件配置文件和负载。 不能指定特定的辅助角色。 混合辅助角色使用轮询机制（每 30 秒一次），并遵循先到先服务的顺序。 根据推送作业的时间，无论哪个混合辅助角色对自动化服务执行 ping 操作，都可提取该作业。 通常，一个混合辅助角色在每次执行 ping 操作时（即每隔 30 秒）可提取四个作业。 如果推送作业的速率高于每 30 秒四个，则混合 Runbook 辅助角色组中的另一个混合辅助角色极有可能提取了该作业。

对于磁盘空间、内存或网络套接字，混合 Runbook 辅助角色没有许多 [Azure 沙盒](automation-runbook-execution.md#runbook-execution-environment)资源[限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。 对混合辅助角色的限制仅与辅助角色自己的资源有关，并且它们不受 Azure 沙盒的[公平共享](automation-runbook-execution.md#fair-share)时间限制的约束。

若要控制如何在混合 Runbook 辅助角色上分发 Runbook 以及何时或如何触发作业，可以针对自动化帐户中不同的混合 Runbook 辅助角色组注册混合辅助角色。 针对特定组或组指定目标作业，以支持执行排列。

## <a name="hybrid-runbook-worker-installation"></a>混合 Runbook 辅助角色安装

安装用户混合 Runbook 辅助角色的过程取决于操作系统。 下表定义了部署类型。

|操作系统  |部署类型  |
|---------|---------|
|Windows     | [自动](automation-windows-hrw-install.md#automated-deployment)<br>[手动](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [手动](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

安装 Windows 计算机的建议方法是，使用 Azure 自动化 Runbook 实现计算机配置过程的自动化。 如果这种方法不可行，你可以执行分步过程来手动安装和配置角色。 对于 Linux 计算机，运行 Python 脚本，在计算机上安装代理。

## <a name="network-planning"></a><a name="network-planning"></a>网络规划

查看 [Azure 自动化网络配置](automation-network-configuration.md#network-planning-for-hybrid-runbook-worker)，以了解有关混合 Runbook 辅助角色所需的端口、URL 和其他网络的详细信息。

### <a name="proxy-server-use"></a>使用代理服务器

如果使用代理服务器在 Azure 自动化与运行 Log Analytics 代理的计算机之间通信，请确保能够访问相应的资源。 来自混合 Runbook 辅助角色和自动化服务的请求的超时为 30 秒。 三次尝试后，请求失败。

### <a name="firewall-use"></a>防火墙使用

如果使用防火墙来限制对 Internet 的访问，则必须将防火墙配置为允许访问。 如果将 Log Analytics 网关用作代理，请确保为混合 Runbook 辅助角色配置 Log Analytics 网关。 请参阅[为自动化混合 Runbook 辅助角色配置 Log Analytics 网关](../azure-monitor/agents/gateway.md)。

### <a name="service-tags"></a>服务标记

Azure 自动化从服务标记 [GuestAndHybridManagement](../virtual-network/service-tags-overview.md) 开始支持 Azure 虚拟网络服务标记。 可以在[网络安全组](../virtual-network/network-security-groups-overview.md#security-rules)或 [Azure 防火墙](../firewall/service-tags.md)中使用服务标记来定义网络访问控制。 创建安全规则时，可以使用服务标记代替特定 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 GuestAndHybridManagement），可以允许或拒绝自动化服务的流量。 此服务标记不支持通过将 IP 范围限制到特定区域来实现更精细的控制。

Azure 自动化服务的服务标记仅提供用于以下场景的 IP：

* 从虚拟网络内触发 Webhook
* 允许混合 Runbook 辅助角色或 VNet 中的 State Configuration 代理与自动化服务通信

>[!NOTE]
>服务标记 GuestAndHybridManagement 当前不支持在 Azure 沙盒中执行 runbook 作业，仅允许直接在混合 Runbook 辅助角色中执行。

## <a name="support-for-impact-level-5-il5"></a>对影响级别 5 (IL5) 的支持

可在 Azure 政府中使用 Azure 自动化混合 Runbook 辅助角色，以下面两种配置中的任何一种来支持影响级别为 5 工作负载：

* [独立虚拟机](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines)。 在部署时，对该计算机使用整个物理主机，从而提供支持 IL5 工作负载所需的必要隔离级别。

* [Azure 专用主机](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-host)，可提供物理服务器，物理服务器可托管专用于一个 Azure 订阅的一个或多个虚拟机。

>[!NOTE]
>通过混合 Runbook 辅助角色进行的计算隔离适用于 Azure 商业云和美国政府云。

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>混合 Runbook 辅助角色的更新管理地址

除了混合 Runbook 辅助角色所需的标准地址和端口，更新管理还具有[网络规划](./update-management/plan-deployment.md#ports)部分下所述的其他网络配置要求。

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>混合 Runbook 辅助角色上的 Azure Automation State Configuration

可以在混合 Runbook 辅助角色上运行 [Azure Automation State Configuration](automation-dsc-overview.md)。 若要管理支持混合 Runbook 辅助角色的服务器的配置，必须将这些服务器添加为 DSC 节点。 请参阅[启用要通过 Azure Automation State Configuration 进行管理的计算机](automation-dsc-onboarding.md)。

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>混合 Runbook 辅助角色上的 Runbook

可以使用 Runbook 来管理本地计算机上的资源，或者针对部署了用户混合 Runbook 辅助角色的本地环境中的资源运行 Runbook。 在这种情况下，可以选择在混合辅助角色上（而不是在自动化帐户中）运行 Runbook。 在混合 Runbook 辅助角色上运行的 Runbook 与在自动化帐户中运行的 Runbook 具有相同的结构。 请参阅[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)。

### <a name="hybrid-runbook-worker-jobs"></a>混合 Runbook 辅助角色作业

混合 Runbook 辅助角色作业在 Windows 上的本地系统帐户下运行，或在 Linux 上的 [nxautomation 帐户](automation-runbook-execution.md#log-analytics-agent-for-linux)下运行。 Azure 自动化处理混合 Runbook 辅助角色上的作业的方式不同于处理 Azure 沙盒中运行的作业的方式。 请参阅[Runbook 执行环境](automation-runbook-execution.md#runbook-execution-environment)。

如果混合 Runbook 辅助角色托管计算机重新启动，则任何正在运行的 Runbook 将从头重启，或者从 PowerShell 工作流 Runbook 的最后一个检查点重启。 如果某个 Runbook 作业重启了 3 次以上，则它会暂停。

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>混合 Runbook 辅助角色的 Runbook 权限

由于在用户混合 Runbook 辅助角色上运行的 Runbook 可访问非 Azure 资源，因此无法使用通常由 Runbook 对 Azure 资源进行身份验证时所用的身份验证机制。 Runbook 可以针对本地资源提供其自己的身份验证，也可以配置使用 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)的身份验证。 还可以指定运行方式帐户，为所有 Runbook 提供用户上下文。

## <a name="view-system-hybrid-runbook-workers"></a>查看系统混合 Runbook 辅助角色

在 Windows 或 Linux 计算机上启用“更新管理”功能之后，可以在 Azure 门户中以清单形式列出系统混合 Runbook 辅助角色组的内容。 你可以在门户中查看最多 2,000 个辅助角色，方法是从所选自动化帐户的左侧窗格的“混合辅助角色组”选项中选择“系统混合辅助角色组”选项卡。

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="自动化帐户系统混合辅助角色组页" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

如果混合辅助角色超过 2000 个，则若要获取所有这些辅助角色的列表，可运行以下 PowerShell 脚本：

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>后续步骤

* 若要了解如何配置 Runbook，使本地数据中心或其他云环境中的过程自动化，请参阅[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)。

* 若要了解如何对混合 Runbook 辅助角色进行故障排除，请参阅[排查混合 Runbook 辅助角色问题](troubleshoot/hybrid-runbook-worker.md#general)。
