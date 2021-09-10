---
title: 使用资源管理器模板创建 Azure 自动化帐户
titleSuffix: Azure Automation
description: 本文介绍如何使用 Azure 资源管理器模板创建自动化帐户。
services: automation
ms.author: magoedte
ms.date: 08/27/2021
ms.topic: conceptual
ms.workload: infrastructure-services
ms.custom:
- mvc
- subject-armqs
- mode-arm
ms.openlocfilehash: daf04e7070b1d05e077993ae9cdb38d38595944f
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123110767"
---
# <a name="create-an-azure-automation-account-using-a-resource-manager-template"></a>使用资源管理器模板创建 Azure 自动化帐户

Azure 自动化提供基于云的自动化和配置服务，用于支持 Azure 环境和非 Azure 环境之间的一致管理。 本文介绍如何部署用于创建自动化帐户的 Azure 资源管理器模板（ARM 模板）。 与其他部署方法相比，使用 ARM 模板需要的步骤更少。 JSON 模板可为参数指定默认值，这些参数可能用作环境中的标准配置。 可以将模板存储在 Azure 存储帐户中，以便在组织中共享访问。 有关使用模板的详细信息，请参阅[使用 ARM 模板和 Azure CLI 部署资源](../azure-resource-manager/templates/deploy-cli.md)。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

此示例模板执行以下步骤：

* 自动创建 Azure Monitor Log Analytics 工作区。
* 自动创建 Azure 自动化帐户。
* 将自动化帐户链接到 Log Analytics 工作区。
* 将示例自动化 runbook 添加到帐户。

> [!NOTE]
> 使用 ARM 模板时，不支持创建自动化运行方式帐户。 若要从门户或使用 PowerShell 手动创建运行方式帐户，请参阅[创建运行方式帐户](create-run-as-account.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

如果你不熟悉 Azure 自动化和 Azure Monitor，请务必了解配置详细信息。 当你尝试创建、配置和使用链接到新自动化帐户的 Log Analytics 工作区时，该了解有助于避免出错。

* 查看[其他详细信息](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace)以充分了解工作区配置选项，如访问控制模式、定价层、保留期和产能预留级别。

* 查看[工作区映射](how-to/region-mappings.md)，以内联方式或在参数文件中指定支持的区域。 只有某些区域支持链接订阅中的 Log Analytics 工作区和自动化帐户。

* 如果刚开始使用 Azure Monitor 日志，并且尚未部署工作区，则查看[工作区设计指南](../azure-monitor/logs/design-logs-deployment.md)。 此文档将有助于了解访问控制，以及帮助了解适用于组织的建议设计实现策略。

## <a name="review-the-template"></a>查看模板

本文中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/101-automation/)。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.automation/101-automation/azuredeploy.json":::

该模板中定义的 Azure 资源：

* [**Microsoft.OperationalInsights/workspaces**](/azure/templates/microsoft.operationalinsights/workspaces)：创建 Azure Log Analytics 工作区。
* [**Microsoft.Automation/automationAccounts**](/azure/templates/microsoft.automation/automationaccounts)：创建 Azure 自动化帐户。
* [**Microsoft.Automation/automationAccounts/runbooks**](/azure/templates/microsoft.automation/automationaccounts/runbooks)：创建 Azure 自动化帐户 Runbook。

## <a name="deploy-the-template"></a>部署模板

1. 选择下面的“部署到 Azure”按钮登录到 Azure，并打开 ARM 模板。

    [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.automation%2F101-automation%2Fazuredeploy.json)

1. 输入或选择下列值：

    |属性 |说明 |
    |---|---|
    |订阅 |从下拉列表中选择自己的 Azure 订阅。|
    |资源组 |从下拉列表中选择现有资源组，或选择“新建”  。|
    |区域 |此值将自动填充。|
    |工作区名称 |输入新 Log Analytics 工作区的名称。|
    |SKU | 默认为“按 GB”定价层，该层已在 2018 年 4 月的定价模型中发布。 如果要在订阅中创建或配置 Log Analytics 工作区，而该订阅已加入 2018 年 4 月的定价模型，则唯一有效的 Log Analytics 定价层为 `PerGB2018`。|
    |数据保留 |默认为 30 天。|
    |位置 |将使用用于资源组的位置自动填充此值。|
    |自动化帐户名称 | 输入新自动化帐户的名称。|
    |示例图形 Runbook 名称 | 原样保留。|
    |示例图形 Runbook 说明 | 原样保留。|
    |示例 PowerShell Runbook 名称 | 原样保留。|
    |示例 PowerShell Runbook 说明 | 原样保留。|
    |示例 Python2Runbook 名称 |原样保留。|
    |示例 Python2Runbook 说明 |原样保留。|
    |_artifacts 位置 |原样保留。<sup>*</sup> 项目位置的 URI。|
    |_artifacts 位置 SAS 令牌 | 留空。 访问 `_artifactsLocation` 所需的 sasToken。 使用随附的脚本部署模板时，将自动生成 `sasToken`。|

    <sup>*</sup> 尝试从门户中的 PowerShell、CLI 或模板功能运行 ARM 模板时，如果未正确设置 `_artifactsLocation` 参数，你将会收到类似以下的错误消息：
    
    `"message": "Deployment template validation failed: 'The template resource '_artifactsLocation' at line '96' and column '31' is not valid: The language expression property 'templateLink' doesn't exist, available properties are 'template, templateHash, parameters, mode, debugSetting, provisioningState'.. Please see https://aka.ms/arm-template-expressions for usage details.'."`
    
    若要防止此错误，从门户中的模板功能运行时，为 `_artifactsLocation` 参数指定以下值 - `https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`。
    
    从 PowerShell 运行时，请包含参数且其值为 `-TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`。
    
    从 Azure CLI 运行时，请包含参数且其值为 `--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`。
    
    有关 PowerShell/CLI 的参考，请参阅“使用模板”部分下的[创建 Azure 自动化帐户 (microsoft.com)](https://azure.microsoft.com/resources/templates/101-automation/)。

1. 选择“审阅 + 创建”，然后选择“创建” 。 部署可能需要几分钟才能完成。 完成后，输出类似于以下图像：

    ![部署完成后的示例结果](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>查看已部署的资源

1. 部署完成后，你会收到“部署成功”通知，通知中附有“转到资源”链接 。 “资源组”页面将列出你的新资源。 从列表中选择新的自动化帐户。

1. 从左侧的“过程自动化”下，选择“Runbook”。  “Runbook”页列出了使用自动化帐户创建的三个示例 Runbook。

    ![使用自动化帐户创建的教程 runbook](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

1. 从左侧的“相关资源”下，选择“链接的工作区” 。 “链接的工作区”页显示了先前指定的、已链接到自动化帐户的 Log Analytics 工作区。

    ![已关联到 Log Analytics 工作区的自动化帐户](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="next-steps"></a>后续步骤

为自动化帐户[配置诊断设置](automation-manage-send-joblogs-log-analytics.md)，以将 runbook 作业状态和作业流发送到链接的 Log Analytics 工作区。
