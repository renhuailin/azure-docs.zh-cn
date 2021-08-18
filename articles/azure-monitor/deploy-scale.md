---
title: 使用 Azure Policy 大规模部署 Azure Monitor
description: 使用 Azure Policy 大规模部署 Azure Monitor 功能。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/08/2020
ms.openlocfilehash: 63da1c8f36f9e2db9593256a071d71ac70ea18bd
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2021
ms.locfileid: "112380025"
---
# <a name="deploy-azure-monitor-at-scale-using-azure-policy"></a>使用 Azure Policy 大规模部署 Azure Monitor
虽然某些 Azure Monitor 功能仅配置一次或有限的几次，但另一些功能必须针对要监视的每个资源重复配置。 本文介绍了如何使用 Azure Policy 大规模实施 Azure Monitor 以确保为所有 Azure 资源一致且准确地配置监视功能。

例如，你需要为所有现有的 Azure 资源和你创建的每个新资源创建诊断设置。 每次创建虚拟机时，你还需要安装并配置代理。 可以使用 PowerShell 或 CLI 等方法执行这些操作，因为这些方法可用于 Azure Monitor 的所有功能。 使用 Azure Policy，你可以实施逻辑，以便在每次创建或修改资源时自动执行相应的配置。


## <a name="azure-policy"></a>Azure Policy
本部分简要介绍了 [Azure Policy](../governance/policy/overview.md)，它使得你可以在整个 Azure 订阅或管理组中以最少的工作量评估和强制实施组织标准。 有关完整的详细信息，请参阅 [Azure Policy 文档](../governance/policy/overview.md)。

通过 Azure Policy，你可以为已创建的任何资源指定配置要求，并识别不合规的资源、阻止资源创建，或添加所需的配置。 它的工作方式是拦截创建新资源或修改现有资源的调用。 它可以做出会带来拒绝请求（如果请求与策略定义中预期的属性不匹配）或将请求标记为不合规这样的后果的响应，也可以部署相关的资源。 你可以使用 **deployIfNotExists** 或 **modify** 策略定义修正现有资源。

