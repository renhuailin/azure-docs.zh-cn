---
title: Azure 基于角色的访问控制 - 自定义视觉
titleSuffix: Azure Cognitive Services
description: 本文介绍如何为自定义视觉项目配置 Azure 基于角色的访问控制。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: 050b4011dbc46b6b6567965ed94847dbaf3560c6
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982305"
---
# <a name="azure-role-based-access-control"></a>Azure 基于角色的访问控制

自定义视觉支持 Azure 基于角色的访问控制 (Azure RBAC)，这是用于管理对 Azure 资源的个人访问权限的授权系统。 使用 Azure RBAC，可为不同的团队成员分配自定义视觉项目的不同级别权限。 有关 Azure RBAC 的详细信息，请参阅 [Azure RBAC 文档](../../role-based-access-control/index.yml)。

## <a name="add-role-assignment-to-custom-vision-resource"></a>将角色分配添加到自定义视觉资源

可以将 Azure RBAC 分配到自定义视觉资源。 若要授予对 Azure 资源的访问权限，可以添加角色分配。
1. 在 [Azure 门户](https://ms.portal.azure.com/)中，选择“所有服务”。 
1. 然后选择“认知服务”，并导航到特定自定义视觉培训资源。
   > [!NOTE]
   > 你还可以为整个资源组、订阅或管理组设置 Azure RBAC。 为此，请选择所需的作用域级别，然后导航到所需的项目（例如，选择“资源组”，然后单击到所需的资源组）。
1. 在左侧导航窗格上，选择“访问控制(IAM)”。
1. 选择“添加” -> “添加角色分配”。
1. 在下一屏幕上的“角色”选项卡上，选择要添加的角色。
1. 在“成员”选项卡上，选择用户、组、服务主体或托管标识。
1. 在“查看 + 分配”选项卡上，选择“查看 + 分配”，以分配角色 。

几分钟后，将向目标分配所选作用域中的选定角色。 有关这些步骤的帮助，请参阅[使用 Azure 门户分配 Azure 角色](https://review.docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

## <a name="custom-vision-role-types"></a>自定义视觉角色类型

使用下表来确定自定义视觉资源的访问需求。

|角色  |权限  |
|---------|---------|
|`Cognitive Services Custom Vision Contributor`     | 对项目的完全访问权限，包括可以创建、编辑或删除项目。        |
|`Cognitive Services Custom Vision Trainer`     | 完全访问权限，但不允许创建或删除项目。 训练者可以查看和编辑项目，并对模型进行训练、发布、取消发布或导出。        |
|`Cognitive Services Custom Vision Labeler`     | 可以上传、编辑或删除训练图像以及创建、添加、移除或删除标记。 标记者可以查看项目，但无法更新除训练图像和标记以外的任何内容。         |
|`Cognitive Services Custom Vision Deployment`     | 可以发布、取消发布或导出模型。 部署者可以查看项目，但无法更新项目、训练图像或标记。        |
|`Cognitive Services Custom Vision Reader`     | 可以查看项目。 读者不能进行任何更改。        |