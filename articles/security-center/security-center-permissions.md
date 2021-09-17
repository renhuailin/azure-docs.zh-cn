---
title: Azure 安全中心的权限 | Microsoft Docs
description: 本文介绍 Azure 安全中心如何使用基于角色的访问控制为用户分配权限并确定每个角色允许的操作。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 07/04/2021
ms.author: memildin
ms.openlocfilehash: 9f35b3e79736ed195af9e0fc1303a24951fbb675
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734857"
---
# <a name="permissions-in-azure-security-center"></a>Azure 安全中心的权限

安全中心使用 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) 提供可在 Azure 中分配给用户、组和服务的[内置角色](../role-based-access-control/built-in-roles.md)。

安全中心会评估资源的配置以识别安全问题和漏洞。 在安全中心，只有在为订阅或资源的资源组分配了“所有者”、“参与者”或“读取者”角色时，你才能看到与资源相关的信息。

除了内置角色外，还有两个特定于安全中心的角色：

* **安全读取者**：属于此角色的用户对安全中心具有查看权限。 该用户可查看建议、警报、安全策略和安全状态，但不能更改。
* **安全管理员**：属于此角色的用户具有与安全读取者相同的权限，此外，还可以更新安全策略、关闭警报和建议。

> [!NOTE]
> 安全角色（安全读取者和安全管理员）只能访问安全中心。 安全角色无权访问存储、Web 和移动或物联网等其他 Azure 服务。

## <a name="roles-and-allowed-actions"></a>角色和允许的操作

下表显示安全中心的角色和允许的操作。

| **操作**                                                                                                                      | [安全读取者](../role-based-access-control/built-in-roles.md#security-reader) / <br> [读者](../role-based-access-control/built-in-roles.md#reader) | [安全管理员](../role-based-access-control/built-in-roles.md#security-admin) | [参与者](../role-based-access-control/built-in-roles.md#contributor) / [所有者](../role-based-access-control/built-in-roles.md#owner)| [参与者](../role-based-access-control/built-in-roles.md#contributor)| [所有者](../role-based-access-control/built-in-roles.md#owner)|
|:----------------------------------------------------------------------------------------------------------------------------|:-----------------------------:|:--------------:|:------------------------------------------------------:|:------------------------:|:------------------:|
||||（资源组级别）|（订阅级别）|（订阅级别）|
| 添加/分配计划（包括合规性标准）                                                         | -                             | -              | -                                                      | -                        | ✔                 |
| 编辑安全策略                                                                                                        | -                             | ✔             | -                                                      | -                        | ✔                 |
| 启用/禁用 Azure Defender 计划                                                                                             | -                             | ✔             | -                                                      | -                        | ✔                 |
| 启用/禁用自动预配                                                                                          | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| 消除警报                                                                                                              | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| 应用资源的安全建议</br> （和使用[修补程序](security-center-remediate-recommendations.md#fix-button)） | -                             | -              | ✔                                                     | ✔                        | ✔                 |
| 查看警报和建议                                                                                             | ✔                            | ✔              | ✔                                                     | ✔                        | ✔                 |
||||||

> [!NOTE]
> 对于需要完成任务的用户，建议尽可能为其分配权限最小的角色。 例如，将“读者”角色分配到只需查看有关资源的安全运行状况而不执行操作（例如应用建议或编辑策略）的用户。

## <a name="next-steps"></a>后续步骤
本文介绍安全中心如何使用 Azure RBAC 将权限分配给用户，并辨别每个角色允许的操作。 现在，已熟悉监视订阅安全状态所需的角色分配，请编辑安全策略，并应用建议，了解如何：

- [在安全中心设置安全策略](tutorial-security-policy.md)
- [管理安全中心的安全建议](security-center-recommendations.md)
- [管理和响应安全中心的安全警报](security-center-managing-and-responding-alerts.md)
- [监视合作伙伴安全解决方案](./security-center-partner-integration.md)