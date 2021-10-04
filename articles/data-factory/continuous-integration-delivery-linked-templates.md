---
title: 使用链接的资源管理器模板
description: 了解如何使用链接的资源管理器模板在 Azure 数据工厂管道中进行持续集成和交付。
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d2df62471818bbca069802f3c60993ce79bb50dd
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219260"
---
# <a name="linked-resource-manager-templates-with-cicd"></a>使用链接的资源管理器模板进行 CI/CD

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

如果为数据工厂设置了持续集成和持续交付 (CI/CD)，随着工厂的不断增大，最终可能会超过 Azure 资源管理器模板限制。 例如，有一项限制是资源管理器模板中的最大资源数。 为了容纳较大的工厂，同时为工厂生成完整的资源管理器模板，数据工厂现在会生成链接的资源管理器模板。 借助此功能，整个工厂有效负载将分解为多个文件，这样就不会受到限制的约束。

## <a name="finding-the-linked-templates"></a>查找链接的模板

如果已配置 Git，将会生成链接的模板，并将其连同完整的资源管理器模板一起保存在 adf_publish 分支中名为 linkedTemplates 的新文件夹内：

:::image type="content" source="media/continuous-integration-delivery/linked-resource-manager-templates.png" alt-text="链接的资源管理器模板文件夹":::

链接的资源管理器模板通常包括一个主模板，以及一组链接到主模板的子模板。 父模板名为 ArmTemplate_master.json，子模板按照 ArmTemplate_0.json、ArmTemplate_1.json ... 的模式命名。 

## <a name="using-linked-templates"></a>使用链接的模板
若要使用链接的模板而不是完整的资源管理器模板，请将 CI/CD 任务更新为指向 ArmTemplate_master.json 而不是 ArmTemplateForFactory.json（完整资源管理器模板）。 资源管理器还要求将链接的模板上传到存储帐户，使 Azure 能够在部署期间访问这些模板。 有关详细信息，请参阅[使用 VSTS 部署链接的资源管理器模板](/archive/blogs/najib/deploying-linked-arm-templates-with-vsts)。

不要忘记在执行部署任务之前和之后在 CI/CD 管道中添加数据工厂脚本。

如果未配置 Git，可以通过“ARM 模板”列表中的“导出 ARM 模板”访问链接的模板。 

部署资源时，可以指定部署为增量更新还是完整更新。 这两种模式之间的区别在于资源管理器如何处理资源组中不在模板中的现有资源。 请查看[部署模式](../azure-resource-manager/templates/deployment-modes.md)。

## <a name="next-steps"></a>后续步骤

- [持续集成和交付概述](continuous-integration-delivery.md)
- [使用 Azure Pipelines 发布自动进行持续集成](continuous-integration-delivery-automate-azure-pipelines.md)
- [手动将资源管理器模板推广到每个环境](continuous-integration-delivery-manual-promotion.md)
- [将自定义参数用于资源管理器模板](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [使用修补程序生产环境](continuous-integration-delivery-hotfix-environment.md)
- [部署前和部署后示例脚本](continuous-integration-delivery-sample-script.md)