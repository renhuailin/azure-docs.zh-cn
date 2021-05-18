---
title: 使用资源管理器模板启用 VM 见解
description: 本文介绍如何使用 Azure PowerShell 或 Azure 资源管理器模板为一个或多个 Azure 虚拟机或虚拟机规模集启用 VM 见解。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: fc0c304a3fea81f44e01d3e815f34e44728ea42e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031851"
---
# <a name="enable-vm-insights-using-resource-manager-templates"></a>使用资源管理器模板启用 VM 见解
本文介绍如何使用资源管理器模板为虚拟机或虚拟机规模集启用 VM 见解。 此过程可用于以下对象：

- Azure 虚拟机
- Azure 虚拟机规模集
- 与 Azure Arc 连接的混合虚拟机

## <a name="prerequisites"></a>先决条件

- [创建和配置 Log Analytics 工作区](./vminsights-configure-workspace.md)。 
- 请查看[支持的操作系统](./vminsights-enable-overview.md#supported-operating-systems)，确保要启用的虚拟机或虚拟机规模集的操作系统受到支持。 

## <a name="resource-manager-templates"></a>资源管理器模板

我们已创建示例性的 Azure 资源管理器模板，用于载入虚拟机和虚拟机规模集。 这些模板中包含的方案可以用来在现有的资源上启用监视，并可创建新的启用了监视的资源。

>[!NOTE]
>模板需要部署在与要启用的虚拟机或虚拟机规模集相同的资源组中。


Azure 资源管理器模板在存档文件 (.zip) 中提供，该文件可以从 GitHub 存储库[下载](https://aka.ms/VmInsightsARMTemplates)。 文件内容中包含文件夹，这些文件夹代表每个带模板和参数文件的部署方案。 在运行它们之前，请修改参数文件并指定所需值。 

下载文件包含下述适用于不同方案的模板：

- ExistingVmOnboarding 模板可启用 VM 见解，前提是虚拟机已存在。
- NewVmOnboarding 模板用于创建虚拟机并启用 VM 见解来监视它。
- ExistingVmssOnboarding 模板可启用 VM 见解，前提是虚拟机规模集已存在。
- NewVmssOnboarding 模板用于创建虚拟机规模集并启用 VM 见解来监视它们。
- ConfigureWorkspace 模板可将 Log Analytics 工作区配置为支持 VM 见解，只需启用相关解决方案并收集 Linux 和 Windows 操作系统性能计数器即可。

>[!NOTE]
>如果虚拟机规模集已经存在，并且升级策略已设置为“手动”，则在运行 ExistingVmssOnboarding Azure 资源管理器模板后，无需为实例默认启用 VM 见解 。 必须手动升级实例。

## <a name="deploy-templates"></a>部署模板
可使用[任何部署资源管理器模板的方法](../../azure-resource-manager/templates/deploy-powershell.md)来部署模板（包括以下使用 PowerShell 和 CLI 的示例）。

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```


```azurecli
az deployment group create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```



## <a name="next-steps"></a>后续步骤

现在已为虚拟机启用监视，这些信息可供 VM 见解用于分析目的。

- 要查看已发现的应用程序依赖关系，请参阅[查看 VM 见解地图](vminsights-maps.md)。

- 若要通过 VM 的性能了解瓶颈和整体利用率，请参阅[查看 Azure VM 性能](vminsights-performance.md)。