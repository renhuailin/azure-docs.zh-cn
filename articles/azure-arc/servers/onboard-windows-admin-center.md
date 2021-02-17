---
title: 从 Windows 管理中心将混合计算机连接到 Azure
description: 本文介绍如何安装代理，以及如何从 Windows 管理中心使用启用了 Azure Arc 的服务器将计算机连接到 Azure。
ms.date: 10/12/2020
ms.topic: conceptual
ms.openlocfilehash: 5fa7a61b1e3b22503377cbcbe308a82be89dac72
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584776"
---
# <a name="connect-hybrid-machines-to-azure-from-windows-admin-center"></a>从 Windows 管理中心将混合计算机连接到 Azure

您可以通过手动执行一组步骤，为您的环境中的一台或多台 Windows 计算机启用启用了 Azure Arc 的服务器。 或者，你可以使用 [Windows 管理中心](/windows-server/manage/windows-admin-center/understand/what-is) 来部署连接的计算机代理并注册本地服务器，而无需执行此工具外部的任何步骤。

## <a name="prerequisites"></a>先决条件

* 启用 Arc 的服务器-查看 [先决条件](agent-overview.md#prerequisites) ，并验证你的订阅、你的 Azure 帐户和资源是否满足要求。

* Windows 管理中心-查看 [准备环境](/windows-server/manage/windows-admin-center/deploy/prepare-environment) 以部署和 [配置 Azure 集成 ](/windows-server/manage/windows-admin-center/azure/azure-integration)的要求。

* Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 要管理的目标 Windows 服务器必须具有 Internet 连接才能访问 Azure。

### <a name="security"></a>安全性

此部署方法要求您对目标 Windows 计算机或服务器具有管理员权限才能安装和配置代理。 还需要是 [**网关用户**](/windows-server/manage/windows-admin-center/plan/user-access-options#gateway-access-roles) 角色的成员。

## <a name="deploy"></a>部署

执行以下步骤以配置启用了 Arc 的服务器的 Windows server。

1. 登录到 Windows 管理中心。

1. 从 " **概述** " 页上的 "连接" 列表中，在 "连接的 Windows 服务器" 列表中，从列表中选择要连接到该服务器的服务器。

1. 在左侧窗格中，选择 " **Azure 混合服务**"。

1. 在 " **Azure 混合服务** " 页上，选择 " **发现 Azure 服务**"。

1. 在 " **发现 azure 服务** " 页上，在 " **利用 azure 策略和解决方案来使用 azure Arc 管理服务器**" 下，选择 " **设置**"。

1. 如果系统提示对 Azure 进行身份验证，请在 " **服务器的 Settings\Azure Arc** " 页上，选择 " **入门**"。

1. 在 "将 **服务器连接到 Azure** " 页上，提供以下内容：

    1. 在 " **azure 订阅** " 下拉列表中，选择 "azure 订阅"。
    1. 对于 " **资源组**"，选择 " **新建** " 以创建新的资源组，或在 " **资源组** " 下拉列表中，选择要从中注册和管理计算机的现有资源组。
    1. 在“区域”下拉列表中，选择用于存储服务器元数据的 Azure 区域。
    1. 如果计算机或服务器通过代理服务器进行通信以连接到 internet，请选择 " **使用代理服务器**" 选项。 指定代理服务器的 IP 地址或名称，以及计算机将用于与代理服务器进行通信的端口号。

1. 选择 " **设置** " 以继续为启用了 Azure Arc 的服务器配置 Windows server。

Windows server 将连接到 Azure，下载连接的计算机代理，安装它并向启用了 Azure Arc 的服务器注册。 若要跟踪进度，请在菜单中选择 " **通知** "。

若要确认已连接的计算机代理的安装，请在 Windows 管理中心的左窗格中选择 " [**事件**](/windows-server/manage/windows-admin-center/use/manage-servers#events) "，以查看应用程序事件日志中的 *MsiInstaller* 事件。

## <a name="verify-the-connection-with-azure-arc"></a>验证是否与 Azure Arc 连接

安装代理并将其配置为连接到启用了 Azure Arc 的服务器后，请转到 Azure 门户，验证是否已成功连接服务器。 在 [Azure 门户](https://portal.azure.com)中查看你的计算机。

:::image type="content" source="./learn/media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="成功的计算机连接" border="false":::

## <a name="next-steps"></a>后续步骤

* 有关疑难解答信息，请参阅 [连接计算机代理疑难解答指南](troubleshoot-agent-onboard.md)。

* 了解如何使用 [Azure Policy](../../governance/policy/overview.md) 管理计算机，例如，进行 VM [来宾配置](../../governance/policy/concepts/guest-configuration.md)，验证计算机是否向预期的 Log Analytics 工作区报告，使用[用于 VM 的 Azure Monitor](../../azure-monitor/vm/vminsights-enable-policy.md) 启用监视等。

* 详细了解 [Log Analytics 代理](../../azure-monitor/agents/log-analytics-agent.md)。 需要收集操作系统和工作负荷监视数据、使用自动化 runbook 或功能（如更新管理）管理该数据，或使用 [Azure 安全中心](../../security-center/security-center-introduction.md)之类的其他 azure 服务时，需要使用适用于 Windows 和 Linux 的 Log Analytics 代理。