---
title: Speech Studio 中基于角色的访问控制 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何通过 Speech Studio 将访问角色分配给语音服务。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/07/2021
ms.author: pafarley
ms.openlocfilehash: 72d2acd25381af654fb098d81a7cdc4ec0bbce22
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699361"
---
# <a name="azure-role-based-access-control-in-speech-studio"></a>Speech Studio 中 Azure 基于角色的访问控制 

Speech Studio 支持 Azure 基于角色的访问控制 (Azure RBAC)，这是用于管理对 Azure 资源的个人访问权限的授权系统。 使用 Azure RBAC，你可以为 Speech Studio 操作分配不同级别的权限给不同的团队成员。 有关 Azure RBAC 的详细信息，请参阅 [Azure RBAC 文档](/azure/role-based-access-control/overview)。

## <a name="prerequisites"></a>先决条件

* 必须使用 Azure 帐户和语音资源登录 Speech Studio。 请参阅 [Speech Studio 概述](speech-studio-overview.md)。

## <a name="manage-role-assignments-for-speech-resources"></a>管理语音资源的角色分配

若要授予对 Azure 语音资源的访问权限，请在 Azure 门户中通过 Azure RBAC 工具添加角色分配。 

几分钟后，将向目标分配所选作用域中的选定角色。 有关这些步骤的帮助，请参阅[使用 Azure 门户分配 Azure 角色](/azure/role-based-access-control/role-assignments-portal?tabs=current)。

## <a name="supported-built-in-roles-in-speech-studio"></a>Speech Studio 中支持的内置角色

角色定义是权限的集合。 如果没有针对权限的任何唯一自定义要求，请使用以下建议的内置角色：

| **内置角色** | 列出资源密钥的权限 | 自定义语音操作的权限 | 自定义语音操作的权限| 其他功能的权限 |
| ---| ---| ---| ---| --|
|**所有者** |是 |对项目的完全访问权限，包括创建、编辑或删除项目/数据/模型/终结点的权限 |对项目的完全访问权限，包括创建、编辑或删除项目/数据/模型/终结点的权限 |完全访问权限 |
|**参与者** |是 |对项目的完全访问权限，包括创建、编辑或删除项目/数据/模型/终结点的权限 |对项目的完全访问权限，包括创建、编辑或删除项目/数据/模型/终结点的权限 |完全访问权限 |
|认知服务参与者 |是 |对项目的完全访问权限，包括创建、编辑或删除项目/数据/模型/终结点的权限 |对项目的完全访问权限，包括创建、编辑或删除项目/数据/模型/终结点的权限 |完全访问权限 |
|认知服务用户 |是 |对项目的完全访问权限，包括创建、编辑或删除项目/数据/模型/终结点的权限 |对项目的完全访问权限，包括创建、编辑或删除项目/数据/模型/终结点的权限 |完全访问权限 |
|认知服务语音参与者 |否 |对项目的完全访问权限，包括创建、编辑或删除项目/数据/模型/终结点的权限 |对项目的完全访问权限，包括创建、编辑或删除项目/数据/模型/终结点的权限 |完全访问权限 |
|认知服务语音用户 |否 |可以查看项目/数据集/模型/终结点；无法创建、编辑和删除 |可以查看项目/数据集/模型/终结点；无法创建、编辑和删除 |完全访问权限 |
|认知服务数据读取者（预览版） |否 |可以查看项目/数据集/模型/终结点；无法创建、编辑和删除 |可以查看项目/数据集/模型/终结点；无法创建、编辑和删除 |完全访问权限 |

或者，你可以[创建自己的自定义角色](/azure/role-based-access-control/custom-roles)。 例如，你可以创建一个有权上传自定义语音数据集权限但不能将自定义语音模型部署到终结点的自定义角色。

> [!NOTE]
> Speech Studio 支持基于密钥的身份验证。 有权列出资源密钥 (`Microsoft.CognitiveServices/accounts/listKeys/action`) 的角色将首先使用资源密钥进行身份验证，并且只要在 Azure 门户中启用了密钥身份验证，就可以完全访问 Speech Studio 操作。 如果服务管理员禁用密钥身份验证，则这些角色将失去对 Studio 的所有访问权限。

> [!NOTE]
> 一个资源可以分配或继承多个角色，并且对该资源的最终访问级别是操作级别的所有角色权限的组合。

## <a name="next-steps"></a>后续步骤

详细了解[语音识别服务静态数据的加密](/azure/cognitive-services/speech-service/speech-encryption-of-data-at-rest)。