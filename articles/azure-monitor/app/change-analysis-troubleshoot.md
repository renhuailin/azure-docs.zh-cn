---
title: 排查应用程序更改分析问题-Azure Monitor
description: 了解如何对应用程序更改分析中的问题进行故障排除。
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 7200978ce31a47f7bd0d023cecf359b10a1c96de
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520907"
---
# <a name="troubleshoot-application-change-analysis-preview"></a>排查应用程序更改分析 (预览) 

## <a name="having-trouble-registering-microsoft-change-analysis-resource-provider-from-change-history-tab"></a>注册 Microsoft 时遇到问题。 更改 "更改历史记录" 选项卡中的分析资源提供程序

如果是第一次在其与应用程序更改分析集成后查看更改历史记录，则会看到它会自动注册资源提供程序 **ChangeAnalysis**。 在极少数情况下，可能会由于以下原因而失败：

- **你的权限不足，无法注册 ChangeAnalysis 资源提供程序**。 此错误消息表示当前订阅中的角色不具有与之关联的 **Microsoft. 支持/注册/操作** 范围。 如果你不是订阅的所有者，并且是通过同事 (获取共享访问权限的，则可能会发生这种情况，即，查看对资源组的访问权限) 。 若要解决此问题，可以联系订阅所有者来注册 **ChangeAnalysis** 资源提供程序。 这可以通过订阅 Azure 门户来完成 **|资源提供程序** ，并 ```Microsoft.ChangeAnalysis``` 在 UI 中搜索和注册，或通过 Azure PowerShell 或 Azure CLI。

    通过 PowerShell 注册资源提供程序：
    ```PowerShell
    # Register resource provider
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
    ```

- **注册 ChangeAnalysis 资源提供程序失败**。 此消息意味着 UI 发送请求以注册资源提供程序时，会立即失败，这与权限问题无关。 可能是暂时性的 internet 连接问题。 请尝试刷新页面并检查你的 internet 连接。 如果错误仍然存在，请联系 changeanalysishelp@microsoft.com

- **此时间比预期时间长**。 此消息表示注册花费的时间超过2分钟。 这种情况很罕见，但并不一定表示出现了问题。 可以中转到 " **订阅" |** 用于检查 **ChangeAnalysis** 资源提供程序注册状态的资源提供程序。 可以尝试使用 UI 注销、重新注册或刷新，以查看是否有帮助。 如果问题仍然存在，请联系 changeanalysishelp@microsoft.com 以获得支持。
    ![排除 RP 注册花费的时间过长](./media/change-analysis/troubleshoot-registration-taking-too-long.png)

![选择了疑难解答工具的虚拟机的 "诊断和解决问题" 工具的屏幕截图。](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![用于分析虚拟机的最新更改故障排除工具的磁贴的屏幕截图。](./media/change-analysis/analyze-recent-changes.png)

## <a name="azure-lighthouse-subscription-is-not-supported"></a>不支持 Azure Lighthouse 订阅

- **无法查询 ChangeAnalysis 资源提供程序** *，因为不支持消息 Azure lighthouse 订阅，只能在订阅的 home 租户中使用这些更改*。 现在，对于不在 home 租户中的用户，将通过 Azure Lighthouse 订阅注册更改分析资源提供程序存在限制。 我们正在努力解决此限制。 如果这是你的阻塞问题，则有一种解决方法，涉及到创建服务主体，并显式分配角色以允许访问。  请联系 changeanalysishelp@microsoft.com 以了解更多相关信息。

## <a name="an-error-occurred-while-getting-changes-please-refresh-this-page-or-come-back-later-to-view-changes"></a>获取更改时出错。 请刷新此页或稍后返回以查看更改

这是应用程序更改分析服务在无法加载更改时显示的一般错误消息。 一些已知的原因是：

- 来自客户端设备的 Internet 连接错误
- 如果更改分析服务暂时不可用，则在几分钟后刷新页面通常会解决此问题。 如果错误仍然存在，请联系 changeanalysishelp@micorosoft.com

## <a name="you-dont-have-enough-permissions-to-view-some-changes-contact-your-azure-subscription-administrator"></a>你的权限不足，无法查看某些更改。 联系你的 Azure 订阅管理员

这是一般性的未授权错误消息，说明当前用户的权限不足，无法查看更改。 至少需要对资源具有读取者访问权限，才能查看 Azure 资源图和 Azure 资源管理器返回的基础结构更改。 对于 web 应用中的来宾文件更改和配置更改，必须至少具有 "参与者" 角色。

## <a name="failed-to-register-microsoftchangeanalysis-resource-provider"></a>未能注册 ChangeAnalysis 资源提供程序

此消息意味着 UI 发送请求以注册资源提供程序时，会立即失败，这与权限问题无关。 可能是暂时性的 internet 连接问题。 请尝试刷新页面并检查你的 internet 连接。 如果错误仍然存在，请联系 changeanalysishelp@microsoft.com

## <a name="you-dont-have-enough-permissions-to-register-microsoftchangeanalysis-resource-provider-contact-your-azure-subscription-administrator"></a>你的权限不足，无法注册 ChangeAnalysis 资源提供程序。 联系你的 Azure 订阅管理员

此错误消息表示当前订阅中的角色不具有与之关联的 **Microsoft. 支持/注册/操作** 范围。 如果你不是订阅的所有者，并且是通过同事 (获取共享访问权限的，则可能会发生这种情况，即，查看对资源组的访问权限) 。 若要解决此问题，可以联系订阅所有者来注册 **ChangeAnalysis** 资源提供程序。 这可以通过订阅 Azure 门户来完成 **|资源提供程序** ，并 ```Microsoft.ChangeAnalysis``` 在 UI 中搜索和注册，或通过 Azure PowerShell 或 Azure CLI。

通过 PowerShell 注册资源提供程序：

```PowerShell
# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
```

## <a name="next-steps"></a>后续步骤

- 详细了解有助于增强更改分析功能的 [Azure Resource Graph](../../governance/resource-graph/overview.md)。