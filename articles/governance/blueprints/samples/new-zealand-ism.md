---
title: 限制性的新西兰 ISM 蓝图示例
description: 限制性的新西兰 ISM 蓝图示例概述。 此蓝图示例可帮助客户评估特定控制要求。
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: f64d14bc7fd2a3dd133f1f3fd4da6d5de39f9f01
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659714"
---
# <a name="new-zealand-ism-restricted-blueprint-sample"></a>限制性的新西兰 ISM 蓝图示例

限制性的新西兰 ISM 蓝图示例使用 [Azure Policy](../../policy/overview.md) 提供治理防护措施，帮助你评估特定的[新西兰信息安全手册](https://www.nzism.gcsb.govt.nz/)控制要求。 对于必须实现“限制性的新西兰 ISM”控制的 Azure 部署的任何体系结构，此蓝图可帮助客户部署一组核心策略。

## <a name="control-mapping"></a>控制映射

[Azure Policy 控制映射](../../policy/samples/new-zealand-ism.md)详细介绍了此蓝图中包含的策略定义，以及这些策略定义如何映射到“新西兰信息安全手册”中的 **控制**。 在将资源分配给体系结构时，Azure Policy 会评估这些资源是否不符合已分配的策略定义。 有关详细信息，请参阅 [Azure Policy](../../policy/overview.md)。

## <a name="deploy"></a>部署

若要部署 Azure 蓝图限制性的新西兰 ISM 蓝图示例，必须执行以下步骤：

> [!div class="checklist"]
> - 基于示例创建新的蓝图
> - 将示例副本标记为“已发布” 
> - 将蓝图副本分配到现有的订阅

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。

### <a name="create-blueprint-from-sample"></a>基于示例创建蓝图

首先，通过使用示例作为起点在环境中创建新的蓝图，来实现蓝图示例。

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 在左侧的“开始”页中，选择“创建蓝图”下的“创建”按钮。 

1. 在 _其他示例_ 下找到 **限制性的新西兰 ISM** 蓝图示例，然后选择 **使用此示例**。

1. 输入该蓝图示例的“基本信息”：

   - **蓝图名称**：提供限制性的新西兰 ISM 蓝图示例副本的名称。
   - **定义位置**：使用省略号并选择要将示例副本保存到的管理组。

1. 选择页面顶部的“项目”选项卡，或页面底部的“下一步:项目”。

1. 查看蓝图示例中所包含项目的列表。 许多项目包含稍后我们将要定义的参数。 查看完蓝图示例后，选择“保存草稿”。 

### <a name="publish-the-sample-copy"></a>发布示例副本

现已在环境中创建蓝图示例的副本。 该副本在创建后处于“草稿”模式，必须先将其 **发布**，然后才能分配和部署它。  可根据环境和需求自定义蓝图示例的副本，但这种修改可能不符合限制性的新西兰 ISM 控制要求。

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 在左侧选择“蓝图定义”页。 使用筛选器找到蓝图示例的副本，然后选择它。

1. 选择页面顶部的“发布蓝图”。 在右侧的新窗格中，提供蓝图示例副本的 **版本**。 以后做出修改时，此属性非常有用。 提供 **变更注释**，例如，“基于限制性的新西兰 ISM 蓝图示例发布的第一个版本”。 然后选择页面底部的“发布”。

### <a name="assign-the-sample-copy"></a>分配示例副本

成功 **发布** 蓝图示例的副本后，可将它分配到它所在的管理组中的某个订阅。 在此步骤中，需提供参数来使蓝图示例副本的每个部署保持唯一。

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 在左侧选择“蓝图定义”页。 使用筛选器找到蓝图示例的副本，然后选择它。

1. 选择蓝图定义页面顶部的“分配蓝图”。

1. 提供蓝图分配的参数值：

   - 基础

     - **订阅**：在蓝图示例副本所保存到的管理组中选择一个或多个订阅。 如果选择多个订阅，将使用输入的参数为每个订阅创建一个分配。
     - **分配名称**：系统会根据蓝图的名称预先填充该名称。
       请根据需要更改该名称，或保留原样。
     - **位置**：选择要在其中创建托管标识的区域。 Azure 蓝图使用此托管标识在分配的蓝图中部署所有项目。 若要了解详细信息，请参阅 [Azure 资源的托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)。
     - **蓝图定义版本**：选择蓝图示例副本的 **已发布** 版本。

   - 锁分配

     选择环境的蓝图锁定设置。 有关更多信息，请参阅[蓝图资源锁定](../concepts/resource-locking.md)。

   - 托管标识

     保留默认的系统分配的托管标识选项。

   - 项目参数

     在本部分定义的参数将应用到定义了这些参数的项目。 这些参数属于[动态参数](../concepts/parameters.md#dynamic-parameters) ，因为它们是在分配蓝图期间定义的。 有关完整列表或项目参数及其说明，请参阅[项目参数表](#artifact-parameters-table) 。

1. 输入所有参数后，选择页面底部的“分配”。  随后将创建蓝图分配，并开始部署项目。 部署过程大约需要一小时。 若要检查部署状态，请打开蓝图分配。

> [!WARNING]
> Azure 蓝图服务和内置蓝图示例是 **免费的**。 Azure 资源[按产品定价](https://azure.microsoft.com/pricing/) 。 使用[定价计算器](https://azure.microsoft.com/pricing/calculator/) 可以估算运行此蓝图示例部署的资源所需的成本。

### <a name="artifact-parameters-table"></a>项目参数表

下表提供了蓝图项目参数的列表：

|项目名称|项目类型|参数名称|说明|
|-|-|-|-|
|限制性的新西兰 ISM|策略分配|必须包含在 Windows VM 管理员组中的用户的列表|以分号分隔的应包括在管理员本地组中的用户列表；示例：管理员；myUser1；myUser2|
|限制性的新西兰 ISM|策略分配|必须从 Windows VM“管理员”组中排除的用户的列表|以分号分隔的应从管理员本地组中排除的用户列表；示例：管理员；myUser1；myUser2|
|限制性的新西兰 ISM|策略分配|Windows VM 管理员组只能包含的用户的列表|包含“管理员”本地组的所有预期成员的分号分隔列表；例如：Administrator; myUser1; myUser2|
|限制性的新西兰 ISM|策略分配|用于 VM 代理报告的 Log Analytics 工作区 ID|VM 代理应在其中报告的 Log Analytics 工作区的 ID (GUID)|
|限制性的新西兰 ISM|策略分配|策略效果：应为 Azure Front Door 服务启用 Web 应用程序防火墙 (WAF)|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略效果：SQL Server 的“漏洞评估”设置应包含用来接收扫描报告的电子邮件地址|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略效果：应对面向 Internet 的虚拟机应用自适应网络强化建议|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应该为你的订阅分配了多个所有者|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应在虚拟机上启用磁盘加密|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应当为函数应用禁用远程调试|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略效果：Web 应用程序防火墙 (WAF) 应对应用程序网关使用指定模式|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|应用程序网关的 WAF 模式要求|必须为应用程序网关服务启用预防或检测模式|
|限制性的新西兰 ISM|策略分配|策略的效果：应在 SQL 数据库上启用透明数据加密|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略效果：应在 SQL 托管实例上启用漏洞评估|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|可选：除策略库中的映像之外，还有受支持的 Windows OS 要添加到范围中的自定义 VM 映像列表：部署 - 配置依赖项代理以在 Windows 虚拟机上启用|有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应该为 SQL 服务器预配 Azure Active Directory 管理员|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略效果：应只启用与 Azure Cache for Redis 的安全连接|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应在虚拟机规模集上安装 Endpoint Protection 解决方案|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|包括评估策略时已连接 Arc 的服务器：审核管理员组中缺少任意指定成员的 Windows 计算机|选择“true”即表示你同意按月支付每台已连接 Arc 的虚拟机的费用|
|限制性的新西兰 ISM|策略分配|可选：除策略库中的映像之外，还有受支持的 Windows OS 要添加到范围中的自定义 VM 映像列表：[预览版]：应为列出的虚拟机映像启用日志分析代理|有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md)|
|限制性的新西兰 ISM|策略分配|可选：除策略库中的映像之外，还有受支持的 Linux OS 要添加到范围中的自定义 VM 映像列表：[预览版]：应为列出的虚拟机映像启用日志分析代理|有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md)|
|限制性的新西兰 ISM|策略分配|策略效果: 存储帐户应限制网络访问|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|可选：除策略库中的映像之外，还有受支持的 Windows OS 要添加到范围中的自定义 VM 映像列表：部署 - 配置依赖项代理以在 Windows 虚拟机规模集上启用|有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应该修复虚拟机规模集上安全配置中的漏洞|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|包括评估策略时已连接 Arc 的服务器：审核管理员组中有额外帐户的 Windows 计算机|选择“true”即表示你同意按月支付每台已连接 Arc 的虚拟机的费用|
|限制性的新西兰 ISM|策略分配|策略的效果：应该启用安全传输到存储帐户|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略效果：Web 应用程序防火墙 (WAF) 应对 Azure Front Door 服务使用指定模式|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|Azure Front Door 服务的 WAF 模式要求|必须为 Azure Front Door 服务启用预防或检测模式|
|限制性的新西兰 ISM|策略分配|策略效果：应在计算机中启用用于定义安全应用程序的自适应应用程序控制|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：只多只为订阅指定 3 个所有者|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略效果：[预览版]：应禁止存储帐户公共访问|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略效果：应在虚拟机上启用漏洞评估解决方案|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略效果：应为应用程序网关启用 Web 应用程序防火墙 (WAF)|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：CORS 不应允许所有资源访问 Web 应用程序|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|包括评估策略时已连接 Arc 的服务器：审核未使用安全通信协议的 Windows Web 服务器|选择“true”即表示你同意按月支付每台已连接 Arc 的虚拟机的费用|
|限制性的新西兰 ISM|策略分配|Windows Web 服务器的最低 TLS 版本|使用较低 TLS 版本的 Windows Web 服务器将被评估为不符合|
|限制性的新西兰 ISM|策略分配|可选：除策略库中的映像之外，还有受支持的 Linux OS 要添加到范围中的自定义 VM 映像列表：应在虚拟机规模集中为列出的虚拟机映像启用日志分析代理|有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md)|
|限制性的新西兰 ISM|策略分配|可选：除策略库中的映像之外，还有受支持的 Windows OS 要添加到范围中的自定义 VM 映像列表：应在虚拟机规模集中为列出的虚拟机映像启用日志分析代理|有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应从订阅中删除具有写入权限的外部帐户|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|包括评估策略时已连接 Arc 的服务器：审核管理员组中有指定成员的 Windows 计算机|选择“true”即表示你同意按月支付每台已连接 Arc 的虚拟机的费用|
|限制性的新西兰 ISM|策略分配|策略的效果：应从订阅中删除弃用的帐户|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应该只能通过 HTTPS 访问函数应用|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略效果：Azure 订阅应有用于活动日志的日志配置文件|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|应启用诊断日志的资源类型列表||
|限制性的新西兰 ISM|策略分配|策略的效果：应在计算机上安装系统更新|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应在 API 应用中使用最新的 TLS 版本|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应对订阅中拥有写入权限的帐户启用 MFA|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应在 Windows 服务器上部署 Microsoft IaaSAntimalware 扩展|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：只能通过 HTTPS 访问 Web 应用程序|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略效果：应启用 Azure DDoS 防护标准|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应在对订阅拥有所有者权限的帐户上启用 MFA|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应在 SQL 服务器上启用高级数据安全性|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略效果：应在 SQL 托管实例上启用高级数据安全|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：监视 Azure 安全中心 Endpoint Protection 的缺失情况|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略效果：活动日志至少应保留一年|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略效果：应通过即时网络访问控制来保护虚拟机的管理端口|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：Service Fabric 群集只应使用 Azure Active Directory 进行客户端身份验证|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：只能通过 HTTPS 访问 API 应用|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略效果：审核未启用 Windows Defender 攻击防护的 Windows 计算机|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|包括评估策略时已连接 Arc 的服务器：审核未启用 Windows Defender 攻击防护的 Windows 计算机|选择“true”即表示你同意按月支付每台已连接 Arc 的虚拟机的费用|
|限制性的新西兰 ISM|策略分配|Windows Defender 攻击防护不可用的 Windows 计算机的符合性状态|Windows Defender 攻击防护仅从 Windows 10/带更新 1709 的 Windows Server 开始提供。 如果将此值设置为“不符合”，则会将 Windows Defender 攻击防护不可用的早期版本（例如 Windows Server 2012 R2）的计算机显示为不符合。 如果将此值设置为“符合”，则会将这些计算机显示为符合。|
|限制性的新西兰 ISM|策略分配|策略的效果：应在虚拟机规模集上安装系统更新|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应当为 Web 应用程序禁用远程调试|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应该修复计算机上安全配置中的漏洞|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应在对订阅拥有读取权限的帐户上启用 MFA|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应该修复容器安全配置中的漏洞|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应当为 API 应用禁用远程调试|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略效果：审核允许在不使用密码的情况下从帐户进行远程连接的 Linux 计算机|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|包括评估策略时已连接 Arc 的服务器：审核允许在没有密码的情况下从帐户进行远程连接的 Linux 计算机|选择“true”即表示你同意按月支付每台已连接 Arc 的虚拟机的费用|
|限制性的新西兰 ISM|策略分配|策略的效果：应从订阅中删除拥有所有者权限的已弃用帐户|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应对 SQL Server 启用漏洞评估|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应在 Web 应用中使用最新的 TLS 版本|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略效果：Windows 计算机应符合“安全设置 - 帐户策略”的要求|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|强制执行 Windows VM 本地帐户的密码历史记录|指定密码重用限制 - 在可以重用密码之前需要为用户帐户创建新密码的次数|
|限制性的新西兰 ISM|策略分配|包括评估策略时已连接 Arc 的服务器：Windows 计算机应符合“安全设置 - 帐户策略”的要求|选择“true”即表示你同意按月支付每台已连接 Arc 的虚拟机的费用|
|限制性的新西兰 ISM|策略分配|Windows VM 本地帐户的最长密码期限|指定必须更改用户帐户密码之前可以经过的最大天数；值的格式为两个用逗号分隔的整数，表示包含范围|
|限制性的新西兰 ISM|策略分配|Windows VM 本地帐户的最短密码期限|指定必须至少经过多少天才可以更改用户帐户密码|
|限制性的新西兰 ISM|策略分配|Windows VM 本地帐户的最短密码长度|指定用户帐户密码可以包含的最小字符数|
|限制性的新西兰 ISM|策略分配|密码必须符合 Windows VM 本地帐户的复杂性要求|指明用户帐户密码是否必须是复杂的；如果是，复杂密码不得包含部分或全部用户帐户名；长度至少为 6 个字符；包含大小写、数字和非字母字符的组合|
|限制性的新西兰 ISM|策略分配|策略效果：面向 Internet 的虚拟机应使用网络安全组进行保护|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略效果：审核具有不使用密码的帐户的 Linux 计算机|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|包括评估策略时已连接 Arc 的服务器：审核具有不使用密码的帐户的 Linux 计算机|选择“true”即表示你同意按月支付每台已连接 Arc 的虚拟机的费用|
|限制性的新西兰 ISM|策略分配|策略的效果：应从订阅中删除拥有所有者权限的外部帐户|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应在函数应用中使用最新的 TLS 版本|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略效果：[预览版]：所有 Internet 流量都应通过所部署的 Azure 防火墙进行路由|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|限制性的新西兰 ISM|策略分配|策略的效果：应该修复 SQL 数据库中的漏洞|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|

## <a name="next-steps"></a>后续步骤

有关蓝图及其使用方式的更多文章：

- 了解[蓝图生命周期](../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../how-to/update-existing-assignments.md)。
