---
title: 手动提升资源管理器模板
description: 了解如何通过 Azure 数据工厂中的持续集成和交付将资源管理器模板手动推广到多个环境。
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 367a0b7f231fcdd88a28237e83916995fd58062b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219249"
---
# <a name="manually-promote-a-resource-manager-template-to-each-environment"></a>手动将资源管理器模板推广到每个环境

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

使用以下步骤将资源管理器模板推广到每个环境，以便在 Azure 数据工厂中进行持续集成和交付。

## <a name="steps-to-manually-promote-a-template"></a>手动推广模板的步骤

1. 在数据工厂中转到“管理”中心，然后在“源代码管理”部分中选择“ARM 模板” 。 在“ARM 模板”部分中选择“导出 ARM 模板”，在开发环境中导出数据工厂的资源管理器模板 。

   :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image-1.png" alt-text="导出资源管理器模板":::

1. 在测试和生产数据工厂中，选择“导入 ARM 模板”。 此操作会将你转到 Azure 门户，可以在其中导入已导出的模板。 选择“在编辑器中生成自己的模板”以打开资源管理器模板编辑器。

   :::image type="content" source="media/continuous-integration-delivery/custom-deployment-build-your-own-template.png" alt-text="生成自己的模板"::: 

1. 选择“加载文件”，然后选择生成的资源管理器模板。 此模板是在步骤 1 中导出的 .zip 文件中的 **arm_template.json** 文件。

   :::image type="content" source="media/continuous-integration-delivery/custom-deployment-edit-template.png" alt-text="编辑模板":::

1. 在设置部分输入配置值，例如链接服务凭据。 完成后，选择“购买”以部署该资源管理器模板。

   :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image5.png" alt-text="设置部分":::

## <a name="next-steps"></a>后续步骤

- [持续集成和交付概述](continuous-integration-delivery.md)
- [使用 Azure Pipelines 发布自动进行持续集成](continuous-integration-delivery-automate-azure-pipelines.md)
- [将自定义参数用于资源管理器模板](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [链接的资源管理器模板](continuous-integration-delivery-linked-templates.md)
- [使用修补程序生产环境](continuous-integration-delivery-hotfix-environment.md)
- [部署前和部署后示例脚本](continuous-integration-delivery-sample-script.md)