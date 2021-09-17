---
title: 'Azure 数字孪生请求失败，状态为: 403 (已禁用)'
description: 'Azure 数字孪生“服务请求失败。 状态: 403 (已禁用)”的原因和解决方法。'
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 8/20/2021
ms.openlocfilehash: b3ad9c84e35483cf81bde83703b01ef0ff3d8a9d
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772328"
---
# <a name="service-request-failed-status-403-forbidden"></a>服务请求失败。 状态: 403 (已禁用)

本文介绍 Azure 数字孪生服务请求接收到 403 错误的原因和解决步骤。 

## <a name="symptoms"></a>症状

多种需要进行身份验证的服务请求类型可能发生此错误。 其结果是 API 请求失败，返回错误状态 `403 (Forbidden)`。

## <a name="causes"></a>原因

### <a name="cause-1"></a>原因 #1

大多数情况下，此错误表明未正确设置对此服务的 Azure 基于角色的访问控制 (Azure RBAC) 权限。 对 Azure 数字孪生实例的很多操作要求你对正在尝试管理的实例拥有“Azure 数字孪生数据所有者”角色。 

### <a name="cause-2"></a>原因 #2

如果客户端应用与使用[应用注册](./how-to-create-app-registration-portal.md)进行身份验证的 Azure 数字孪生通信，则可能会发生此错误，因为尚未为应用注册设置对 Azure 数字孪生服务的权限。

必须为应用注册配置对 Azure 数字孪生 API 的访问权限。 然后，在根据应用注册对客户端应用进行身份验证时，会向其授予已为应用注册配置的权限。

## <a name="solutions"></a>解决方案

### <a name="solution-1"></a>解决方案 #1

第一种解决方案是验证 Azure 用户对正在尝试管理的实例是否拥有“Azure 数字孪生数据所有者”角色。 如果没有此角色，请对其进行设置。

此角色不同于
* 此角色之前在 Azure 数字孪生所有者（预览版）预览期间使用的名称。 在这种情况下，角色相同，但名称已更改。
* 对整个 Azure 订阅的“所有者”角色。 “Azure 数字孪生数据所有者”是 Azure 数字孪生中的角色，其作用范围限定为单个 Azure 数字孪生实例。
* Azure 数字孪生中的“所有者”角色。 它们是两个不同的 Azure 数字孪生管理角色，“Azure 数字孪生数据所有者”是应该用于管理的角色。

#### <a name="check-current-setup"></a>检查当前设置

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>解决问题 

如果你没有此角色分配，则在 Azure 订阅中具有“所有者”角色的用户应运行以下命令，向 Azure 用户授予对 Azure 数字孪生实例的“Azure 数字孪生数据所有者”角色。  

如果你是订阅的“所有者”，则可以自己运行此命令。 如果不是，请联系“所有者”代表你运行此命令。

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Data Owner"
```

有关此角色要求和分配过程的详细信息，请参阅“操作说明：设置实例和身份验证（CLI 或门户）”的[“设置用户的访问权限”部分](how-to-set-up-instance-CLI.md#set-up-user-access-permissions)。

如果你已拥有此角色分配且使用 Azure AD 应用注册对客户端应用进行身份验证，但此解决方案未解决 403 问题，则可继续执行下一个解决方案。

### <a name="solution-2"></a>解决方法 #2

如果要使用 Azure AD 应用注册对客户端应用进行身份验证，则第二种可能的解决方案是验证是否已为应用注册配置对 Azure 数字孪生服务的权限。 如果未配置这些，请对其进行设置。

#### <a name="check-current-setup"></a>检查当前设置

若要检查是否已正确配置权限，请导航到 Azure 门户的“[Azure AD 应用注册概述](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)”页。 可以通过在门户搜索栏中搜索“应用注册”来自行访问此页。

切换到“所有应用程序”选项卡以查看在订阅中创建的所有应用注册。

在列表中应该可以看到已创建的应用注册。 选择它以打开其详细信息。

:::image type="content" source="media/troubleshoot-error-403/app-registrations.png" alt-text="Azure 门户中“应用注册”页的屏幕截图。":::

首先，验证注册中是否已正确设置 Azure 数字孪生权限：从菜单栏中选择“清单”，以查看应用注册的清单代码。 滚动到代码窗口的底部，在 `requiredResourceAccess` 下查找以下字段。 这些值应与以下屏幕截图中的值匹配：

:::image type="content" source="media/troubleshoot-error-403/verify-manifest.png" alt-text="Azure 门户中 Azure AD 应用注册清单屏幕截图。":::

接下来，从菜单栏中选择“API 权限”，验证此应用注册是否包含对 Azure 数字孪生的读/写权限。 应看到如下条目：

:::image type="content" source="media/troubleshoot-error-403/verify-api-permissions.png" alt-text="Azure 门户中 Azure AD 应用注册的 API 权限屏幕截图，其中显示了 Azure 数字孪生的“读/写访问权限”。":::

#### <a name="fix-issues"></a>解决问题

如果出现任何不同于以上所述的情况，请按照[创建应用注册](./how-to-create-app-registration-portal.md)中如何设置应用注册的说明进行操作。

## <a name="next-steps"></a>后续步骤

阅读有关 Azure 数字孪生新实例创建和身份验证的设置步骤：
* [设置实例和身份验证 (CLI)](how-to-set-up-instance-cli.md)

阅读有关 Azure 数字孪生的安全性和权限的详细信息：
* [Azure 数字孪生解决方案的安全性](concepts-security.md)