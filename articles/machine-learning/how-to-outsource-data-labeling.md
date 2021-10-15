---
title: 数据标记供应商公司
titleSuffix: Azure Machine Learning
description: 使用数据标记供应商公司来帮助标记数据标记项目中的数据
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.reviewer: sgilley
author: kvijaykannan
ms.author: vkann
ms.date: 09/30/2021
ms.topic: how-to
ms.openlocfilehash: 2937be805d82abde1125c8c8b23162d7eedec015
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129429540"
---
# <a name="work-with-a-data-labeling-vendor-company"></a>与数据标记供应商公司合作

了解如何聘请数据标记供应商公司来帮助你标记数据。 可以在 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend)的产品/服务页中详细了解这些公司及其提供的标记服务。


## <a name="workflow-summary"></a>工作流摘要

在创建数据标记项目之前：

1. 选择标记服务提供商。  若要在 Azure 市场中查找提供商：
    1. 查看[这些标记供应商公司的产品/服务详细信息](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend)。
    1. 如果标记供应商公司符合你的要求，请选择 Azure 市场中的“联系我”选项。 Azure 市场会将你的咨询路由到标记供应商公司。 在选择最终的公司之前，可以联系多家标记供应商公司。

1. 与标记服务提供商联系并签订合同。

与标记供应商公司签订合同之后：

1. 在 [Azure 机器学习工作室](https://ml.azure.com)中创建标记项目。 有关创建项目的更多详细信息，请参阅如何创建[图像标记项目](how-to-create-image-labeling-projects.md)或[文本标记项目](how-to-create-text-labeling-projects.md)。
1. 你并不局限于使用 Azure 市场中的数据标记提供商。  但是，如果你确实使用 Azure 市场中的提供商：
    1. 请在劳动力步骤中选择“使用 Azure 市场中的标记供应商公司”。
    1. 在下拉列表中选择相应的数据标记公司。

    > [!NOTE]
    > 创建标记项目后，无法更改标记供应商公司的名称。

1. 对于任何提供商（无论是通过 Azure 市场还是其他渠道找到的），都需要使用 Azure 基于角色的访问 (RBAC) 来启用对标记供应商公司的访问（`labeler` 角色、`techlead` 角色）。 这些角色允许公司访问资源以批注数据。

## <a name="select-a-company"></a><a name="review"></a> 选择公司

Microsoft 已识别出一些标记服务提供商，其知识和经验可能能够满足你的需求。 你可以在 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend)内标记提供商的产品/服务页中了解这些提供商并选择其中的一家提供商，同时考虑你的项目需求和要求。

> [!IMPORTANT]
> 可以在 Azure 市场的产品/服务页中详细了解这些公司及其提供的标记服务。 你需要对选用通过 Azure 市场提供服务的标记公司的任何决策负责，并且需要独立评估标记公司及其经验、服务、人员配备、条款等是否符合你的项目要求。 可以使用 Azure 市场中的“联系我”选项来联系通过 Azure 市场提供服务的标记公司，在三个工作日内应该就可以收到该公司的回复。 你将直接与标记公司签约并向其付款。

Microsoft 会定期审查 Azure 市场中潜在的标记服务提供商列表，并可随时在列表中添加或删除提供商。  

* 如果删除了某家提供商，这不会影响任何现有项目，也不会影响公司对这些项目的访问。
* 如果你使用的提供商不再在 Azure 市场中列出，请不要在新项目中选择“使用 Azure 市场中的标记供应商公司”选项。
* 已删除的提供商将不再在 Azure 市场中提供产品/服务。
* 不再可以通过 Azure 市场联系已删除的提供商。

你可以聘请多家标记供应商公司来解决各种标记项目需求。 每个项目将链接到一家标记供应商公司。

下面是可以帮助你使用 Azure 机器学习数据标记服务标记数据的标记供应商公司。 查看[供应商公司的产品/服务](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend)。

* [iSoftStone](https://azuremarketplace.microsoft.com/marketplace/consulting-services/isoftstoneinc1614950352893.20210527) 

* [Quadrant Resource](https://azuremarketplace.microsoft.com/marketplace/consulting-services/quadrantresourcellc1587325810226.quadrant_resource_data_labeling)

## <a name="enter-into-a-contract"></a>签订合同 

选择了要与其合作的标记公司后，你需要直接与该公司签订合同，并规定聘用条款。 Microsoft 不是此协议的一方，也不参与协议条款的确定或协商。 根据此协议支付的款项将直接支付给标记公司。

如果你在标记项目中启用“ML 辅助”标记，则 Microsoft 将单独针对与此服务相关的已使用计算资源向你收费。 与你使用 Azure 机器学习相关的所有其他费用（例如 Azure 机器学习工作区中使用的数据的存储费）均受你与 Microsoft 之间签订的协议条款的约束。

## <a name="enable-access"></a>启用访问权限

为使标记供应商公司能够访问你的项目，需要在工作区级别通过 [Azure 基于角色的访问 (RBAC)](how-to-assign-roles.md#manage-workspace-access) 为该公司启用访问权限。  如果你打算为不同的标记项目使用多家标记供应商公司，我们建议为每家公司创建单独的工作区。

> [!IMPORTANT]
> 与聘请标记公司相关的所有事宜需由你（而不是 Microsoft）负责，包括但不限于与范围、质量、计划和定价相关的问题。

## <a name="next-steps"></a>后续步骤

* [创建图像标记项目和导出标签](how-to-create-image-labeling-projects.md)
* [创建文本标记项目和导出标签（预览版）](how-to-create-text-labeling-projects.md)