---
title: 将 Agari 仿冒防御和品牌防护解决方案连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Agari 仿冒防御和品牌防护连接器将其日志拉取到 Azure Sentinel 中。 在工作簿中查看 Alsid 数据，创建警报，并改进调查。
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: f356bce141ece3f92e6f482cfa4aff1a914fbb61
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253039"
---
# <a name="connect-your-agari-phishing-defense-and-brand-protection-solutions-to-azure-sentinel"></a>将 Agari 仿冒防御和品牌防护解决方案连接到 Azure Sentinel

> [!IMPORTANT]
> Agari 仿冒防御和品牌防护连接器目前处于“预览阶段”。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Agari 仿冒防御和品牌防护连接器可让你轻松地将品牌防护和仿冒防御解决方案的日志连接到 Azure Sentinel，使你可以查看工作簿中的数据、查询数据以创建自定义警报，并结合它来改进调查。 Agari 的解决方案使用 Azure Functions 和 REST API 与 Azure Sentinel 集成。

此外，品牌防护和仿冒响应客户可以通过安全图形 API 利用威胁情报共享。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

若要将 Agari 的仿冒防御和品牌防护解决方案连接到 Azure Sentinel，需要满足以下要求：

- Azure Sentinel 工作区的读取和写入权限。

- 对工作区的共享密钥的读取权限。 [详细了解工作区密钥](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)。

- Azure Functions 的读取和写入权限，用于创建函数应用。 [详细了解 Azure Functions](../azure-functions/index.yml)。

- 确保 Agari 的“客户端 ID”和“密钥”（在所有 Agari 解决方案中完全相同）。 有关说明，请参阅 [Agari 开发人员站点](https://developers.agari.com/agari-platform/docs/quick-start)。

## <a name="configure-and-connect-agari-solutions"></a>配置并连接 Agari 解决方案 

Agari 解决方案可以使用 Azure 函数应用将日志直接集成和导出到 Azure Sentinel。

> [!NOTE]
> 此连接器使用 Azure Functions 连接到 Agari 的解决方案，以将其日志拉取到 Azure Sentinel 中。 这可能会导致额外的数据引入成本。 有关详细信息，请参阅 [Azure Functions 定价](https://azure.microsoft.com/pricing/details/functions/)页。

1. **收集 Agari API 凭据：** 

    确保具有 Agari 的“客户端 ID”和“密钥”。 可在 [Agari 开发人员站点](https://developers.agari.com/agari-platform/docs/quick-start#generate-api-credentials)上找到相关说明。

1. **（可选）启用安全图形 API：** 

    Agari 函数应用允许通过安全图形 API 与 Azure Sentinel 共享威胁情报。 若要使用此功能，你需要启用 [Sentinel 威胁情报平台连接器](./connect-threat-intelligence-tip.md)，同时在 Azure Active Directory 中[注册应用程序](/graph/auth-register-app-v2)。

    此过程将提供以下三条信息，供你在部署函数应用时使用：“图形租户 ID”、“图形客户端 ID”和“图形客户端密码”。

1. **部署连接器和关联的 Azure 函数应用：** 

    1. 在 Azure Sentinel 门户中，单击“数据连接器”。 选择“Agari 仿冒防御和品牌防护(预览版)，然后单击“打开连接器页”。

    1. 在“配置”下，复制 Azure Sentinel“工作区 ID”和“主密钥”，并粘贴到一旁。

    1. 选择“部署到 Azure”。 （可能需要向下滚动才能看到此按钮。）

    1. 将显示“自定义部署”屏幕。

        - 输入你的 Agari“客户端 ID”和“客户端密码”（密钥）

        - 输入复制并放在一旁的 Azure Sentinel“工作区 ID”和“工作区密钥”（主密钥）。

        - 对于具有活动订阅的 Agari 解决方案，请选择“True”或“False”。

        - 如果已使用安全图形 API 创建了用于与 Azure Sentinel 共享 IoC 的 Azure 应用程序，请为“启用安全图形共享”选择“True”，然后输入“图形租户 ID”、“图形客户端 ID”和“图形客户端密码”。

    1. 选择“查看 + 创建”。 验证完成后，单击“创建”。

1. **为函数应用分配必要的权限：**

    Agari 连接器使用环境变量来存储日志访问时间戳。 为了使应用程序能够写入此变量，必须将权限分配给系统分配的标识。

    1. 在 Azure 门户中，导航到“函数应用”。

    1. 在“函数应用”边栏选项卡中，从列表中选择“函数应用”，然后在“函数应用”导航菜单中的“设置”下选择“标识”。

    1. 在“系统分配”选项卡中，将“状态”设置为“启用”  。 

    1. 选择“保存”，将出现“Azure 角色分配”按钮。 请单击此按钮。

    1. 在“Azure 角色分配”屏幕中，选择“添加角色分配”。 将“作用域”设置为“订阅”，从“订阅”下拉菜单中选择订阅，并将“角色”设置为“应用配置数据所有者”。 

    1. 选择“保存”。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在下列各表中的“自定义日志”下的“日志”中： 

- `agari_apdtc_log_CL`
- `agari_apdpolicy_log_CL`
- `agari_bpalerts_log_CL`

若要查询 Agari 解决方案数据，请在“查询”窗口中输入上述表名称之一。

请参阅连接器页中的“后续步骤”选项卡，了解一些有用的示例查询。

## <a name="validate-connectivity"></a>验证连接

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。 

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Agari 仿冒防御和品牌防护解决方案连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。