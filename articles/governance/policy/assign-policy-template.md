---
title: 带有模板的新策略分配
description: 本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）来创建策略分配以识别不合规的资源。
ms.date: 08/17/2021
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 52daeb50d1b8eea91b43c07e45682d87e0bc12cb
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323476"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-an-arm-template"></a>使用 ARM 模板创建策略分配以识别不合规的资源

若要了解 Azure 中的符合性，第一步是确定资源的状态。
本快速入门逐步讲解如何使用 Azure 资源管理器模板（ARM 模板）创建策略分配，以识别未使用托管磁盘的虚拟机。 此过程结束时，你可以成功识别哪些虚拟机未使用托管磁盘。 这些虚拟机不符合策略分配要求。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="部署用于将 Azure Policy 分配给 Azure 的 ARM 模板的按钮。" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.authorization%2Fazurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="review-the-template"></a>查看模板

在本快速入门中，我们将创建一个策略分配，并分配一个名为“审核不使用托管磁盘的 VM”的内置策略定义。 有关可用内置策略的部分列表，请参阅 [Azure Policy 示例](./samples/index.md)。

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/azurepolicy-assign-builtinpolicy-resourcegroup/)。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.authorization/azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json":::

该模板中定义了以下资源：

- [Microsoft.Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

## <a name="deploy-the-template"></a>部署模板

> [!NOTE]
> Azure Policy 服务是免费的。 有关详细信息，请参阅 [Azure Policy 概述](./overview.md)。

1. 选择下图登录到 Azure 门户并打开模板：

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="部署用于将 Azure Policy 分配给 Azure 的 ARM 模板的按钮。" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.authorization%2Fazurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

1. 选择或输入以下值：

   | 名称 | 值 |
   |------|-------|
   | 订阅 | 选择 Azure 订阅。 |
   | 资源组 | 选择“新建”，指定名称，然后选择“确定”。  在屏幕截图中，资源组名称为 _mypolicyquickstart\<Date in MMDD\>rg_。 |
   | 位置 | 选择区域。 例如“美国中部”。 |
   | 策略分配名称 | 指定策略分配名称。 如果需要，可以使用策略定义显示名称。 例如，“审核不使用托管磁盘的 VM”。 |
   | 资源组名称 | 指定要将策略分配到的资源组名称。 本快速入门使用默认值 **[resourceGroup().name]**。 **[resourceGroup()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** 是检索资源组的模板函数。 |
   | 策略定义 ID | 指定 **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**。 |
   | 我同意上述条款和条件 | （选择） |

1. 选择“购买”。

其他一些资源：

- 若要查找更多示例模板，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)。
- 若要查看模板参考，请转到 [Azure 模板参考](/azure/templates/microsoft.authorization/allversions)。
- 若要了解如何开发 ARM 模板，请参阅 [Azure 资源管理器文档](../../azure-resource-manager/management/overview.md)。
- 若要了解订阅级部署，请参阅[在订阅级别创建资源组和资源](../../azure-resource-manager/templates/deploy-to-subscription.md)。

## <a name="validate-the-deployment"></a>验证部署

选择页面左侧的“符合性”。 然后找到所创建的“审核未使用托管磁盘的 VM”策略分配。

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="“策略符合性”页上符合性详细信息的屏幕截图。" border="false":::

如果存在与此新分配不相符的任何现有资源，这些资源会在“不符合的资源”下显示。

有关详细信息，请参阅[合规工作原理](./how-to/get-compliance-data.md#how-compliance-works)。

## <a name="clean-up-resources"></a>清理资源

删除创建的分配，请执行以下步骤：

1. 选择“Azure Policy”页面左侧中的“符合性”（或“分配”）并找到你创建的“审核未使用托管磁盘的 VM”策略分配。

1. 右键单击“审核不使用托管磁盘的 VM”策略分配并选择“删除分配”。

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="使用上下文菜单从“符合性”页中删除分配的屏幕截图。" border="false":::

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们在某个范围分配了一个内置策略定义并评估了其合规性报告。 策略定义可验证范围内的所有资源都符合策略，并可标识不符合策略的资源。

要了解有关分配策略以验证新资源是否符合要求的详细信息，请继续以下教程：

> [!div class="nextstepaction"]
> [创建和管理策略](./tutorials/create-and-manage.md)
