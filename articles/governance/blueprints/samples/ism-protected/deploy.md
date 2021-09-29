---
title: 部署澳大利亚政府 ISM PROTECTED 蓝图示例
description: 澳大利亚政府 ISM PROTECTED 蓝图示例的部署步骤，包括蓝图项目参数详细信息。
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: 630453b0673d07ffb43380a26af9871d394c0584
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128632246"
---
# <a name="deploy-the-australian-government-ism-protected-blueprint-sample"></a>部署澳大利亚政府 ISM PROTECTED 蓝图示例

若要部署 Azure 蓝图 ISM PROTECTED 蓝图示例，必须执行以下步骤：

> [!div class="checklist"]
> - 基于示例创建新的蓝图
> - 将示例副本标记为“已发布” 
> - 将蓝图副本分配到现有的订阅

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。

## <a name="create-blueprint-from-sample"></a>基于示例创建蓝图

首先，通过使用示例作为起点在环境中创建新的蓝图，来实现蓝图示例。

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 在左侧的“开始”页中，选择“创建蓝图”下的“创建”按钮。 

1. 在“其他示例”下找到“ISM PROTECTED”蓝图示例，然后选择“使用此示例”。

1. 输入该蓝图示例的“基本信息”：

   - **蓝图名称**：提供 ISM PROTECTED 蓝图示例副本的名称。
   - **定义位置**：使用省略号并选择要将示例副本保存到的管理组。

1. 选择页面顶部的“项目”选项卡，或页面底部的“下一步:   项目”。

1. 查看构成蓝图示例的项目列表。 许多项目包含稍后我们将要定义的参数。 查看完蓝图示例后，选择“保存草稿”。 

## <a name="publish-the-sample-copy"></a>发布示例副本

现已在环境中创建蓝图示例的副本。 该副本在创建后处于“草稿”模式，必须先将其 **发布**，然后才能分配和部署它。 可根据环境和需求自定义蓝图示例的副本，但这种修改可能不符合 ISM PROTECTED 控制要求。

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 在左侧选择“蓝图定义”页。 使用筛选器找到蓝图示例的副本，然后选择它。

1. 选择页面顶部的“发布蓝图”。 在右侧的新窗格中，提供蓝图示例副本的 **版本**。 以后做出修改时，此属性非常有用。 提供更改注释，例如，“基于 ISM PROTECTED 蓝图示例发布的第一个版本。” 然后选择页面底部的“发布”。

## <a name="assign-the-sample-copy"></a>分配示例副本

成功 **发布** 蓝图示例的副本后，可将它分配到它所在的管理组中的某个订阅。 在此步骤中，需提供参数来使蓝图示例副本的每个部署保持唯一。

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 在左侧选择“蓝图定义”页。 使用筛选器找到蓝图示例的副本，然后选择它。

1. 选择蓝图定义页面顶部的“分配蓝图”。

