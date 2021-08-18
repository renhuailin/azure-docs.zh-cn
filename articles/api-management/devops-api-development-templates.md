---
title: 用于使用 ARM 模板进行 Azure API 管理的 CI/CD
description: 使用 Azure API 管理的 API DevOps 简介，使用 Azure 资源管理器模板在 CI/CD 管道中管理 API 部署
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: apimpm
ms.openlocfilehash: e7d0515262fc06d850b61f257e7cd577797bcf39
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742747"
---
# <a name="cicd-for-api-management-using-azure-resource-manager-templates"></a>用于使用 Azure 资源管理器模板进行 API 管理的 CI/CD

本文介绍如何通过 Azure 资源管理器模板将 API DevOps 与 Azure API 管理配合使用。 由于 API 的战略价值，持续集成和持续部署 (CI/CD) 管道已成为 API 开发的一个重要方面。 通过它，组织无需执行容易出错的手动步骤即可实现 API 更改部署自动化，更早检测到问题，并最终加快向用户提供价值。 

有关本文所述的实施 DevOps 方法的详细信息、工具和代码示例，请参阅 GitHub 中的开源 [Azure API Management DevOps 资源工具包](https://github.com/Azure/azure-api-management-devops-resource-kit)。 因为客户提供了各种工程文化和现有自动化解决方案，所以这种方法并非一个通用解决方案。

## <a name="the-problem"></a>问题

如今，组织通常有多个部署环境 (例如，开发、测试和生产) ，并对每个环境使用单独的 API 管理实例。 某些实例由多个开发团队共享，这些团队负责不同发布时间的不同 API。

因此，客户面临以下挑战：

* 如何将 API 自动部署到 API 管理
* 如何将配置从一个环境迁移到另一个环境
* 如何避免共享相同 API 管理实例的不同开发团队间的干扰

## <a name="manage-configurations-in-resource-manager-templates"></a>管理资源管理器模板中的配置

下图说明了建议的方法。 

:::image type="content" source="media/devops-api-development-templates/apim-devops.png" alt-text="说明 DevOps 与 API 管理的图。":::

在此示例中，有两种部署环境：“开发”和“生产”。 每个环境都有其自己的 API 管理实例。 

* API 开发人员有权访问开发实例，并可将其用于开发和测试其 API。 
* 名为“API 发布者”的指定团队管理生产实例。

此建议方法中的关键是要在 [Azure 资源管理器模板](../azure-resource-manager/templates/syntax.md)中保留所有 API 管理配置。 组织应将这些模板保留在一个源代码管理系统（如 Git）中。 如图所示，发布者存储库包含模板集合中的生产 API 管理实例的所有配置：

|模板  |说明  |
|---------|---------|
|服务模板     | API 管理实例的服务级别配置，例如定价层和自定义域。         |
|共享模板     |  在整个 API 管理实例中共享资源，如组、产品和记录器。    |
|API 模板     |  API 及其子资源的配置：操作、策略、诊断设置。        |
|主模板     |   通过[链接](../azure-resource-manager/templates/linked-templates.md)到所有模板并按顺序进行部署，将所有内容连接在一起。 若要将所有配置部署到 API 管理实例，请部署主模板。 还可以单独部署每个模板。       |

API 开发人员会创建发布者存储库的分支为开发人员存储库，并处理对其 API 进行的更改。 在大多数情况下，开发人员将重点放在其 API 的 API 模板上，无需更改共享或服务模板。

## <a name="migrate-configurations-to-templates"></a>将配置迁移到模板
使用资源管理器模板时，API 开发人员面临着以下挑战：

* API 开发人员通常使用 [OpenAPI 规范](https://github.com/OAI/OpenAPI-Specification)，可能不熟悉资源管理器架构。 手动创作模板可能容易出错。 

   使用资源工具包中名为 [Creator](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#Creator) 的工具，可根据开放 API 规范文件自动创建 API 模板。 此外，开发人员还可以为 XML 格式的 API 提供 API 管理策略。 

* 对于已经在使用 API 管理的客户，另一个挑战是将现有配置提取到资源管理器模板中。 对于这些客户，可使用资源工具包中名为 [Extractor](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#extractor) 的工具，通过从其 API 管理实例中提取配置来生成模板。  

## <a name="workflow"></a>工作流

* API 开发人员完成 API 开发和测试，并生成 API 模板之后，就可以提交拉取请求，将更改合并到发布者存储库中。 

* API 发布者可以验证拉取请求，并确保更改的安全性和符合性。 例如，他们可以检查是否只允许使用 HTTPS 与 API 通信。 大多数验证都可以作为 CI/CD 管道中的一个步骤自动执行。

* 成功批准且合并更改后，API 发布者可以选择按计划或按需将其部署到生产实例。 可以使用 [GitHub Actions](https://docs.github.com/en/actions)、[Azure Pipelines](/azure/devops/pipelines)、[Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)、[Azure CLI](../azure-resource-manager/templates/deploy-cli.md) 或其他工具自动部署模板。


使用此方法，组织可以自动将 API 更改部署到 API 管理实例，并轻松地将更改从一个环境提升到另一个环境。 由于不同的 API 开发团队会使用不同的 API 模板和文件集，因此可以防止不同团队之间发生干扰。

## <a name="video"></a>视频

> [!VIDEO https://www.youtube.com/embed/4Sp2Qvmg6j8]

## <a name="next-steps"></a>后续步骤

- 有关其他信息、工具和示例模板，请参阅开源 [Azure API Management DevOps 资源工具包](https://github.com/Azure/azure-api-management-devops-resource-kit)。
