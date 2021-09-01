---
title: 从 Windows Admin Center 将混合计算机连接到 Azure
description: 本文介绍如何从 Windows Admin Center 中安装代理并通过使用已启用 Azure Arc 的服务器将计算机连接到 Azure。
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 1abfe2e69a8f3e7b2ab1abcc2d34c79877b750d3
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323268"
---
# <a name="connect-hybrid-machines-to-azure-from-windows-admin-center"></a>从 Windows Admin Center 将混合计算机连接到 Azure

可以通过手动执行一系列步骤，为环境中的一台或多台 Windows 计算机启用已启用 Azure Arc 的服务器。 或者，可以使用 [Windows Admin Center](/windows-server/manage/windows-admin-center/understand/what-is) 来部署 Connected Machine 代理并注册本地服务器，而无需在此工具之外执行任何步骤。

## <a name="prerequisites"></a>先决条件

* 已启用 Arc 的服务器 - 请查看[先决条件](agent-overview.md#prerequisites)，并验证你的订阅、Azure 帐户和资源是否符合要求。

* Windows Admin Center - 请查看[准备环境](/windows-server/manage/windows-admin-center/deploy/prepare-environment)以部署和[配置 Azure 集成](/windows-server/manage/windows-admin-center/azure/azure-integration)的要求。

* Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 要管理的目标 Windows 服务器必须有 Internet 连接以便访问 Azure。

### <a name="security"></a>安全

此部署方法需要有目标 Windows 计算机或服务器上的管理员权限，以便安装和配置代理。 还需要是[网关用户](/windows-server/manage/windows-admin-center/plan/user-access-options#gateway-access-roles)角色的成员。

## <a name="deploy"></a>部署

请执行以下步骤来配置具有已启用 Arc 的服务器的 Windows 服务器。

1. 登录到 Windows Admin Center。

1. 从“概述”页上的连接列表中，在已连接的 Windows 服务器的列表中选择要连接到的服务器。

1. 在左侧窗格中，选择“Azure 混合服务”。

1. 在“Azure 混合服务”页上，选择“发现 Azure 服务” 。

1. 在“发现 Azure 服务”页上，在“利用 Azure 策略和解决方案来管理使用 Azure Arc 的服务器”下，选择“设置”  。

1. 在“设置\Azure Arc for servers”页上，如果系统提示对 Azure 进行身份验证，请选择“开始” 。

1. 在“将服务器连接到 Azure”页上，提供以下内容：

    1. 在“Azue 订阅”下拉列表框中，选择 Azure 订阅。
    1. 对于“资源组”，请选择“新建”以创建新资源组，或者在“资源组”下拉列表下选择要从中注册和管理计算机的现有资源组  。
    1. 在“区域”下拉列表中，选择用于存储服务器元数据的 Azure 区域。
    1. 如果计算机或服务器通过代理服务器进行通信以连接到 Internet，请选择“使用代理服务器”选项。 使用此配置，代理使用 HTTP 协议通过代理服务器进行通信。 指定计算机要用来与代理服务器通信的代理服务器 IP 地址或名称以及端口号。

1. 选择“设置”，以继续配置具有已启用 Azure Arc 的服务器的 Windows 服务器。

该 Windows 服务器将会连接到 Azure，下载 Connected Machine 代理，安装该代理并向已启用 Azure Arc 的服务器注册。 如果要跟踪进度，请在菜单中选择“通知”。

如果要确认 Connected Machine 代理的安装，请在 Windows Admin Center 中从左侧窗格选择[事件](/windows-server/manage/windows-admin-center/use/manage-servers#events)，以查看应用程序事件日志中的 MsiInstaller 事件。

## <a name="verify-the-connection-with-azure-arc"></a>验证是否与 Azure Arc 连接

安装代理并将其配置为连接到启用了 Azure Arc 的服务器后，请转到 Azure 门户，验证是否已成功连接服务器。 在 [Azure 门户](https://portal.azure.com)中查看你的计算机。

:::image type="content" source="./learn/media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="成功的计算机连接" border="false":::

## <a name="next-steps"></a>后续步骤

* 在 [Connected Machine 代理故障排除指南](troubleshoot-agent-onboard.md)中可以找到故障排除信息。

* 查看[规划和部署指南](plan-at-scale-deployment.md)，以便对按任意规模部署启用了 Azure Arc 的服务器进行规划，并实现集中管理和监视。

* 了解如何使用 [Azure Policy](../../governance/policy/overview.md) 管理计算机，例如，进行 VM [来宾配置](../../governance/policy/concepts/guest-configuration.md)、验证计算机是否向预期的 Log Analytics 工作区报告、使用 [VM 见解](../../azure-monitor/vm/vminsights-enable-policy.md)启用监视，等等。