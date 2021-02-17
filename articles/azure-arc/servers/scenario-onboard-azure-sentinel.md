---
title: 将启用了 Azure Arc 的服务器加入 Azure Sentinel
description: 了解如何将启用了 Azure Arc 的服务器添加到 Azure Sentinel，并主动监视其安全状态。
ms.date: 11/16/2020
ms.topic: conceptual
ms.openlocfilehash: 2364ba72ac5b10ec4e1f433cc6d591c3ca389ecd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584736"
---
# <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>将启用了 Azure Arc 的服务器加入 Azure Sentinel

本文旨在帮助你将启用了 Azure Arc 的服务器加入 [Azure Sentinel](../../sentinel/overview.md) ，并开始收集与安全相关的事件。 Azure Sentinel 为企业内的警报检测、威胁可见性、主动搜寻和威胁响应提供单一解决方案。

## <a name="prerequisites"></a>先决条件

首先，请确保你已满足以下要求：

- [Log Analytics 工作区](../../azure-monitor/logs/data-platform-logs.md)。 有关 Log Analytics 工作区的详细信息，请参阅[设计 Azure 监视日志部署](../../azure-monitor/logs/design-logs-deployment.md)。

- [已在订阅中启用](../../sentinel/quickstart-onboard.md)Azure Sentinel。

- 计算机或服务器已连接到启用了 Azure Arc 的服务器。

## <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>将启用了 Azure Arc 的服务器加入 Azure Sentinel

Azure Sentinel 随附了许多适用于 Microsoft 解决方案的连接器，随时可用，并提供实时集成。 对于计算机和虚拟机，可以安装用于收集日志并将其转发到 Azure Sentinel 的 Log Analytics 代理。 启用 Arc 的服务器支持使用以下方法部署 Log Analytics 代理：

- 使用 VM 扩展框架。

    使用启用了 Azure Arc 的服务器中的此功能，可以将 Log Analytics 代理 VM 扩展部署到非 Azure Windows 和/或 Linux 服务器。 可以在混合计算机上或通过启用了 Arc 的服务器管理的服务器上使用以下方法来管理 VM 扩展：

    - [Azure 门户](manage-vm-extensions-portal.md)
    - [Azure CLI](manage-vm-extensions-cli.md)
    - [Azure PowerShell](manage-vm-extensions-powershell.md)
    - Azure [资源管理器模板](manage-vm-extensions-template.md)

- 使用 Azure 策略。

    使用此方法时，请使用 Azure Policy [Deploy Log Analytics 代理到 Linux 或 Windows Azure Arc 计算机](../../governance/policy/samples/built-in-policies.md#monitoring) 的内置策略来审核启用了 Arc 的服务器是否安装了 Log Analytics 代理。 如果未安装代理，则它将使用修正任务自动部署。 或者，如果你计划使用用于 VM 的 Azure Monitor 来监视计算机，则改为使用 [启用用于 VM 的 Azure Monitor](../../governance/policy/samples/built-in-initiatives.md#monitoring) 计划安装和配置 Log Analytics 代理。

建议使用 Azure 策略安装适用于 Windows 或 Linux 的 Log Analytics 代理。

启用 Arc 的服务器后，数据开始流式传输到 Azure Sentinel，并准备好开始使用。 你可以在[内置仪表板](../../sentinel/quickstart-get-visibility.md)中查看日志并开始在 Log Analytics 中构建查询以[调查数据](../../sentinel/tutorial-investigate-cases.md)。

## <a name="next-steps"></a>后续步骤

开始[使用 Azure Sentinel 检测威胁](../../sentinel/tutorial-detect-threats-built-in.md)。