1. 提供蓝图分配的参数值：

   - 基础

     - **订阅**：在蓝图示例副本所保存到的管理组中选择一个或多个订阅。 如果选择多个订阅，将使用输入的参数为每个订阅创建一个分配。
     - **分配名称**：系统会根据蓝图的名称预先填充该名称。
       请根据需要更改该名称，或保留原样。
     - **位置**：选择要在其中创建托管标识的区域。 Azure 蓝图使用此托管标识在分配的蓝图中部署所有项目。 若要了解详细信息，请参阅 [Azure 资源的托管标识](../../../../active-directory/managed-identities-azure-resources/overview.md)。
     - **蓝图定义版本**：选择蓝图示例副本的 **已发布** 版本。

   - 锁分配

     选择环境的蓝图锁定设置。 有关更多信息，请参阅[蓝图资源锁定](../../concepts/resource-locking.md)。

   - 托管标识

     保留默认的系统分配的托管标识选项。

   - 项目参数

     在本部分定义的参数将应用到定义了这些参数的项目。 这些参数属于[动态参数](../../concepts/parameters.md#dynamic-parameters) ，因为它们是在分配蓝图期间定义的。 有关完整列表或项目参数及其说明，请参阅[项目参数表](#artifact-parameters-table) 。

1. 输入所有参数后，选择页面底部的“分配”。  随后将创建蓝图分配，并开始部署项目。 部署过程大约需要一小时。 若要检查部署状态，请打开蓝图分配。

> [!WARNING]
> Azure 蓝图服务和内置蓝图示例是 **免费的**。 Azure 资源[按产品定价](https://azure.microsoft.com/pricing/) 。 使用[定价计算器](https://azure.microsoft.com/pricing/calculator/) 可以估算运行此蓝图示例部署的资源所需的成本。

## <a name="artifact-parameters-table"></a>项目参数表

下表提供了蓝图项目参数的列表：

|项目名称|项目类型|参数名称|说明|
|-|-|-|-|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应为 VM 配置的 Log Analytics 工作区 ID|这是应为 VM 配置的 Log Analytics 工作区的 ID (GUID)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应启用诊断日志的资源类型列表|用于审核是否未启用诊断日志设置的资源类型列表。 [Azure Monitor 资源日志类别](../../../../azure-monitor/essentials/resource-logs-categories.md#supported-log-categories-per-resource-type)中提供了可接受的值。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应该从 Windows VM 管理员组中排除的用户的列表|以分号分隔的应从管理员本地组中排除的成员列表。 例如：管理员；myUser1；myUser2|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应该包括在 Windows VM 管理员组中的用户的列表|以分号分隔的应包括在管理员本地组中的成员列表。 例如：管理员；myUser1；myUser2|
|\[预览\]：为 Linux VM 规模集(VMSS)部署 Log Analytics 代理|策略分配|Linux VM 规模集 (VMSS) 的 Log Analytics 工作区|如果此工作区超出分配范围，则必须手动将“Log Analytics 参与者”权限（或类似权限）授予策略分配的主体 ID。|
|\[预览\]：为 Linux VM 规模集(VMSS)部署 Log Analytics 代理|策略分配|可选：支持将 Linux OS 添加到范围的 VM 映像列表|可以使用空数组来表示没有可选参数：\[\]|
|\[预览\]：为 Linux VM 部署 Log Analytics 代理|策略分配|Linux VM 的 Log Analytics 工作区|如果此工作区超出分配范围，则必须手动将“Log Analytics 参与者”权限（或类似权限）授予策略分配的主体 ID。|
|\[预览\]：为 Linux VM 部署 Log Analytics 代理|策略分配|可选：支持将 Linux OS 添加到范围的 VM 映像列表|可以使用空数组来表示没有可选参数：\[\]|
|\[预览\]：为 Windows VM 规模集(VMSS)部署 Log Analytics 代理|策略分配|Windows VM 规模集 (VMSS) 的 Log Analytics 工作区|如果此工作区超出分配范围，则必须手动将“Log Analytics 参与者”权限（或类似权限）授予策略分配的主体 ID。|
|\[预览\]：为 Windows VM 规模集(VMSS)部署 Log Analytics 代理|策略分配|可选：支持将 Windows OS 添加到范围的 VM 映像列表|可以使用空数组来表示没有可选参数：\[\]|
|\[预览\]：为 Windows VM 部署 Log Analytics 代理|策略分配|Windows VM 的 Log Analytics 工作区|如果此工作区超出分配范围，则必须手动将“Log Analytics 参与者”权限（或类似权限）授予策略分配的主体 ID。|
|\[预览\]：为 Windows VM 部署 Log Analytics 代理|策略分配|可选：支持将 Windows OS 添加到范围的 VM 映像列表|可以使用空数组来表示没有可选参数：\[\]|
|在存储帐户上部署高级威胁防护|策略分配|效果|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|对 SQL 服务器部署审核|策略分配|保持期的值（天数，0 表示保持期无限制） |保留天数（可选，如果未指定，则为 180 天） |
|对 SQL 服务器部署审核|策略分配|要进行 SQL Server 审核的存储帐户的资源组名称|审核针对 Azure 存储帐户（将在 SQL Server 所在的每个区域中创建的存储帐户，由该区域中的所有服务器共享）中审核日志的写入数据库事件。 重要提示 - 为了正确地进行审核，请勿删除或重命名资源组或存储帐户。|
|为网络安全组部署诊断设置|策略分配|适用于网络安全组诊断的存储帐户前缀|此前缀将与网络安全组位置结合使用，一起构成已创建的存储帐户的名称。|
|为网络安全组部署诊断设置|策略分配|适用于网络安全组诊断的存储帐户的资源组名称（必须存在） |将在其中创建存储帐户的资源组。 此资源组必须已存在。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|资源和资源组允许的位置|你的组织在部署资源时可以指定的 Azure 位置的列表。 所提供的此值还会由策略计划中的“允许的位置”策略使用。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应对 SQL 托管实例启用漏洞评估|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应对 SQL Server 启用漏洞评估|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应在虚拟机上启用漏洞评估|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应为存储帐户启用异地冗余存储|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应为 Azure Database for MariaDB 启用异地冗余备份|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应为 Azure Database for MySQL 启用异地冗余备份|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应为 Azure Database for PostgreSQL 启用异地冗余备份|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应该强化面向 Internet 的虚拟机的网络安全组规则|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|只能通过 HTTPS 访问 Web 应用程序|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应该只能通过 HTTPS 访问函数应用|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应从订阅中删除具有写入权限的外部帐户|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应从订阅中删除拥有读取权限的外部帐户|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应从订阅中删除拥有所有者权限的外部帐户|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应从订阅中删除拥有所有者权限的已弃用帐户|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应从订阅中删除弃用的帐户|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|CORS 不应允许所有资源都能访问你的 Web 应用程序|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应在虚拟机规模集上安装系统更新|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应在对订阅拥有读取权限的帐户上启用 MFA|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应在对订阅拥有所有者权限的帐户上启用 MFA|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应在对订阅拥有写入权限的帐户上启用 MFA|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应为 Azure SQL 数据库启用长期异地冗余备份|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|从 Windows VM 管理员组排除的用户的列表|以分号分隔的应从管理员本地组中排除的成员列表。 例如：管理员；myUser1；myUser2|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应配置 VM 的 Log Analytics 工作区 ID|这是应为 VM 配置的 Log Analytics 工作区的 ID (GUID)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|SQL Server 的漏洞评估设置应包含用来接收扫描报告的电子邮件地址|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应在面向 Internet 的虚拟机上应用自适应网络强化建议|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应该为你的订阅分配了多个所有者|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应在虚拟机上启用磁盘加密| 有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应对函数应用禁用远程调试|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应在 SQL 数据库上启用透明数据加密| 有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应对 SQL 托管实例启用漏洞评估|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应该为 SQL 服务器预配 Azure Active Directory 管理员|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应该启用只能通过安全方式连接到 Redis 缓存|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应在虚拟机规模集上安装 Endpoint Protection 解决方案 |有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|可选：支持将 Windows OS 添加到范围的 VM 映像列表|可以使用空数组来表示没有可选参数：[]
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|可选：支持将 Linux OS 添加到范围的 VM 映像列表 |可以使用空数组来表示没有可选参数：[]|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|审核对存储帐户的不受限的网络访问|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应该修复虚拟机规模集上安全配置中的漏洞|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应该启用安全传输到存储帐户  |有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应在虚拟机上启用自适应应用程序控制|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|只多只为订阅指定 3 个所有者|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|[预览版] 应在虚拟机上启用漏洞评估  |有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|CORS 不应允许所有资源都能访问你的 Web 应用程序|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应从订阅中删除具有写入权限的外部帐户 |  有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应从订阅中删除弃用的帐户 |有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应只能通过 HTTPS v2 访问函数应用  |有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应该通过漏洞评估解决方案修复漏洞  |有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|Azure 订阅应有用于活动日志的日志配置文件  |有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应启用诊断日志的资源类型列表|用于审核是否未启用诊断日志设置的资源类型列表。 [Azure Monitor 资源日志类别](../../../../azure-monitor/essentials/resource-logs-categories.md#supported-log-categories-per-resource-type)中提供了可接受的值。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应在计算机上安装系统更新|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应为应用服务使用最新 TLS 版本|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应对订阅中拥有写入权限的帐户启用 MFA|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应在 Windows 服务器上部署 Microsoft IaaSAntimalware 扩展|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。 |
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应只能通过 HTTPS v2 访问 Web 应用程序  |有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。 |
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应启用 DDoS 防护标准版  |有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。 |
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应在对订阅拥有所有者权限的帐户上启用 MFA |有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应在 SQL 服务器上启用高级数据安全性 |有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应在 SQL 托管实例上启用高级数据安全性 |有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|监视 Azure 安全中心 Endpoint Protection 的缺失情况  |有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应对虚拟机应用实时网络访问控制 |有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|Service Fabric 群集只应使用 Azure Active Directory 进行客户端身份验证 |有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应该只能通过 HTTPS v2 访问应用服务 |有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应在虚拟机规模集上安装系统更新  |有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应禁用 Web 应用程序的远程调试|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应该修复计算机上安全配置中的漏洞  |有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。|
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应在对订阅拥有读取权限的帐户上启用 MFA  |有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。 |
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|强制密码历史  | 指定密码重用限制 - 在可以重用密码之前需要为用户帐户创建新密码的次数。 |
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|最长密码期限  | 指定最久经过多少天就必须更改用户帐户密码。 该值的格式是由逗号分隔的两个整数，表示包含范围。 |
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|最短密码期限 |   指定必须至少经过多少天才可以更改用户帐户密码。 |
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|最短密码长度  | 指定用户帐户密码可以包含的最小字符数。 |
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|密码必须符合复杂性要求|指定用户帐户密码是否必须复杂。 如果是，复杂密码不得包含部分或全部用户帐户名；长度至少为 6 个字符；包含大小写、数字和非字母字符的组合。 |
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应该修复容器安全配置中的漏洞|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。 |
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应为应用服务禁用远程调试|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。 |
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应从订阅中删除拥有所有者权限的已弃用帐户|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。 |
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应对 SQL Server 启用漏洞评估|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。 |
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应在 Web 应用中使用最新的 TLS 版本|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。 |
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|面向 Internet 的虚拟机应使用网络安全组进行保护|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。 |
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应从订阅中删除拥有所有者权限的外部帐户|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。 |
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应在函数应用中使用最新的 TLS 版本|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。 |
|\[预览\]：审核澳大利亚政府 ISM PROTECTED 控件，并部署特定 VM 扩展来支持审核要求|策略分配|应该修复 SQL 数据库中的漏洞|有关策略效果的信息，可参阅[了解 Azure Policy 效果](../../../policy/concepts/effects.md)。 |

## <a name="next-steps"></a>后续步骤

了解澳大利亚政府 ISM PROTECTED 蓝图示例的部署步骤后，请访问以下文章来了解蓝图和控制映射：

> [!div class="nextstepaction"]
> [ISM PROTECTED 蓝图 - 概述](./index.md)
> [ISM PROTECTED 蓝图 - 控制映射](./control-mapping.md)

有关蓝图和如何使用这些蓝图的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。
