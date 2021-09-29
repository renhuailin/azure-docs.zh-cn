---
title: 使用 Azure Functions 将 Azure Sentinel 连接到数据源 | Microsoft Docs
description: 了解如何配置数据连接器，连接器使用 Azure Functions 将数据从数据源获取到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/07/2021
ms.author: yelevin
ms.openlocfilehash: b6e38d6ce5944d09a089404ae1fe2ca81a951c18
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124781286"
---
# <a name="use-azure-functions-to-connect-azure-sentinel-to-your-data-source"></a>使用 Azure Functions 将 Azure Sentinel 连接到数据源

可以将 [Azure Functions](../azure-functions/functions-overview.md) 与各种编码语言（如 [PowerShell](../azure-functions/functions-reference-powershell.md) 或 Python）结合使用，以创建到兼容数据源 REST API 终结点的无服务器连接器。 然后，Azure Function Apps 允许将 Azure Sentinel 连接到数据源的 REST API 来拉取日志。

本文介绍如何使用 Azure Function Apps 来配置 Azure Sentinel。 可能还需要配置源系统，可以在门户中每个数据连接器页或 [Azure Sentinel 数据连接器参考](data-connectors-reference.md)页中的服务部分查找供应商和产品特定信息链接。




> [!NOTE]
> - 将数据引入 Azure Sentinel 后，数据存储在运行 Azure Sentinel 的工作区的地理位置。
>
>     对于长期保留，可能还希望将数据存储在 Azure 数据资源管理器中。 有关详细信息，请参阅[集成 Azure 数据资源管理器](store-logs-in-azure-data-explorer.md)。
>
> - 使用 Azure Functions 将数据引入 Azure Sentinel 可能会导致额外的数据引入成本。 有关详细信息，请参阅 [Azure Functions 定价](https://azure.microsoft.com/pricing/details/functions/)页。

## <a name="prerequisites"></a>先决条件

在使用 Azure Functions 连接到数据源 Azure Sentinel 以及在 Azure Sentinel 中拉取其日志之前，请确保具有以下权限和凭据：

- 你必须具有对 Azure Sentinel 工作区的读取和写入权限。

- 必须有工作区的共享密钥的读取权限。 [详细了解工作区密钥](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)。

- 必须具有 Azure Functions 的读取和写入权限，用于创建函数应用。 [详细了解 Azure Functions](../azure-functions/index.yml)。

- 还需要凭据才能访问产品的 API - 用户名和密码、令牌、密钥或其他组合。 可能还需要其他 API 信息，例如终结点 URI。

    有关详细信息，请参阅要连接到的服务的文档以及 [Azure Sentinel 数据连接器参考](data-connectors-reference.md)页中的服务部分。

## <a name="configure-and-connect-your-data-source"></a>配置和连接数据源

> [!NOTE]
> - 可以将工作区和 API 授权密钥或令牌安全地存储在 Azure Key Vault 中。 Azure Key Vault 提供了一种存储和检索键值的安全机制。 [按照这些说明](../app-service/app-service-key-vault-references.md)，将 Azure Key Vault 与 Azure 函数应用结合使用。
>
> - 某些数据连接器依赖于基于 [Kusto 函数](/azure/data-explorer/kusto/query/functions/user-defined-functions)的分析程序才能按预期方式工作。 有关用于创建 Kusto 函数和别名的说明，请参阅 [Azure Sentinel 数据连接器参考](data-connectors-reference.md)页中的服务部分。


### <a name="step-1---get-your-source-systems-api-credentials"></a>步骤 1 - 获取源系统的 API 凭据

按照源系统的说明获取其“API 凭据/授权密钥/令牌”。 将其复制并粘贴到文本文件中，供稍后使用。

可以在门户的数据连接器页和 [Azure Sentinel 数据连接器参考](data-connectors-reference.md)页的服务部分找到所需的确切凭据的详细信息，以及指向产品查找或创建说明的链接。

可能还需在源系统上配置日志记录或其他设置。 你将找到相关的以及上一段落中的说明。
### <a name="step-2---deploy-the-connector-and-the-associated-azure-function-app"></a>步骤 2 - 部署连接器和关联的 Azure 函数应用

#### <a name="choose-a-deployment-option"></a>选择部署选项

# <a name="azure-resource-manager-arm-template"></a>[Azure 资源管理器 (ARM) 模板](#tab/ARM)

此方法使用 ARM 模板自动部署基于 Azure 函数的连接器。

1. 在 Azure Sentinel 门户中，单击“数据连接器”。 从列表中选择基于 Azure Functions 的连接器，然后选择“打开连接器页面”。

1. 在“配置”下，复制 Azure Sentinel“工作区 ID”和“主密钥”，并粘贴到一旁。

1. 选择“部署到 Azure”。 （可能需要向下滚动才能看到此按钮。）

1. 将显示“自定义部署”屏幕。
    - 选择“订阅”、“资源组”以及“区域”来部署函数应用  。

    - 输入上述[步骤 1](#step-1---get-your-source-systems-api-credentials) 中保存的 API 凭据/授权密钥/令牌。

    - 输入复制并放在一旁的 Azure Sentinel“工作区 ID”和“工作区密钥”（主密钥）。

        > [!NOTE]
        > 如果针对以上任何值使用 Azure Key Vault 机密，请使用 `@Microsoft.KeyVault(SecretUri={Security Identifier})` 架构来表示字符串值。 有关更多详细信息，请参阅 Key Vault 参考文档。

    - 在“自定义部署”屏幕上填写窗体中的其他任何字段。 在门户中查看数据连接器页，或在 [Azure Sentinel 数据连接器参考](data-connectors-reference.md)页中查看服务部分。

    - 选择“查看 + 创建”  。 验证完成后，选择“创建”。 

# <a name="manual-deployment-with-powershell"></a>[使用 PowerShell 进行手动部署](#tab/MPS)

使用以下分步说明手动部署使用 PowerShell 函数且基于 Azure Functions 的连接器。

1. 在 Azure Sentinel 门户中，单击“数据连接器”。 从列表中选择基于 Azure Functions 的连接器，然后选择“打开连接器页面”。

1. 在“配置”下，复制 Azure Sentinel“工作区 ID”和“主密钥”，并粘贴到一旁。

1. 创建函数应用
    1. 从 Azure 门户，搜索并选择“函数应用”。

    1. 在“函数应用”页面，选择“创建” 。 将打开“创建函数应用”向导。

    1. 在“基本信息”选项卡中：
        - 选择一个“订阅”和“资源组” 。
        - 为函数应用命名。
        - 设置 PowerShell Core 的“运行时堆栈”。
        - 选择适当的版本号。
        - 选择要部署的“区域”，然后选择“下一步: 托管” 。

    1. 在“托管”选项卡中：
        - 选择要使用的存储帐户，或新建一个。
        - 选择“消耗(无服务器)”作为“计划类型”。

    1. 进行所需的任何其他配置更改，然后选择“查看 + 创建”。

    1. 等待“验证通过”的消息，然后选择“创建”。

    1. 部署完成后，选择“转到资源”。

1. 导入函数应用代码
    1. 在新建的函数应用中，从导航菜单中选择“函数”。

    1. 在“函数”页面，选择“+ 添加” 。
    
    1. 在“添加函数”面板，选择“计时器”触发 。

    1. 为函数输入一个唯一名称，并输入 cron 表达式以指定计划。 默认时间间隔为每 5 分钟。

    1. 创建函数后，在左窗格中选择“代码 + 测试”。

    1. 下载源系统供应商提供的函数应用代码，将其复制并粘贴到“函数应用”的“run.ps1”编辑器中，替换默认存在的代码。 可以在连接器页或位于 [Azure Sentinel 数据连接器参考](data-connectors-reference.md)页的服务部分找到下载链接。

    1. 选择“保存”  。

1. 配置函数应用
    1. 在函数应用页面，在导航菜单的“设置”下选择“配置” 。

    1. 在“应用程序设置”选项卡中，选择“+ 新建应用程序设置” 。

    1. 根据各自区分大小写的字符串值，将指定的应用程序设置单独添加到产品中。 查看数据连接器页或 [Azure Sentinel 数据连接器参考](data-connectors-reference.md)服务部分中的产品部分。

        > [!TIP]
        > 如果适用，请使用“logAnalyticsUri”应用程序设置来替代 Log Analytics API 终结点（如果使用的是专用云）。 因此，例如，如果使用的是公有云，将值留空；对于 Azure GovUS 云环境，指定以下格式的值：`https://<CustomerId>.ods.opinsights.azure.us`。
        >

# <a name="manual-deployment-with-python"></a>[使用 Python 进行手动部署](#tab/MPY)

使用以下分步说明手动部署使用 Python 函数且基于 Azure Functions 的连接器。 此类部署需要 Visual Studio Code。

1. 在 Azure Sentinel 门户中，单击“数据连接器”。 从列表中选择基于 Azure Functions 的连接器，然后选择“打开连接器页面”。

1. 在“配置”下，复制 Azure Sentinel“工作区 ID”和“主密钥”，并粘贴到一旁。

1. 部署函数应用

    > [!NOTE]
    > 需要为 Azure 函数开发[准备 Visual Studio Code](../azure-functions/create-first-function-vs-code-python.md) (VS Code)。

    1. 使用数据连接器页和 [Azure Sentinel 数据连接器参考](data-connectors-reference.md)页的服务部分提供的链接下载 Azure 函数应用文件。 将存档提取到本地开发计算机。

    1. 启动 VS Code。 从菜单栏中，选择“文件“>”打开文件夹...”。

    1. 在从存档提取的文件中选择顶层文件夹。

    1. 在 VS Code 活动栏（左侧）中，选择 Azure 图标。 然后，在“Azure: 函数”区域，选择“部署到函数应用”按钮 。 

        > [!NOTE]
        > 如果你尚未登录，请在活动栏中选择 Azure 图标。 然后，在“Azure: 函数”区域，选择“登录到 Azure” 。  
        > 如果已登录，请转到下一步骤。

    1. 根据提示提供以下信息：
        - **选择文件夹**：从工作区选择一个文件夹，或浏览一个包含你的函数应用的文件夹。
        - **选择订阅**：选择要使用的订阅。
        - 选择“在 Azure 中新建函数应用”。 （请勿选择“高级”选项。）
        - **为函数应用输入一个全局唯一名称**：为函数应用指定一个在 URL 路径中有效的名称。 将对你选择的名称进行验证，以确保它在 Azure Functions 中是唯一的。
        - **选择运行时**：选择“Python 3.8”。

    1. 将开始部署。 创建函数应用并应用了部署包之后，会显示一个通知。

    1. 返回到函数应用的配置页面。

1. 配置函数应用
    1. 在函数应用页面，在导航菜单的“设置”下选择“配置” 。

    1. 在“应用程序设置”选项卡中，选择“+ 新建应用程序设置” 。

    1. 根据各自区分大小写的字符串值，将指定的应用程序设置单独添加到产品中。 查看数据连接器页或 [Azure Sentinel 数据连接器参考](data-connectors-reference.md)页服务部分中的应用程序设置以添加。

        - 如果适用，请使用“logAnalyticsUri”应用程序设置来替代 Log Analytics API 终结点（如果使用的是专用云）。 因此，例如，如果使用的是公有云，将值留空；对于 Azure GovUS 云环境，指定以下格式的值：`https://<CustomerId>.ods.opinsights.azure.us`。

---

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据会显示在“CustomLogs”下的日志中，在 [Azure Sentinel 数据连接器参考](data-connectors-reference.md)页的服务部分列出的表中。

若要查询数据，请在查询窗口中输入其中一个表名称或相关的 Kusto 函数别名。

请参阅连接器页中的“后续步骤”选项卡，了解一些有用的示例查询。

## <a name="validate-connectivity"></a>验证连接

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。 

## <a name="next-steps"></a>后续步骤

在本文档，你已了解如何使用基于 Azure Functions 的连接器将 Azure Sentinel 连接到数据源。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](./get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](./detect-threats-built-in.md)。
- [使用工作簿](./monitor-your-data.md)监视数据。