Azure Policy 由下表中的对象构成。 有关每个对象的更详细的说明，请参阅 [Azure Policy 对象](../governance/policy/overview.md#azure-policy-objects)。

| 项目 | 说明 |
|:---|:---|
| 策略定义 | 描述资源合规条件以及满足条件时会实现的效果。 这可能是特定类型的所有资源，也可能只是匹配某些属性的资源。 效果可能是简单地将资源标记为合规或者部署相关的资源。 策略定义是使用 JSON 编写的，如 [Azure Policy 定义结构](../governance/policy/concepts/definition-structure.md)中所述。 [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)中介绍了各种效果。
| 策略计划 | 应当一起应用的一组策略定义。 例如，你可能有一个策略定义用于将资源日志发送到 Log Analytics 工作区，有另一个策略定义用于将资源日志发送到事件中心。 可以创建一个包含这两个策略定义的计划，并向资源应用该计划而不是应用各个策略定义。 计划是使用 JSON 编写的，如 [Azure Policy 计划结构](../governance/policy/concepts/initiative-definition-structure.md)中所述。 |
| 分配 | 策略定义或计划在分配到作用域之前不会生效。 例如，将策略分配给某个资源组以将其应用于在该资源中创建的所有资源，或将其应用于某个订阅以将其应用于该订阅中的所有资源。  有关更多详细信息，请参阅 [Azure Policy 分配结构](../governance/policy/concepts/assignment-structure.md)。 |

## <a name="built-in-policy-definitions-for-azure-monitor"></a>Azure Monitor 的内置策略定义
Azure Policy 包括多个与 Azure Monitor 相关的预生成定义。 你可以将这些策略定义分配给现有订阅，也可以将它们作为基础来创建你自己的自定义定义。 有关“监视”类别中的内置策略的完整列表，请参阅 [Azure Monitor的 Azure Policy 内置策略定义](.//policy-reference.md)。

若要查看与监视相关的内置策略定义，请执行以下操作：

1. 在 Azure 门户中转到“Azure Policy”。
2. 选择“定义”。
3. 对于“类型”，请选择“内置”；对于“类别”，请选择“监视”。

  ![Azure 门户中“Azure Policy 定义”页的屏幕截图，其中显示了用于监视类别和内置类型的策略定义的列表。](media/deploy-scale/builtin-policies.png)

## <a name="azure-monitor-agent"></a>Azure Monitor 代理
[Azure Monitor 代理](agents/azure-monitor-agent-overview.md)从 Azure 虚拟机的来宾操作系统中收集监视数据并将数据交付给 Azure Monitor。 它按照[数据收集规则](agents/data-collection-rule-overview.md)将数据配置为从每个代理收集，从而实现大规模收集设置的可管理性，同时仍为部分计算机启用独特的范围配置。  
创建虚拟机时，使用以下策略和策略计划自动安装代理并将其与数据收集规则关联。

### <a name="built-in-policy-initiatives"></a>内置策略计划
可在[此处](agents/azure-monitor-agent-install.md#prerequisites)查看代理安装的先决条件。 

以下是针对 Windows 和 Linux 虚拟机的策略计划，其中包含可执行以下操作的各个策略：
- 在虚拟机上安装 Azure Monitor 代理扩展
- 创建并部署关联，以将虚拟机链接到数据收集规则

  ![“Azure Policy 定义”页面的部分屏幕截图，其中显示了用于配置 Azure Monitor 代理的两个内置策略计划。](media/deploy-scale/built-in-ama-dcr-initiatives.png)  

### <a name="built-in-policy"></a>内置策略  
可以根据自己的需要从各个策略计划中选用各个策略。 例如，如果只想自动安装代理，只需使用计划中的第一个策略，如下所示：  

  ![“Azure Policy 定义”页面的部分屏幕截图，其中显示了用于配置 Azure Monitor 代理的计划中包含的策略。](media/deploy-scale/built-in-ama-dcr-policy.png)  

### <a name="remediation"></a>补救
这些计划或策略将在创建时应用于每个虚拟机。 [补救任务](../governance/policy/how-to/remediate-resources.md)会将计划中的策略定义部署到现有资源，因此你可以为任何已创建的资源配置 Azure Monitor 代理。 当使用 Azure 门户创建分配时，你可以选择同时创建修正任务。 有关修正的详细信息，请参阅[使用 Azure Policy 修正不合规资源](../governance/policy/how-to/remediate-resources.md)。

![AMA 的计划补救](media/deploy-scale/built-in-ama-dcr-remediation.png)


## <a name="diagnostic-settings"></a>诊断设置
[诊断设置](essentials/diagnostic-settings.md)将资源日志和指标从 Azure 资源收集到多个位置，通常是收集到 Log Analytics 工作区，该工作区允许使用[日志查询](logs/log-query-overview.md)和[日志警报](alerts/alerts-log.md)来分析数据。 使用 Policy 可在每次创建资源时自动创建诊断设置。

每个 Azure 资源类型都有需要在诊断设置中列出的一组唯一的类别。 因此，每个资源类型都需要一个单独的策略定义。 某些资源类型具有无需修改便可分配的内置策略定义。 对于其他资源类型，你需要创建自定义定义。

### <a name="built-in-policy-definitions-for-azure-monitor"></a>Azure Monitor 的内置策略定义
每个资源类型都有两个内置的策略定义，一个用于将数据发送到 Log Analytics 工作区，另一个用于将数据发送到事件中心。 如果你只需要一个位置，请为资源类型分配该策略。 如果需要这两者，请为资源分配两个策略定义。

例如，下图显示了 Data Lake Analytics 的内置诊断设置策略定义。

  ![“Azure Policy 定义”页中的部分屏幕截图，显示了 Data Lake Analytics 的两个内置诊断设置策略定义。](media/deploy-scale/builtin-diagnostic-settings.png)

### <a name="custom-policy-definitions"></a>自定义策略定义
对于没有内置策略的资源类型，你需要创建自定义策略定义。 可以在 Azure 门户中通过以下方式手动执行此操作：复制某个现有的内置策略，然后针对你的资源类型修改该策略。 不过，使用 PowerShell 库中的脚本以编程方式创建策略更高效。

脚本 [Create-AzDiagPolicy](https://www.powershellgallery.com/packages/Create-AzDiagPolicy) 为可使用 PowerShell 或 CLI 安装的特定资源类型创建策略文件。 使用以下过程为诊断设置创建自定义策略定义。


1. 请确保已安装 [Azure PowerShell](/powershell/azure/install-az-ps)。
2. 使用以下命令安装该脚本：
  
    ```azurepowershell
    Install-Script -Name Create-AzDiagPolicy
    ```

3. 运行该脚本，使用参数指定要将日志发送到何处。 系统会提示你指定订阅和资源类型。 例如，若要创建将数据发送到 Log Analytics 工作区和事件中心的策略定义，请使用以下命令。

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

4. 另外，还可以在命令中指定订阅和资源类型。 例如，若要为 Azure SQL Server 数据库创建将数据发送到 Log Analytics 工作区和事件中心的策略定义，请使用以下命令。

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -SubscriptionID xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -ResourceType Microsoft.Sql/servers/databases  -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

5. 此脚本为每个策略定义创建单独的文件夹，每个文件夹都包含名为 azurepolicy.json、azurepolicy.rules.json 和 azurepolicy.parameters.json 的三个文件。 如果要在 Azure 门户中手动创建策略，你可以复制并粘贴 azurepolicy.json 的内容，因为它包括整个策略定义。 通过 PowerShell 或 CLI 从命令行使用其他两个文件创建策略定义。

    下面的示例展示了如何通过 PowerShell 和 CLI 安装策略定义。 每个定义都包括元数据，用于指定一个“监视”类别，以便将新策略定义与内置策略定义分组到一起。

      ```azurepowershell
      New-AzPolicyDefinition -name "Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace" -policy .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json -parameter .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json -mode All -Metadata '{"category":"Monitoring"}'
      ```

      ```azurecli
      az policy definition create --name 'deploy-diag-setting-sql-database--workspace' --display-name 'Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace'  --rules 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json' --params 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json' --subscription 'AzureMonitor_Docs' --mode All
      ```

### <a name="initiative"></a>计划
与为每个策略定义创建分配不同，一种常见的策略是创建一个计划，在其中包括策略定义，以便为每个 Azure 服务创建诊断设置。 根据你管理环境的方式，在计划与管理组、订阅或资源组之间创建分配。 此策略具有以下优势：

- 为计划创建单个分配，而不是为每个资源类型创建多个分配。 对多个监视组、订阅或资源组使用同一计划。
- 当需要添加新的资源类型或目标时，只需修改计划即可。 例如，你最初的要求可能是仅将数据发送到 Log Analytics 工作区，但之后想要添加事件中心。 请修改计划，不需要创建新分配。

有关创建计划的详细信息，请参阅[创建和分配计划定义](../governance/policy/tutorials/create-and-manage.md#create-and-assign-an-initiative-definition)。 请考虑以下建议：

- 将“类别”设置为“监视”，以将其与相关的内置和自定义策略定义分组到一起。
- 请使用一个通用的计划参数，而不是为计划中包含的策略定义指定 Log Analytics 工作区和事件中心的详细信息。 这样你就可以轻松地为所有策略定义指定一个通用值，并根据需要更改该值。

![计划定义](media/deploy-scale/initiative-definition.png)

### <a name="assignment"></a>分配 
根据要监视的资源的范围，将计划分配给 Azure 管理组、订阅或资源组。 [管理组](../governance/management-groups/overview.md)特别适用于限定策略作用域，尤其是当你的组织有多个订阅时。

![屏幕截图，显示了 Azure 门户中 Log Analytics 工作区诊断设置的“分配计划”部分中的“基本信息”选项卡设置。](media/deploy-scale/initiative-assignment.png)

通过使用计划参数，你可以同时为计划中的所有策略定义指定工作区或任何其他详细信息。 

![计划参数](media/deploy-scale/initiative-parameters.png)

### <a name="remediation"></a>补救
该计划将在创建时应用于每个虚拟机。 [修正任务](../governance/policy/how-to/remediate-resources.md)将该计划中的策略定义部署到现有资源，因此，你可以为已创建的任何资源创建诊断设置。 当使用 Azure 门户创建分配时，你可以选择同时创建修正任务。 有关修正的详细信息，请参阅[使用 Azure Policy 修正不合规资源](../governance/policy/how-to/remediate-resources.md)。

![计划修正](media/deploy-scale/initiative-remediation.png)


## <a name="vm-insights"></a>VM 见解
[VM 见解](vm/vminsights-overview.md)是 Azure Monitor 中用于监视虚拟机的主要工具。 启用 VM 见解会同时安装 Log Analytics 代理和依赖项代理。 使用 Azure Policy 可确保在创建每个虚拟机时对其进行配置，而不是手动执行这些任务。

> [!NOTE]
> VM 见解包含一项名为 VM 见解策略覆盖范围的功能，让你可在自己的环境中发现并修正不合规的虚拟机。 可以使用此功能，而不是直接针对 Azure VM 和与 Azure Arc 连接的混合虚拟机使用 Azure Policy。对于 Azure 虚拟机规模集，必须使用 Azure Policy 创建分配。
 

VM 见解包括以下内置计划，这些计划可安装两个代理来实现完全监视。 

|名称 |描述 |
|:---|:---|
|启用 VM 见解 | 在 Azure VM 和与 Azure Arc 连接的混合 VM 上安装 Log Analytics 代理和依赖项代理。 |
|为虚拟机规模集启用 Azure Monitor | 在 Azure 虚拟机规模集上安装 Log Analytics 代理和依赖项代理。 |


### <a name="virtual-machines"></a>虚拟机
VM 见解包含一项功能，可用于检查每个范围中的虚拟机数，以确定是否已应用计划，而不是使用 Azure Policy 界面为这些计划创建分配。 然后，可以配置工作区，并使用该接口创建任何所需的分配。

有关此过程的详细信息，请参阅[使用 Azure Policy 启用 VM 见解](./vm/vminsights-enable-policy.md)。

![VM 见解策略](media/deploy-scale/vminsights-policy.png)

### <a name="virtual-machine-scale-sets"></a>虚拟机规模集
若要使用 Azure Policy 为虚拟机规模集启用监视，请根据要监视的资源的范围，将“为虚拟机规模集启用 Azure Monitor”计划分配给 Azure 管理组、订阅或资源组。 [管理组](../governance/management-groups/overview.md)特别适用于限定策略作用域，尤其是当你的组织有多个订阅时。

![Azure 门户中“分配计划”页面的屏幕截图。 计划定义设置为“为虚拟机规模集启用 Azure Monitor”。](media/deploy-scale/virtual-machine-scale-set-assign-initiative.png)

选择要将数据发送到的工作区。 此工作区必须安装 VMInsights 解决方案，如[配置 VM 见解的 Log Analytics 工作区](vm/vminsights-configure-workspace.md)中所述。

![选择工作区](media/deploy-scale/virtual-machine-scale-set-workspace.png)

如果现有虚拟机规模集需要分配此策略，请创建修正任务。

![修正任务](media/deploy-scale/virtual-machine-scale-set-remediation.png)

### <a name="log-analytics-agent"></a>Log Analytics 代理
你可能遇到一些情况要安装 Log Analytics 代理但不安装依赖项代理。 没有内置计划仅仅用于代理，但你可根据 VM 见解提供的内置策略定义来创建自己的计划。

> [!NOTE]
> 无需单独部署依赖项代理，因为它需要 Log Analytics 代理将其数据传递到 Azure Monitor。


|名称 |描述 |
|-----|------------|
|审核 Log Analytics 代理部署 - VM 映像 (OS) 未列出 |如果 VM 映像 (OS) 未在列表中定义且未安装代理，则报告 VM 不合规。 |
|为 Linux VM 部署 Log Analytics 代理 |如果 VM 映像 (OS) 在列表中定义但未安装代理，请为 Linux VM 部署 Log Analytics 代理。 |
|为 Windows VM 部署 Log Analytics 代理 |如果 VM 映像 (OS) 在列表中定义但未安装代理，请为 Windows VM 部署 Log Analytics 代理。 |
| [预览]：Log Analytics 代理应安装在 Linux Azure Arc 计算机中 |如果列表中定义了 VM 映像 (OS) 且未安装代理，则会报告混合 Azure Arc 计算机不符合 Linux VM。 |
| [预览]：Log Analytics 代理应安装在 Windows Azure Arc 计算机中 |如果列表中定义了 VM 映像 (OS) 且未安装代理，则会报告混合 Azure Arc 计算机不符合 Windows VM。 |
| [预览]：将 Log Analytics 代理部署到 Linux Azure Arc 计算机 |如果列表中定义了 VM 映像 (OS) 且未安装代理，请为 Linux 混合 Azure Arc 计算机部署 Log Analytics 代理。 |
| [预览]：将 Log Analytics 代理部署到 Windows Azure Arc 计算机 |如果列表中定义了 VM 映像 (OS) 且未安装代理，请为 Windows 混合 Azure Arc 计算机部署 Log Analytics 代理。 |
|审核虚拟机规模集中的依赖项代理部署 - VM 映像 (OS) 未列出 |如果虚拟机规模集映像 (OS) 未在列表中定义且未安装代理，则报告 VM 不合规。 |
|审核虚拟机规模集中的 Log Analytics 代理部署 - VM 映像 (OS) 未列出 |如果虚拟机规模集映像 (OS) 未在列表中定义且未安装代理，则报告 VM 不合规。 |
|为 Linux 虚拟机规模集部署 Log Analytics 代理 |如果 VM 映像 (OS) 在列表中定义但未安装代理，请为 Linux 虚拟机规模集部署 Log Analytics 代理。 |
|为 Windows 虚拟机规模集部署 Log Analytics 代理 |如果 VM 映像 (OS) 在列表中定义但未安装代理，请为 Windows 虚拟机规模集部署 Log Analytics 代理。 |


## <a name="next-steps"></a>后续步骤

- 阅读有关 [Azure Policy](../governance/policy/overview.md) 的更多内容。
- 阅读有关[诊断设置](essentials/diagnostic-settings.md)的更多内容。
