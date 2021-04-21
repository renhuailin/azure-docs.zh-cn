---
title: include 文件
description: include 文件
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 53e3f37d14153f3a2d7b5886a49b08ca9052b128
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799873"
---
| 资源 | 限制 |
| --- | --- |
| 每个 Azure AD 租户的管理组数 | 10,000 |
| 每个管理组的订阅数 | 不受限制。 |
| 管理组层次结构的级别数 | 根级别加上 6 个级别<sup>1</sup> |
| 每个管理组的直接父管理组数 | 一种 |
| 每个位置的[管理组级别部署数](../articles/azure-resource-manager/templates/deploy-to-management-group.md) | 800<sup>2</sup> |

<sup>1</sup>6 个级别不包括订阅级别。

<sup>2</sup>如果达到 800 个部署的限制，则会从历史记录中删除不再需要的部署。 若要删除管理组级别部署，请使用 [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) 或 [az deployment mg delete](/cli/azure/deployment/mg#az_deployment_mg_delete)。
