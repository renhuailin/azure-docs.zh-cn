---
title: 使用修补程序生产环境
description: 了解如何在 Azure 数据工厂管道中通过持续集成和交付使用修补程序生产环境。
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 788b576d351984c4f6a3dff7fd43056aa95aba3c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219261"
---
# <a name="using-a-hotfix-production-environment"></a>使用修补程序生产环境

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

将某个工厂部署到生产环境后，如果你发现某个 bug 需要立即予以修复，但无法部署当前协作分支，则你可能需要部署一个修补程序。 此方法称作快速修复工程 (QFE)。

## <a name="steps-to-deploy-a-hotfix"></a>部署修补程序的步骤

使用以下步骤在生产环境和测试环境中部署修补程序。

1.    在 Azure DevOps 中，转到已部署至生产环境的版本。 找到已部署的最后一个提交内容。

1.    从提交消息中获取协作分支的提交 ID。

1.    基于该提交内容创建新的修补程序分支。

1.    转到“Azure 数据工厂工作室”并切换到修补程序分支。

1.    使用 Azure 数据工厂工作室修复该 bug。 测试更改。

1.    验证修复结果后，选择“导出 ARM 模板”以获取修补程序资源管理器模板。

1.    手动将此生成签入到 adf_publish 分支。

1.    如果已将发布管道配置为根据 adf_publish 签入内容自动触发，则会自动启动新的发布。 否则，请手动将发布排入队列。

1.    将修补程序版本部署到测试工厂和生产工厂。 此版本包含以前的生产有效负载，以及你在步骤 5 中完成的修复。

1.   将此修补程序中的更改添加到开发分支，使以后的版本不会出现相同的 bug。

## <a name="video-tutorial"></a>视频教程
请观看深度视频教程下面的视频，了解如何对环境进行热修复。 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4I7fi]

## <a name="next-steps"></a>后续步骤

- [持续集成和交付概述](continuous-integration-delivery.md)
- [使用 Azure Pipelines 发布自动进行持续集成](continuous-integration-delivery-automate-azure-pipelines.md)
- [手动将资源管理器模板推广到每个环境](continuous-integration-delivery-manual-promotion.md)
- [将自定义参数用于资源管理器模板](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [链接的资源管理器模板](continuous-integration-delivery-linked-templates.md)
- [部署前和部署后示例脚本](continuous-integration-delivery-sample-script.md)