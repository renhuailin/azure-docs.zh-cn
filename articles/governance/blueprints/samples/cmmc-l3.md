---
title: CMMC 级别 3 蓝图示例
description: CMMC 级别 3 蓝图示例概述。 此蓝图示例可帮助客户评估特定控制要求。
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: 2925acf75ada0bd950ca52e3fe63b9c97b5e34ad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128632322"
---
# <a name="cmmc-level-3-blueprint-sample"></a>CMMC 级别 3 蓝图示例

CMMC 级别 3 蓝图示例使用 [Azure Policy](../../policy/overview.md) 提供治理防护措施，帮助你评估特定的[网络安全成熟度模型认证 (CMMC) 框架](https://www.acq.osd.mil/cmmc/index.html)控制要求。 此蓝图帮助客户为 Azure 部署的任何必须实施 CMMC 级别 3 控制要求的体系结构部署一组核心策略。

## <a name="control-mapping"></a>控制映射

[Azure Policy 控制映射](../../policy/samples/cmmc-l3.md)提供了有关此蓝图中包含的策略定义的详细信息，以及这些策略定义如何映射到 CMMC 框架中的控制要求。 在将资源分配给体系结构时，Azure Policy 会评估这些资源是否不符合已分配的策略定义。 有关详细信息，请参阅 [Azure Policy](../../policy/overview.md)。

## <a name="deploy"></a>部署

若要部署 Azure 蓝图 CMMC 级别 3 蓝图示例，必须执行以下步骤：

> [!div class="checklist"]
> - 基于示例创建新的蓝图
> - 将示例副本标记为“已发布” 
> - 将蓝图副本分配到现有的订阅

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。

### <a name="create-blueprint-from-sample"></a>基于示例创建蓝图

首先，通过使用示例作为起点在环境中创建新的蓝图，来实现蓝图示例。

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 在左侧的“开始”页中，选择“创建蓝图”下的“创建”按钮。

1. 在“其他示例”下找到“CMMC 级别 3”蓝图示例，然后选择“使用此示例”。

1. 输入该蓝图示例的“基本信息”：

   - **蓝图名称**：为你的 CMMC 级别 3 蓝图示例副本提供一个名称。
   - **定义位置**：使用省略号并选择要将示例副本保存到的管理组。

1. 选择页面顶部的“项目”选项卡，或页面底部的“下一步:项目”。

1. 查看蓝图示例中所包含项目的列表。 许多项目包含稍后我们将要定义的参数。 查看完蓝图示例后，选择“保存草稿”。 

### <a name="publish-the-sample-copy"></a>发布示例副本

现已在环境中创建蓝图示例的副本。 该副本在创建后处于“草稿”模式，必须先将其 **发布**，然后才能分配和部署它。 可根据环境和需求自定义蓝图示例的副本，但这种修改可能导致该副本不符合 CMMC 级别 3 控制要求。

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 在左侧选择“蓝图定义”页。 使用筛选器找到蓝图示例的副本，然后选择它。

1. 选择页面顶部的“发布蓝图”。 在右侧的新窗格中，提供蓝图示例副本的 **版本**。 以后做出修改时，此属性非常有用。 提供“更改注释”，例如，“基于 CMMC 级别 3 蓝图示例发布的第一个版本”。 然后选择页面底部的“发布”。

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
|CMMC 级别 3|策略分配|在评估来宾配置策略时，包括已连接 Arc 的服务器|选择“true”即表示你同意按月支付每台已连接 Arc 的虚拟机的费用；有关详细信息，请访问 https://aka.ms/policy-pricing|
|CMMC 级别 3|策略分配|必须从 Windows VM“管理员”组中排除的用户的列表|以分号分隔的应从管理员本地组中排除的用户列表；示例：管理员；myUser1；myUser2|
|CMMC 级别 3|策略分配|必须包含在 Windows VM 管理员组中的用户的列表|以分号分隔的应包括在管理员本地组中的用户列表；示例：管理员；myUser1；myUser2|
|CMMC 级别 3|策略分配|用于 VM 代理报告的 Log Analytics 工作区 ID|VM 代理应在其中报告的 Log Analytics 工作区的 ID (GUID)|
|CMMC 级别 3|策略分配|允许的椭圆曲线名称|椭圆曲线加密证书允许的曲线名称列表。|
|CMMC 级别 3|策略分配|允许的密钥类型|允许的密钥类型的列表|
|CMMC 级别 3|策略分配|允许 Kubernetes 群集 Pod 使用主机网络|如果允许 Pod 使用主机网络，则将此值设置为 true；否则，设置为 false。|
|CMMC 级别 3|策略分配|审核身份验证策略更改|指定在对身份验证策略进行更改时是否生成审核事件。 此设置可用于跟踪域级和林级信任更改以及授予给用户帐户或组的权限的更改。|
|CMMC 级别 3|策略分配|审核授权策略更改|指定是否在分配和删除用户权限策略中的用户权限、更改安全令牌对象权限、更改资源属性以及对文件系统对象更改中心访问策略时生成审核事件。|
|CMMC 级别 3|策略分配|策略效果: 应为虚拟机启用 Azure 备份|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 认知服务帐户应限制网络访问|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: SQL 托管实例应使用客户管理的密钥对数据进行静态加密|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: Azure API for FHIR 应使用客户管理的密钥 (CMK) 对数据进行静态加密|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果：应为 Azure Front Door 服务启用 Web 应用程序防火墙 (WAF)|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果：SQL Server 的“漏洞评估”设置应包含用来接收扫描报告的电子邮件地址|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应为认知服务帐户禁用公用网络访问|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: CORS 不得允许所有资源都能访问函数应用|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果：应对面向 Internet 的虚拟机应用自适应网络强化建议|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应该为你的订阅分配了多个所有者|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应在虚拟机上启用磁盘加密|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应启用向订阅所有者发送高严重性警报的电子邮件通知|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 密钥保管库应启用清除保护|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: SQL Server 应使用客户管理的密钥对数据进行静态加密|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应当为函数应用禁用远程调试|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应启用用于密钥保管库的 Azure Defender|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应为 Azure Database for MariaDB 启用异地冗余备份|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: CORS 不得允许每个域都能访问 API for FHIR|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: Windows 虚拟机应符合“安全选项 - 网络安全”要求|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应更新自适应应用程序控制策略中的允许列表规则|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果：Web 应用程序防火墙 (WAF) 应对应用程序网关使用指定模式|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应设置密钥的到期日期|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应在 SQL 数据库上启用透明数据加密|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: Azure Monitor 日志配置文件应为类别 "write"、"delete" 和 "action" 收集日志|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果：应在 SQL 托管实例上启用漏洞评估|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：确保用作 API 应用一部分的“PHP 版本”是最新的|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 密钥保管库应启用软删除|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应该为 SQL 服务器预配 Azure Active Directory 管理员|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果：应只启用与 Azure Cache for Redis 的安全连接|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应为 Azure Database for PostgreSQL 服务器启用基础结构加密|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应在虚拟机规模集上安装 Endpoint Protection 解决方案|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应启用用于应用服务的 Azure Defender|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: Windows 虚拟机应符合“系统审核策略 - 策略更改”要求|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 认知服务帐户应启用数据加密|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应阻止来自 Internet 的 SSH 访问|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应加密未附加的磁盘|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应启用用于存储的 Azure Defender|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 存储帐户应限制网络访问|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: CORS 不得允许所有资源都能访问 API 应用|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 对存储帐户部署高级威胁防护|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应对自动化帐户变量进行加密|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应启用 IoT 中心内的诊断日志|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应为 Azure Database for MySQL 服务器启用基础结构加密|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 对于特定的“安全”操作，应有活动日志警报(Microsoft.Security/securitySolutions/delete)|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应该修复虚拟机规模集上安全配置中的漏洞|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: Windows 虚拟机应符合“安全选项 - 网络访问”要求|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应该启用安全传输到存储帐户|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: Azure Monitor 应从所有区域收集活动日志|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果：Web 应用程序防火墙 (WAF) 应对 Azure Front Door 服务使用指定模式|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 存储帐户应有基础结构加密|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果：应在计算机中启用用于定义安全应用程序的自适应应用程序控制|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应为 Azure Database for PostgreSQL 启用异地冗余备份|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: Windows 虚拟机应符合“安全选项 - 用户帐户控制”要求|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：确保用作 Web 应用一部分的“Java 版本”是最新的|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应启用用于服务器的 Azure Defender|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：只多只为订阅指定 3 个所有者|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 订阅应有用于解决安全问题的联系人电子邮件地址|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应禁止存储帐户公共访问|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果：应在虚拟机上启用漏洞评估解决方案|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应启用 Azure Defender for Kubernetes|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应在密钥保管库上启用防火墙|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果：应为应用程序网关启用 Web 应用程序防火墙 (WAF)|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：CORS 不应允许所有资源访问 Web 应用程序|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 审核允许重用以前的 24 个密码的 Windows 虚拟机|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应使用客户管理的密钥 (CMK) 对容器注册表进行加密|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应从订阅中删除具有写入权限的外部帐户|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应为 PostgreSQL 灵活服务器禁用公用网络访问|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应修正 Azure 容器注册表映像中的漏洞|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应从订阅中删除拥有读取权限的外部帐户|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: Service Fabric 群集应将 ClusterProtectionLevel 属性设置为 EncryptAndSign|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应在虚拟机上启用用于 SQL 服务器的 Azure Defender|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 认知服务帐户应启用使用客户管理的密钥进行数据加密|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应从订阅中删除弃用的帐户|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应该只能通过 HTTPS 访问函数应用|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果:应启用发送高严重性警报的电子邮件通知|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 存储帐户应使用客户管理的密钥进行加密|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：确保用作 Web 应用一部分的“Python 版本”是最新的|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：确保用作函数应用一部分的“Python 版本”是最新的|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：确保用作 WEB 应用一部分的“PHP 版本”是最新的|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：确保用作 API 应用一部分的“Python 版本”是最新的|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 密钥应是指定的加密类型 RSA 或 EC|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果：Azure 订阅应有用于活动日志的日志配置文件|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: Azure Kubernetes 服务群集中的操作系统和数据磁盘都应使用客户管理的密钥进行加密|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应启用用于 Azure SQL 数据库服务器的 Azure Defender|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应使用客户管理的密钥进行 Azure 数据资源管理器静态加密|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 使用 RSA 加密的密钥应有指定的最小密钥大小|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应为 Azure Database for MySQL 启用异地冗余备份|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: Kubernetes 群集 Pod 应只使用已批准的主机网络和端口范围|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应在计算机上安装系统更新|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: Windows 虚拟机应符合“系统审核策略 - 特权使用”要求|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: Azure 流分析作业应使用客户管理的密钥来加密数据|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：确保用作 API 应用一部分的“Java 版本”是最新的|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：确保用于运行 Web 应用的“HTTP 版本”是最新的|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应在 API 应用中使用最新的 TLS 版本|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应对订阅中拥有写入权限的帐户启用 MFA|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果: 确保用于运行 API 应用的“HTTP 版本”是最新的|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应在 Windows 服务器上部署 Microsoft IaaSAntimalware 扩展|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：确保用作函数应用一部分的“Java 版本”是最新的|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应限制在与虚拟机关联的网络安全组上使用所有网络端口|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应选择安全中心标准定价层|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 审核没有将最短密码长度限制为 14 个字符的 Windows 虚拟机|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：审核自定义 RBAC 规则的使用|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：只能通过 HTTPS 访问 Web 应用程序|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应启用 SQL 服务器上的审核|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应在虚拟机上安装 Log Analytics 代理|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应在对订阅拥有所有者权限的帐户上启用 MFA|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应在 SQL 服务器上启用高级数据安全性|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果：应在 SQL 托管实例上启用高级数据安全|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应在 Kubernetes 服务上使用基于角色的访问控制(RBAC)|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 虚拟机应有来宾配置扩展|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：监视 Azure 安全中心 Endpoint Protection 的缺失情况|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果：活动日志至少应保留一年|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果：应通过即时网络访问控制来保护虚拟机的管理端口|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应为 PostgreSQL 服务器禁用公用网络访问|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 为 Cosmos DB 帐户部署高级威胁防护|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应启用应用服务中的诊断日志|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：只能通过 HTTPS 访问 API 应用|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 对于特定的“管理”操作 (Microsoft.ClassicNetwork/networkSecurityGroups/delete)，应有活动日志警报|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 对于特定的“管理”操作 (Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete)，应有活动日志警报|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 对于特定的“管理”操作，应有活动日志警报(Microsoft.Network/networkSecurityGroups/delete)|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 对于特定的“管理”操作，应有活动日志警报(Microsoft.Network/networkSecurityGroups/securityRules/delete)|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 对于特定的“管理”操作，应有活动日志警报(Microsoft.Sql/servers/firewallRules/delete)|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应使用网络安全组来保护非面向 Internet 的虚拟机|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 审核未启用密码复杂性设置的 Windows 虚拟机|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应启用用于容器注册表的 Azure Defender|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: Azure Data Box 作业应对设备上的静态数据启用双重加密|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应在虚拟机规模集上安装系统更新|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: Microsoft Antimalware for Azure 应配置为自动更新保护签名|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 对于特定的“策略”操作，应有活动日志警报(Microsoft.Authorization/policyAssignments/delete)|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应为 MySQL 灵活服务器禁用公用网络访问|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 存储帐户应允许来自受信任的 Microsoft 服务的访问|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应当为 Web 应用程序禁用远程调试|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 使用 RSA 加密的证书应有指定的最小密钥大小|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 容器注册表不得允许不受限制的网络访问|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应为 PostgreSQL 数据库服务器启用“强制执行 SSL 连接”|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应使用系统分配的托管标识将来宾配置扩展部署到 Azure 虚拟机|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应为 Azure SQL 数据库启用长期异地冗余备份|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应为 MySQL 服务器禁用公用网络访问|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 审核没有使用可逆加密来存储密码的 Windows 虚拟机|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: Windows 虚拟机应符合“用户权限分配”要求|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应该修复计算机上安全配置中的漏洞|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：确保用于运行函数应用的“HTTP 版本”是最新的|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应在对订阅拥有读取权限的帐户上启用 MFA|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应阻止来自 Internet 的 RDP 访问|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 审核没有将 passwd 文件权限设为 0644 的 Linux 虚拟机|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 子网应与网络安全组关联|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应为 MySQL 数据库服务器启用“强制执行 SSL 连接”|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应该修复容器安全配置中的漏洞|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应当为 API 应用禁用远程调试|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果：审核允许在不使用密码的情况下从帐户进行远程连接的 Linux 计算机|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应从订阅中删除拥有所有者权限的已弃用帐户|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应对 Azure 数据资源管理器启用双重加密|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应对 SQL Server 启用漏洞评估|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应在虚拟机规模集上安装 Log Analytics 代理|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应在 Web 应用中使用最新的 TLS 版本|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应对 Azure 数据资源管理器启用磁盘加密|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果：面向 Internet 的虚拟机应使用网络安全组进行保护|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果：审核具有不使用密码的帐户的 Linux 计算机|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: Azure Synapse 工作区应使用客户管理的密钥加密静态数据|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应从订阅中删除拥有所有者权限的外部帐户|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应在函数应用中使用最新的 TLS 版本|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: Kubernetes 服务应升级到不易受攻击的 Kubernetes 版本|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 所有 Internet 流量都应通过你部署的 Azure 防火墙进行路由|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: Linux 虚拟机应符合 Azure 安全基线的要求|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 应为 MariaDB 服务器禁用公用网络访问|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略的效果：应该修复 SQL 数据库中的漏洞|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|策略效果: 使用椭圆曲线加密的密钥应有指定的曲线名称|有关效果的详细信息，请访问 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)|
|CMMC 级别 3|策略分配|从策略评估中排除的命名空间: Kubernetes 群集 Pod 应只使用已批准的主机网络和端口范围|要从策略评估中排除的 Kubernetes 命名空间的列表。|
|CMMC 级别 3|策略分配|适用于应用服务的最新 Java 版本|应用服务支持的最新 Java 版本|
|CMMC 级别 3|策略分配|适用于应用服务的最新 Linux Python 版本|应用服务支持的最新 Python 版本|
|CMMC 级别 3|策略分配|可选: 在审核 Log Analytics 代理部署时，需要添加到范围的安装了受支持 Linux OS 的 VM 映像的列表|示例值： `/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage`|
|CMMC 级别 3|策略分配|可选: 在审核 Log Analytics 代理部署时，需要添加到范围的安装了受支持 Windows OS 的 VM 映像的列表|示例值： `/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage`|
|CMMC 级别 3|策略分配|应启用网络观察程序的区域的列表|审核是否没有为区域启用网络观察程序。|
|CMMC 级别 3|策略分配|应启用诊断日志的资源类型列表||
|CMMC 级别 3|策略分配|Pod 可以在主机网络命名空间中使用的允许主机端口范围内的最大值。|Pod 可以在主机网络命名空间中使用的允许主机端口范围内的最大值。|
|CMMC 级别 3|策略分配|密钥的最小 RSA 密钥大小|RSA 密钥的最小密钥大小。|
|CMMC 级别 3|策略分配|证书的最小 RSA 密钥大小|RSA 证书的最小密钥大小。|
|CMMC 级别 3|策略分配|Windows Web 服务器的最低 TLS 版本|使用较低 TLS 版本的 Windows Web 服务器将被评估为不符合|
|CMMC 级别 3|策略分配|Pod 可以在主机网络命名空间中使用的允许主机端口范围内的最小值。|Pod 可以在主机网络命名空间中使用的允许主机端口范围内的最小值。|
|CMMC 级别 3|策略分配|模式要求|所有 WAF 策略需要的模式|
|CMMC 级别 3|策略分配|模式要求|所有 WAF 策略需要的模式|
|CMMC 级别 3|策略分配|允许 Pod hostPath 卷使用的主机路径|允许 Pod hostPath 卷使用的主机路径。 提供空的路径列表来阻止所有主机路径。|
|CMMC 级别 3|策略分配|网络访问：可远程访问的注册表路径|指定可通过网络访问的注册表路径，而不管 `winreg` 注册表项的访问控制列表 (ACL) 中列出的用户或组。|
|CMMC 级别 3|策略分配|网络访问：可远程访问的注册表路径和子路径|指定可通过网络访问的注册表路径和子路径，而不管 `winreg` 注册表项的访问控制列表 (ACL) 中列出的用户或组。|
|CMMC 级别 3|策略分配|网络访问：可匿名访问的共享|指定匿名用户可以访问的网络共享。 此策略设置的默认配置影响不大，因为必须先对所有用户进行身份验证，然后才能访问服务器上的共享资源。|
|CMMC 级别 3|策略分配|网络安全: 配置允许 Kerberos 使用的加密类型|指定允许 Kerberos 使用的加密类型。|
|CMMC 级别 3|策略分配|网络安全：LAN 管理器身份验证级别|指定哪个质询/响应身份验证协议将用于网络登录。 此选择将影响客户端使用的身份验证协议级别、协商的会话安全级别以及服务器接受的身份验证级别。|
|CMMC 级别 3|策略分配|网络安全：LDAP 客户端签名要求|指定代表发出 LDAP BIND 请求的客户端请求获取的数据签名的级别。|
|CMMC 级别 3|策略分配|网络安全：基于 NTLM SSP（包括安全 RPC）客户端的最小会话安全|指定对于使用 NTLM 安全支持提供程序 (SSP) 的应用程序，客户端允许哪些行为。 SSP 接口 (SSPI) 由需要身份验证服务的应用程序使用。 有关详细信息，请参阅 [https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers](/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers)。|
|CMMC 级别 3|策略分配|网络安全：基于 NTLM SSP（包括安全 RPC）服务器的最小会话安全|指定对于使用 NTLM 安全支持提供程序 (SSP) 的应用程序，服务器允许哪些行为。 SSP 接口 (SSPI) 由需要身份验证服务的应用程序使用。|
|CMMC 级别 3|策略分配|适用于应用服务的最新 PHP 版本|应用服务支持的最新 PHP 版本|
|CMMC 级别 3|策略分配|所需的 IoT 中心诊断日志保持期(天)||
|CMMC 级别 3|策略分配|网络观察程序的资源组的名称|NetworkWatcher 资源组名称（如 NetworkWatcherRG）。 这是网络观察程序所在的资源组。|
|CMMC 级别 3|策略分配|SQL 服务器所需的审核设置||
|CMMC 级别 3|策略分配|支持基于软件的双重加密的 Azure Data Box SKU|支持基于软件的双重加密的 Azure Data Box SKU 的列表|
|CMMC 级别 3|策略分配|UAC: 内置管理员帐户的管理员批准模式|指定内置管理员帐户的管理员批准模式行为。|
|CMMC 级别 3|策略分配|UAC: 管理员在管理员批准模式下的提升权限提示行为|指定管理员的提升权限提示行为。|
|CMMC 级别 3|策略分配|UAC: 检测应用程序安装并提示权限提升|指定计算机的应用程序安装检测行为。|
|CMMC 级别 3|策略分配|UAC: 在管理员批准模式下运行所有管理员|指定计算机的所有“用户帐户控制(UAC)”策略设置的行为。|
|CMMC 级别 3|策略分配|可以从远程系统强制关闭计算机的用户和组|指定允许哪些用户和组从网络上的远程位置关闭计算机。|
|CMMC 级别 3|策略分配|被拒绝从网络访问此计算机的用户和组|指定明确禁止哪些用户或组通过网络连接到计算机。|
|CMMC 级别 3|策略分配|被拒绝本地登录的用户和组|指定明确禁止哪些用户和组登录计算机。|
|CMMC 级别 3|策略分配|被拒绝以批处理作业身份登录的用户和组|指定明确禁止哪些用户和组以批处理作业(即计划内任务)身份登录计算机。|
|CMMC 级别 3|策略分配|被拒绝以服务身份登录的用户和组|指定明确禁止哪些服务帐户以服务身份注册进程。|
|CMMC 级别 3|策略分配|被拒绝通过远程桌面服务登录的用户和组|指定明确禁止哪些用户和组通过终端服务/远程桌面客户端登录计算机。|
|CMMC 级别 3|策略分配|可以还原文件和目录的用户和组|指定在还原备份的文件和目录时，允许哪些用户和组绕过文件、目录、注册表和其他永久性对象权限。|
|CMMC 级别 3|策略分配|可以关闭系统的用户和组|指定允许哪些本地登录到环境中计算机的用户和组使用 Shut Down 命令关闭操作系统。|
|CMMC 级别 3|策略分配|可以在本地登录的用户或组|指定允许网络上的哪些远程用户连接到计算机。 这不包括远程桌面连接。|
|CMMC 级别 3|策略分配|可以备份文件和目录的用户或组|指定允许哪些用户和组绕过文件和目录权限来备份系统。|
|CMMC 级别 3|策略分配|可以更改系统时间的用户或组|指定允许哪些用户和组更改计算机内部时钟的时间和日期。|
|CMMC 级别 3|策略分配|可以更改时区的用户或组|指定允许哪些用户和组更改计算机的时区。|
|CMMC 级别 3|策略分配|可以创建令牌对象的用户或组|指定允许哪些用户和组创建访问令牌，此令牌可以提供提升的权限来访问敏感数据。|
|CMMC 级别 3|策略分配|可以在本地登录的用户或组|指定哪些用户或组能够以交互方式登录到计算机。 尝试通过远程桌面连接或 IIS 登录的用户也需要此用户权限。|
|CMMC 级别 3|策略分配|Remote Desktop Users|可以通过远程桌面服务登录的用户或组|
|CMMC 级别 3|策略分配|可以管理审核和安全日志的用户或组|指定允许哪些用户和组更改文件和目录的审核选项并清除安全日志。|
|CMMC 级别 3|策略分配|可以获得文件或其他对象的所有权的用户或组|指定允许哪些用户和组获得文件、文件夹、注册表项、进程或线程的所有权。 此用户权限将绕过为保护对象而实施的任何权限，以将所有权授予指定的用户。|

## <a name="next-steps"></a>后续步骤

有关蓝图及其使用方式的更多文章：

- 了解[蓝图生命周期](../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../how-to/update-existing-assignments.md)。
