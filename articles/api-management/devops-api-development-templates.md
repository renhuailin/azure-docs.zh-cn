---
title: 使用 ARM 模板进行 Azure API 管理的 CI/CD
description: 使用 azure API 管理进行 API DevOps 简介，使用 Azure 资源管理器模板在 CI/CD 管道中管理 API 部署
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: apimpm
ms.openlocfilehash: 62f163b9ce649cd5ddb52b4325682570633dfb92
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183152"
---
# <a name="cicd-for-api-management-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板进行 API 管理的 CI/CD

本文介绍如何通过 Azure 资源管理器模板将 API DevOps 与 Azure API 管理配合使用。 使用 Api 的战略价值，持续集成和持续部署 (CI/CD) 管道已成为 API 开发的一个重要方面。 它使组织能够在不容易出错的手动步骤下自动部署 API 更改，并在前面检测问题，并最终为用户提供更快的价值。 

有关实现本文所述的 DevOps 方法的详细信息、工具和代码示例，请参阅 GitHub 中的开源 [AZURE API Management DevOps 资源工具包](https://github.com/Azure/azure-api-management-devops-resource-kit) 。 因为客户提供了各种工程文化和现有的自动化解决方案，所以这种方法并不是一个适用于所有解决方案的解决方案。

## <a name="the-problem"></a>问题

如今，组织通常具有多个部署环境 (例如，开发、测试和生产) ，并对每个环境使用单独的 API 管理实例。 某些实例由多个开发团队共享，这些团队负责不同版本节奏的不同 Api。

因此，客户面临以下挑战：

* 如何将 Api 自动部署到 API 管理
* 如何将配置从一个环境迁移到另一个环境
* 如何避免不同开发团队间共享同一 API 管理实例的干扰

## <a name="manage-configurations-in-resource-manager-templates"></a>管理资源管理器模板中的配置

下图说明了建议的方法。 

:::image type="content" source="media/devops-api-development-templates/apim-devops.png" alt-text="演示 DevOps 与 API 管理的关系图。":::

在此示例中，有两种部署环境： *开发* 和 *生产*。 每个都有其自己的 API 管理实例。 

* API 开发人员有权访问开发实例，并可将其用于开发和测试其 Api。 
* 名为 *API 发布者* 的指定团队管理生产实例。

此建议的方法中的关键是要在 Azure 中保留所有 API 管理配置 [资源管理器模板](../azure-resource-manager/templates/template-syntax.md)。 组织应将这些模板保留在一个源代码管理系统（如 Git）中。 如图所示，发布者存储库包含模板集合中生产 API 管理实例的所有配置：

|模板  |说明  |
|---------|---------|
|服务模板     | API 管理实例的服务级别配置，例如定价层和自定义域。         |
|共享模板     |  在整个 API 管理实例（如组、产品和记录器）中共享资源。    |
|API 模板     |  Api 及其子资源的配置：操作、策略、诊断设置。        |
|主 (主) 模板     |   通过 [链接](../azure-resource-manager/templates/linked-templates.md) 到所有模板并按顺序进行部署，将所有内容联系在一起。 若要将所有配置部署到 API 管理实例，请部署主模板。 你还可以单独部署每个模板。       |

API 开发人员会将发布者存储库分叉到开发人员存储库，并处理其 Api 的变化。 在大多数情况下，它们将重点放在 API 模板上用于其 Api，而无需更改共享或服务模板。

## <a name="migrate-configurations-to-templates"></a>将配置迁移到模板
使用资源管理器模板时，API 开发人员面临着挑战：

* API 开发人员通常使用 [OpenAPI 规范](https://github.com/OAI/OpenAPI-Specification) ，可能不熟悉资源管理器架构。 手动创作模板可能容易出错。 

   资源工具包中名为 [创建者](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#Creator) 的工具可帮助根据开放的 api 规范文件自动创建 API 模板。 此外，开发人员还可以为 XML 格式的 API 提供 API 管理策略。 

* 对于已经使用 API 管理的客户，另一个难题是将现有配置提取到资源管理器模板中。 对于这些客户，资源工具包中名为 " [提取](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#extractor) 器" 的工具可以通过从其 API 管理实例提取配置来帮助生成模板。  

## <a name="workflow"></a>工作流

* API 开发人员完成了 API 开发和测试，并生成了 API 模板之后，就可以提交拉取请求将更改合并到发布服务器存储库。 

* API 发布者可以验证拉取请求，并确保更改的安全性和合规性。 例如，他们可以检查是否只允许使用 HTTPS 与 API 通信。 大多数验证都可以作为 CI/CD 管道中的一个步骤自动执行。

* 成功批准并合并更改后，API 发布者可以选择按计划或按需将其部署到生产实例。 可以使用 [GitHub 操作](https://github.com/Azure/apimanagement-devops-samples)、 [Azure Pipelines](/azure/devops/pipelines)、 [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)、 [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)或其他工具自动部署模板。


使用此方法，组织可以自动将 API 更改部署到 API 管理实例，并轻松地将更改从一个环境升级到另一个环境。 由于不同的 API 开发团队将使用不同的 API 模板和文件集，因此它可以防止不同团队之间发生干扰。

## <a name="video"></a>视频

> [!VIDEO https://www.youtube.com/embed/4Sp2Qvmg6j8]

## <a name="next-steps"></a>后续步骤

- 有关其他信息、工具和示例模板，请参阅开源 [AZURE API Management DevOps 资源工具包](https://github.com/Azure/azure-api-management-devops-resource-kit) 。