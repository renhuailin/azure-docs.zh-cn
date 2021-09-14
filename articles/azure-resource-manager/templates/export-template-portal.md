---
title: 在 Azure 门户中导出模板
description: 使用 Azure 门户从订阅中的资源导出 Azure 资源管理器模板。
ms.topic: conceptual
ms.date: 09/01/2021
ms.openlocfilehash: c6987f95f2ccb953977244d8ff70b2f925f35f2e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436193"
---
# <a name="use-azure-portal-to-export-a-template"></a>使用 Azure 门户导出模板

[!INCLUDE [Export template intro](../../../includes/resource-manager-export-template-intro.md)]

本文介绍如何通过门户导出模板。 有关其他选项，请参阅：

* [使用 Azure CLI 导出模板](export-template-cli.md)
* [使用 Azure PowerShell 导出模板](export-template-powershell.md)
* [从资源组导出 REST API](/rest/api/resources/resourcegroups/exporttemplate) 和[从部署历史记录导出 REST API](/rest/api/resources/deployments/export-template)。

[!INCLUDE [Export template choose option](../../../includes/resource-manager-export-template-choose-option.md)]

[!INCLUDE [Export template limitations](../../../includes/resource-manager-export-template-limitations.md)]

## <a name="export-template-from-a-resource-group"></a>从资源组导出模板

若要从资源组中导出一个或多个资源：

1. 选择包含所要导出的资源的资源组。

1. 通过选中相应的复选框选择一个或多个资源。  若要全选，请选中 **名称** 左侧的复选框。 “导出模板”菜单项只有在你选择了至少一个资源之后才会启用。

   ![导出所有资源](./media/export-template-portal/select-all-resources.png)

    在屏幕截图中，只选择了存储帐户。
1. 选择“导出模板”。

1. 此时将显示导出的模板，并且该模板可供下载和部署。

   ![显示模板](./media/export-template-portal/show-template.png)

   “包括参数”默认情况下已选中。  如果选中，生成模板时将包括所有模板参数。 如果希望创作自己的参数，请将此复选框切换为不包括参数。

## <a name="export-template-from-a-resource"></a>从资源导出模板

导出一个资源：

1. 选择包含所要导出的资源的资源组。

1. 选择要导出的资源以打开资源。

1. 在左窗格中选择该资源对应的“导出模板”。

   ![导出资源](./media/export-template-portal/export-single-resource.png)

1. 此时将显示导出的模板，并且该模板可供下载和部署。 模板只包含单个资源。 “包括参数”默认情况下已选中。  如果选中，生成模板时将包括所有模板参数。 如果希望创作自己的参数，请将此复选框切换为不包括参数。

## <a name="download-template-before-deployment"></a>在部署之前下载模板

通过门户，可在部署之前下载模板。 无法通过 PowerShell 或 Azure CLI 进行下载。

1. 选择要部署的 Azure 服务。

1. 填写新服务的值。

1. 在通过验证之后、开始部署之前，请选择“下载自动化模板”。

   ![下载模板](./media/export-template-portal/download-before-deployment.png)

1. 此时将显示该模板，并且该模板可供下载和部署。

## <a name="export-template-after-deployment"></a>在部署之后导出模板

可以导出用于部署现有资源的模板。 获取的模板正是用于部署的模板。

1. 选择要导出的资源组。

1. 选择“部署”下的链接。

   ![选择部署历史记录](./media/export-template-portal/select-deployment-history.png)

1. 从部署历史记录中选择一个部署。

   ![选择部署](./media/export-template-portal/select-details.png)

1. 选择“模板”。 随后，用于此部署的模板将会显示，并可供下载。

   ![选择模板](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>后续步骤

- 了解如何使用 [Azure CLI](export-template-cli.md)、[Azure PowerShell](export-template-powershell.md) 或 [REST API](/rest/api/resources/resourcegroups/exporttemplate) 导出模板。
- 若要了解资源管理器模板语法，请参阅[了解 Azure 资源管理器模板的结构和语法](./syntax.md)。
- 若要了解如何开发模板，请参阅[分步教程](../index.yml)。