---
title: 排查应用程序更改分析问题 - Azure Monitor
description: 了解如何排查应用程序更改分析中的问题。
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 91a7cc4b96dcff7a20afccfe5995574b89a8e87b
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108315698"
---
# <a name="troubleshoot-application-change-analysis-preview"></a>排查应用程序更改分析问题（预览）

## <a name="having-trouble-registering-microsoft-change-analysis-resource-provider-from-change-history-tab"></a>注册 Microsoft 时遇到问题。 更改“更改历史记录”选项卡中的分析资源提供程序

如果这是更改历史记录与应用程序更改分析整合后首次查看更改历史记录，将看到它自动注册了资源提供程序 Microsoft.ChangeAnalysis。 在极少数情况下，它可能会由于以下原因而出现故障：

- “你的权限不足，无法注册 Microsoft.ChangeAnalysis 资源提供程序”。 此错误消息表示你在当前订阅中的角色不具备与之关联的 Microsoft.Support/register/action 范围。 如果你不是订阅所有者，并且是通过同事获取的共享访问权限（即，对资源组的查看访问权限），则可能会发生这种情况。 若要解决此问题，可以联系订阅所有者注册 Microsoft.ChangeAnalysis 资源提供程序。 这可以在 Azure 门户中完成，方法是单击“订阅 | 资源提供程序”，搜索 ```Microsoft.ChangeAnalysis``` 并在 UI 中注册，也可以通过 Azure PowerShell 或 Azure CLI 来完成此操作。

    通过 PowerShell 注册资源提供程序：
    ```PowerShell
    # Register resource provider
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
    ```

- “注册 Microsoft.ChangeAnalysis 资源提供程序失败”。 此消息意味着在 UI 发送注册资源提供程序的请求时，立即发生了某种故障，并且该故障与权限问题无关。 这可能是暂时性的 Internet 连接问题。 可以尝试刷新页面并检查 Internet 连接。 如果错误仍然存在，请联系 changeanalysishelp@microsoft.com

- “花费的时间比预期要长”。 此消息表示注册所用的时间超过 2 分钟。 这种情况很罕见，但并不一定表示出现了问题。 可以转到“订阅 | 资源提供程序”检查 Microsoft.ChangeAnalysis 资源提供程序注册状态 。 可以尝试使用 UI 取消注册、重新注册或刷新，看看是否有帮助。 如果问题仍然存在，请联系 changeanalysishelp@microsoft.com 获取支持。
    ![排查 RP 注册问题花费的时间过长](./media/change-analysis/troubleshoot-registration-taking-too-long.png)

![虚拟机的“诊断并解决问题”工具的屏幕截图，其中已选中“故障排除工具”。](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![虚拟机的“分析最新更改”故障排除工具的磁贴的屏幕截图。](./media/change-analysis/analyze-recent-changes.png)

## <a name="azure-lighthouse-subscription-is-not-supported"></a>不支持 Azure Lighthouse 订阅

- 查询 Microsoft.ChangeAnalysis 资源提供程序失败，出现消息“不支持 Azure Lighthouse 订阅，仅订阅的主租户中可进行更改”。 现在，通过 Azure Lighthouse 订阅为不在主租户中的用户注册更改分析资源提供程序存在限制。 我们正在努力解决此限制。 如果这是一种阻塞性问题，则一种解决方法是创建服务主体，并显式分配角色以允许进行访问。  请联系 changeanalysishelp@microsoft.com 了解更多信息。

## <a name="an-error-occurred-while-getting-changes-please-refresh-this-page-or-come-back-later-to-view-changes"></a>获取更改时出错。 请刷新此页面或稍后回来查看更改

这是应用程序更改分析服务在无法加载更改时显示的常规错误消息。 一些已知原因包括：

- 客户端设备的 Internet 连接错误
- 更改分析服务暂时不可用。在几分钟后刷新此页面通常可以解决此问题。 如果错误仍然存在，请联系 changeanalysishelp@micorosoft.com

## <a name="you-dont-have-enough-permissions-to-view-some-changes-contact-your-azure-subscription-administrator"></a>你的权限不足，无法查看某些更改。 请联系 Azure 订阅管理员

这是常规的未授权错误消息，说明当前用户没有足够的权限用于查看更改。 至少需要对资源具有读取者访问权限，才能查看 Azure Resource Graph 和 Azure 资源管理器返回的基础结构更改。 对于 Web 应用来宾文件更改和配置更改，至少需要参与者角色。

## <a name="failed-to-register-microsoftchangeanalysis-resource-provider"></a>注册 Microsoft.ChangeAnalysis 资源提供程序失败

此消息意味着在 UI 发送注册资源提供程序的请求时，立即发生了某种故障，并且该故障与权限问题无关。 这可能是暂时性的 Internet 连接问题。 可以尝试刷新页面并检查 Internet 连接。 如果错误仍然存在，请联系 changeanalysishelp@microsoft.com

## <a name="you-dont-have-enough-permissions-to-register-microsoftchangeanalysis-resource-provider-contact-your-azure-subscription-administrator"></a>你的权限不足，无法注册 Microsoft.ChangeAnalysis 资源提供程序。 请联系 Azure 订阅管理员

此错误消息表示你在当前订阅中的角色不具备与之关联的 Microsoft.Support/register/action 范围。 如果你不是订阅所有者，并且是通过同事获取的共享访问权限（即，对资源组的查看访问权限），则可能会发生这种情况。 若要解决此问题，可以联系订阅所有者注册 Microsoft.ChangeAnalysis 资源提供程序。 这可以在 Azure 门户中完成，方法是单击“订阅 | 资源提供程序”，搜索 ```Microsoft.ChangeAnalysis``` 并在 UI 中注册，也可以通过 Azure PowerShell 或 Azure CLI 来完成此操作。

通过 PowerShell 注册资源提供程序：

```PowerShell
# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
```

## <a name="next-steps"></a>后续步骤

- 详细了解有助于增强更改分析功能的 [Azure Resource Graph](../../governance/resource-graph/overview.md)。
