---
title: 将已启用 Azure Arc 的服务器加入 Azure Sentinel
description: 了解如何将已启用 Azure Arc 的服务器添加到 Azure Sentinel，并主动监视其安全状态。
ms.date: 07/16/2021
ms.topic: conceptual
ms.openlocfilehash: d37a6b62a5a3d95cc02090b4536db2076b2e1b5c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837226"
---
# <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>将已启用 Azure Arc 的服务器加入 Azure Sentinel

本文旨在帮助用户将已启用 Azure Arc 的服务器加入 [Azure Sentinel](../../sentinel/overview.md)，并开始收集与安全相关的事件。 Azure Sentinel 为企业中的警报检测、威胁可见性、主动搜寻和威胁响应提供了单一解决方案。

## <a name="prerequisites"></a>先决条件

首先，请确保你已满足以下要求：

- [Log Analytics 工作区](../../azure-monitor/logs/data-platform-logs.md)。 有关 Log Analytics 工作区的详细信息，请参阅[设计 Azure 监视日志部署](../../azure-monitor/logs/design-logs-deployment.md)。

- [订阅中启用](../../sentinel/quickstart-onboard.md)了 Azure Sentinel。

- 计算机或服务器已连接到已启用 Azure Arc 的服务器。

## <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>将已启用 Azure Arc 的服务器加入 Azure Sentinel

Azure Sentinel 附带了许多 Microsoft 解决方案的连接器，这些连接器立即可用并且提供实时集成。 对于计算机和虚拟机，可以安装用于收集日志并将其转发到 Azure Sentinel 的 Log Analytics 代理。 已启用 Azure Arc 的服务器支持使用以下方法部署 Log Analytics 代理：

- 使用 VM 扩展框架。

    利用已启用 Azure Arc 的服务器中的这项功能，可以将 Log Analytics 代理 VM 扩展部署到非 Azure Windows 和/或 Linux 服务器。 在由已启用 Azure Arc 的服务器管理的混合计算机或服务器上，可以使用以下方法来管理 VM 扩展：

    - [Azure 门户](manage-vm-extensions-portal.md)
    - [Azure CLI](manage-vm-extensions-cli.md)
    - [Azure PowerShell](manage-vm-extensions-powershell.md)
    - Azure [资源管理器模板](manage-vm-extensions-template.md)

- 使用 Azure Policy。

    在使用此方法时，请使用 Azure Policy [将 Log Analytics 代理部署到 Linux 或 Windows Azure Arc 计算机](../../governance/policy/samples/built-in-policies.md#monitoring)内置策略来审核已启用 Azure Arc 的服务器是否已安装 Log Analytics 代理。 如果该代理未安装，则会使用修正任务来自动部署该代理。 或者，如果你计划通过用于 VM 的 Azure Monitor 来监视计算机，请改为使用[启用用于 VM 的 Azure Monitor](../../governance/policy/samples/built-in-initiatives.md#monitoring) 计划来安装和配置 Log Analytics 代理。

建议使用 Azure Policy 来安装适用于 Windows 或 Linux 的 Log Analytics 代理。

在连接已启用 Arc 的服务器后，数据会开始流式传输到 Azure Sentinel，并会准备就绪供你开始使用。 你可以在[内置仪表板](../../sentinel/get-visibility.md)中查看日志并开始在 Log Analytics 中构建查询以[调查数据](../../sentinel/investigate-cases.md)。

## <a name="next-steps"></a>后续步骤

开始[使用 Azure Sentinel 检测威胁](../../sentinel/detect-threats-built-in.md)。