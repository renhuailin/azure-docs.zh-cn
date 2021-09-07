---
title: Azure 虚拟桌面主机池服务更新 - Azure
description: 如何创建验证主机池以在将更新推广到生产之前监视服务更新。
author: Heidilohr
ms.topic: tutorial
ms.date: 07/23/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 13d340d427d2478d226b966e17bf98bcf2561004
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123110155"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>教程：创建主机池以验证服务更新

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Azure 虚拟桌面对象的 Azure 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Azure 虚拟桌面（经典），请参阅[本文](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md)。

主机池是 Azure 虚拟桌面环境中包含一个或多个相同虚拟机的集合。 我们强烈建议先创建一个要应用服务更新的验证主机池。 这样可以在服务将更新应用于标准或非验证环境之前，监视服务更新。 如果没有验证主机池，则可能无法发现引入错误的更改，这可能会在标准环境中对用户导致故障时间。

若要确保应用适用于最新更新，验证主机池应尽可能地与非验证环境中的主机池类似。 用户应如同连接到标准主机池一样频繁地连接到验证主机池。 如果在主机池上进行自动测试，则应在验证主机池上包含自动测试。

可以使用[诊断功能](diagnostics-role-service.md)或 [Azure 虚拟桌面故障排除文章](troubleshoot-set-up-overview.md)对验证主机池中的问题进行调试。

>[!NOTE]
> 建议保留验证主机池以测试所有将来的更新。

>[!IMPORTANT]
>集成 Azure 资源管理器的 Azure 虚拟桌面目前在启用和禁用验证环境时会遇到麻烦。 当解决此问题后，我们会更新本文。

## <a name="create-your-host-pool"></a>创建主机池

可以按照以下任何文章中的说明创建主机池：
- [教程：使用 Azure 市场或 Azure CLI 创建主机池](create-host-pools-azure-marketplace.md)
- [使用 PowerShell 或 Azure CLI 创建主机池](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>将主机池定义为验证主机池

### <a name="portal"></a>[Portal](#tab/azure-portal)

使用 Azure 门户配置验证主机池：

1. 通过 <https://portal.azure.com> 登录到 Azure 门户。
2. 搜索并选择“Azure 虚拟桌面”。
3. 在“Azure 虚拟桌面”页中，选择“主机池”。
4. 选择要编辑的主机池的名称。
5. 选择“属性”。
6. 在“验证环境”字段中，选择“是”，启用验证环境。
7. 选择“保存”。 这将应用新设置。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

如果尚未这样做，请按照[设置 Azure 虚拟桌面 PowerShell 模块](powershell-module.md)中的说明设置 PowerShell 模块并登录到 Azure。

运行以下 PowerShell cmdlet 可将新主机池定义为验证主机池。 将括号中的值替换为与你的会话相关的值：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -ValidationEnvironment:$true
```

运行以下 PowerShell cmdlet 可确认是否设置了验证属性。 将括号中的值替换为与你的会话相关的值。

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | Format-List
```

cmdlet 的结果应类似于以下输出：

```powershell
    HostPoolName        : hostpoolname
    FriendlyName        :
    Description         :
    Persistent          : False
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnvironment : True
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

请为 Azure CLI 准备环境并登录（如果尚未这样做）。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

要将新主机池定义为验证主机池，请使用 [az desktopvirtualization hostpool update](/cli/azure/desktopvirtualization#az_desktopvirtualization_hostpool_update) 命令：

```azurecli
az desktopvirtualization hostpool update --name "MyHostPool" \
    --resource-group "MyResourceGroup" \
    --validation-environment true
```

使用以下命令可确认是否设置了验证属性。

```azurecli
az desktopvirtualization hostpool show --name "MyHostPool" \
    --resource-group "MyResourceGroup" 
```
---

## <a name="update-schedule"></a>更新计划

服务更新每月进行一次。 如果存在重大问题，则会更频繁地提供关键更新。

如果有任何服务更新，请确保每天至少有一小组用户登录，以验证环境。 建议你定期访问我们的 [TechCommunity 站点](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true)并关注包含 WVDUPdate 的任何帖子，以便随时了解服务更新。

## <a name="next-steps"></a>后续步骤

你已经创建了验证主机池，接下来可以了解如何使用 Azure 服务运行状况来监视 Azure 虚拟桌面部署。

> [!div class="nextstepaction"]
> [设置服务警报](./set-up-service-alerts.md)
