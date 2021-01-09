---
title: 'Azure 数字孪生请求失败，状态为： 403 (禁用) '
description: "\"服务请求失败\" 的原因和解决方法。 状态： 403 (禁用 Azure 数字孪生上的) \"。"
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: 1517c066fe20d478094f57d85d6e27f355a93601
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98049807"
---
# <a name="service-request-failed-status-403-forbidden"></a>服务请求失败。 状态： 403 (禁止访问) 

本文介绍从服务请求到 Azure 数字孪生收到403错误的原因和解决步骤。 

## <a name="symptoms"></a>症状

需要身份验证的多个服务请求类型可能发生此错误。 其结果是 API 请求失败，并返回错误状态 `403 (Forbidden)` 。

## <a name="causes"></a>原因

### <a name="cause-1"></a>原因 #1

最常见的情况是，此错误表明你的 Azure 基于角色的访问控制 (没有正确设置服务的 Azure RBAC) 权限。 Azure 数字孪生实例的许多操作要求你在 **尝试管理的实例上** 拥有 *Azure 数字孪生数据所有者* 角色。 

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

### <a name="cause-2"></a>原因 #2

如果你使用客户端应用程序与使用 [应用注册](how-to-create-app-registration.md)进行身份验证的 Azure 数字孪生通信，则可能会发生此错误，因为你的应用注册没有为 Azure 数字孪生服务设置权限。

应用注册必须具有为 Azure 数字孪生 Api 配置的访问权限。 然后，在客户端应用对应用注册进行身份验证时，会向其授予应用注册已配置的权限。

## <a name="solutions"></a>解决方案

### <a name="solution-1"></a>解决方案 #1

第一种解决方案是验证 Azure 用户在尝试管理的实例上是否拥有 _**Azure 数字孪生数据所有者**_ 角色。 如果没有此角色，请对其进行设置。

请注意，此角色不同于 .。。
* 此角色的以前的名称在预览期间， *Azure 数字孪生所有者 (预览)* (角色相同，但名称已更改) 
* 整个 Azure 订阅的 *所有者* 角色。 *Azure 数字孪生数据所有者* 是 Azure 数字孪生中的一个角色，其作用域为此单个 Azure 数字孪生实例。
* Azure 数字孪生中的 *所有者* 角色。 它们是两个不同的 Azure 数字孪生管理角色， *Azure 数字孪生数据所有者* 是应用于管理的角色。

#### <a name="check-current-setup"></a>检查当前设置

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>解决问题 

如果你没有此角色分配，则在 **azure 订阅** 中具有所有者角色的用户应运行以下命令，以向 azure 用户提供 Azure 数字 **孪生实例** 上的 *azure 数字孪生数据所有者* 角色。 

如果你是订阅的所有者，则可以自己运行此命令。 如果不是，请联系所有者以代表你运行此命令。

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Data Owner"
```

有关此角色要求和分配过程的更多详细信息，请参阅如何：设置 [*用户的用户访问权限* 部分](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) 中的 *操作方法：设置实例和身份验证 (CLI 或门户)*。

如果已使用此角色分配 *，并且* 使用 Azure AD 应用注册来验证客户端应用，则如果此解决方案未解决403问题，则可继续执行下一解决方案。

### <a name="solution-2"></a>解决方案 #2

如果使用 Azure AD 应用注册来验证客户端应用，则第二种可能的解决方案是验证应用注册是否具有为 Azure 数字孪生服务配置的权限。 如果未配置这些设置，请对其进行设置。

#### <a name="check-current-setup"></a>检查当前设置

若要检查权限是否已正确配置，请导航到 Azure 门户中的 " [Azure AD 应用注册概述" 页](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) 。 可以通过在门户搜索栏中搜索 *应用注册* 来自行访问此页。

切换到 " *所有应用程序* " 选项卡以查看已在订阅中创建的所有应用程序注册。

你应在列表中看到刚刚创建的应用注册。 选择它以打开其详细信息。

:::image type="content" source="media/troubleshoot-error-403/app-registrations.png" alt-text="Azure 门户中的应用注册页":::

首先，验证是否已正确设置注册上的 Azure 数字孪生权限设置。 为此，请从菜单栏中选择 " *清单* "，以查看应用注册的清单代码。 滚动到代码窗口的底部，在下查找这些字段 `requiredResourceAccess` 。 值应与以下屏幕截图中的值匹配：

:::image type="content" source="media/troubleshoot-error-403/verify-manifest.png" alt-text="Azure AD 应用注册清单的门户视图":::

接下来，从菜单栏中选择 " *API 权限* "，验证此应用注册是否包含 Azure 数字孪生的读/写权限。 应会看到如下所示的条目：

:::image type="content" source="media/troubleshoot-error-403/verify-api-permissions.png" alt-text="Azure AD 应用注册的 API 权限的门户视图，其中显示了 Azure 数字孪生的 &quot;读取/写入访问权限&quot;":::

#### <a name="fix-issues"></a>解决问题

如果任何此项的显示方式与所述有所不同，请按照 [*如何：创建应用注册*](how-to-create-app-registration.md)中的如何设置应用注册中的说明进行操作。

## <a name="next-steps"></a>后续步骤

阅读创建和验证新的 Azure 数字孪生实例的设置步骤：
* [*操作说明：设置实例和身份验证 (CLI)*](how-to-set-up-instance-cli.md)

阅读有关 Azure 数字孪生的安全和权限的详细信息：
* [*概念： Azure 数字孪生解决方案的安全性*](concepts-security.md)
