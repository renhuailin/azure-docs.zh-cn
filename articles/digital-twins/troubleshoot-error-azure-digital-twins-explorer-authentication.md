---
title: Azure Digital Twins Explorer 身份验证错误
description: Azure Digital Twins Explorer 中的“身份验证失败” 的原因和解决方法。
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/8/2021
ms.openlocfilehash: ef78f1dd6089201744de19900738ebcc6e5f70db
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468665"
---
# <a name="authentication-failed"></a>验证失败

本文介绍在本地计算机上运行 [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) 示例时收到“身份验证失败”错误的原因和解决步骤。 

## <a name="symptoms"></a>症状

在设置和运行 Azure Digital Twins Explorer 应用程序时，对应用执行身份验证尝试后，收到以下错误消息：

:::image type="content" source="media/troubleshoot-error-azure-digital-twins-explorer-authentication/authentication-error.png" alt-text="Azure Digital Twins Explorer 中的身份验证失败错误消息的屏幕截图。":::

## <a name="causes"></a>原因

### <a name="cause-1"></a>原因 #1

如果 Azure 帐户没有在 Azure 数字孪生实例上设置所需的 Azure 基于角色的访问控制 (Azure RBAC) 权限，可能会发生此错误。 若要访问实例中的数据，必须分别在你尝试读取或管理的实例上拥有“Azure 数字孪生数据读取者”或“Azure 数字孪生数据所有者”角色。 

有关 Azure 数字孪生中的安全性和角色的详细信息，请参阅 [Azure 数字孪生安全性解决方案](concepts-security.md)。

## <a name="solutions"></a>解决方案

### <a name="solution-1"></a>解决方案 #1

验证 Azure 用户是在 Azure 数字孪生实例上拥有“Azure 数字孪生数据读取者”角色（如果你只是尝试读取数据）还是在实例上拥有“Azure 数字孪生数据所有者”角色（如果你尝试管理数据）。

请注意，此角色不同于...
* 此角色之前在Azure 数字孪生所有者 (预览) 预览期间使用的名称（角色相同，但名称已更改）
* 对整个 Azure 订阅的“所有者”角色。 “Azure 数字孪生数据所有者”是 Azure 数字孪生中的角色，其作用范围限定为单个 Azure 数字孪生实例。
* Azure 数字孪生中的“所有者”角色。 它们是两个不同的 Azure 数字孪生管理角色，“Azure 数字孪生数据所有者”是应该用于管理的角色。

 如果你没有此角色，请进行设置以解决问题。

#### <a name="check-current-setup"></a>检查当前设置

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>解决问题 

如果你没有此角色分配，则对 Azure 订阅具有“所有者”角色的用户应运行以下命令，向 Azure 用户授予对 Azure 数字孪生实例的相应角色。 

如果你是订阅的“所有者”，则可以自己运行此命令。 如果不是，请联系“所有者”代表你运行此命令。 角色名称为“Azure 数字孪生数据所有者”（用于编辑访问权限），或“Azure 数字孪生数据读取者”（用于读取访问权限）。

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "<role-name>"
```

有关此角色要求和分配过程的详细信息，请参阅“操作说明：设置实例和身份验证（CLI 或门户）”的[设置用户访问权限](how-to-set-up-instance-CLI.md#set-up-user-access-permissions)部分。

## <a name="next-steps"></a>后续步骤

阅读有关 Azure 数字孪生新实例创建和身份验证的设置步骤：
* [设置实例和身份验证 (CLI)](how-to-set-up-instance-cli.md)

阅读有关 Azure 数字孪生的安全性和权限的详细信息：
* [Azure 数字孪生解决方案的安全性](concepts-security.md)
