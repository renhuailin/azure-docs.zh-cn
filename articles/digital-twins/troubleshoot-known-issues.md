---
title: 已知问题-Azure 数字孪生
description: 获取帮助识别和缓解 Azure 数字孪生的已知问题。
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: d7d97ca1eb590fb96789d439243dd04d6143a960
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203141"
---
# <a name="known-issues-in-azure-digital-twins"></a>Azure 数字孪生中的已知问题

本文介绍与 Azure 数字孪生相关的已知问题。

## <a name="400-client-error-bad-request-in-cloud-shell"></a>Cloud Shell 中的 "400 客户端错误：错误的请求"

**问题说明：** 在上运行 Cloud Shell 中 *https://shell.azure.com* 的命令可能会间歇性地失败，并出现错误 "400 客户端错误： url 的错误请求： http://localhost:50342/oauth2/token "，后跟完整堆栈跟踪。

| 这是否会影响我？ | 原因 | 解决方法 |
| --- | --- | --- |
| 在 &nbsp; Azure &nbsp; 数字 &nbsp; 孪生中，这会影响以下命令组：<br><br>`az dt route`<br><br>`az dt model`<br><br>`az dt twin` | 这是 Cloud Shell 中的已知问题的结果： [*从 Cloud Shell 中获取令牌间歇性失败，并出现400客户端错误：错误的请求*](https://github.com/Azure/azure-cli/issues/11749)。<br><br>这会给 Azure 数字孪生实例身份验证令牌和 Cloud Shell 默认的基于 [管理身份](../active-directory/managed-identities-azure-resources/overview.md) 的身份验证提供问题。 <br><br>这不会影响或命令组中的 Azure 数字孪生命令 `az dt` `az dt endpoint` ，因为它们使用不同类型的身份验证令牌 (基于 Azure 资源管理器) ，这不会对 Cloud Shell 的托管标识身份验证产生问题。 | 解决此问题的一种方法是 `az login` 在 Cloud Shell 中重新运行该命令并完成后续的登录步骤。 这会将会话切换到托管标识身份验证，从而避免了根本问题。 此后，你应该能够重新运行该命令。<br><br>或者，您可以打开 Azure 门户中的 "Cloud Shell" 窗格，并从该处完成 Cloud Shell 工作。<br>:::image type="content" source="media/troubleshoot-known-issues/portal-launch-icon.png" alt-text="Azure 门户图标栏中的 &quot;Cloud Shell&quot; 图标的图像" lightbox="media/troubleshoot-known-issues/portal-launch-icon.png":::<br><br>最后，另一个解决方案是在您的计算机上 [安装 Azure CLI](/cli/azure/install-azure-cli) ，以便您可以在本地运行 Azure CLI 命令。 本地 CLI 不会遇到此问题。 |


## <a name="missing-role-assignment-after-scripted-setup"></a>编写脚本后缺少角色分配

**问题说明：** 在 [*操作方法：设置实例和身份验证 (脚本)*](how-to-set-up-instance-scripted.md)时，某些用户可能会遇到有关角色分配部分的问题。 此脚本不表示失败，但 *Azure 数字孪生数据所有者* 角色未成功分配给用户，此问题将影响在路上创建其他资源的能力。

| 这是否会影响我？ | 原因 | 解决方法 |
| --- | --- | --- |
| 若要确定运行脚本后是否成功设置了角色分配，请按照安装程序一文中的 [*验证用户角色分配*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) 部分中的说明进行操作。 如果用户未显示此角色，此问题会影响你。 | 对于使用个人 [Microsoft 帐户 (MSA) ](https://account.microsoft.com/account)登录的用户，用户的主体 ID （用于标识类似于此的命令可能不同于用户的登录电子邮件），这使得脚本难以发现并使用来正确分配角色。 | 若要解决此问题，可以使用 [CLI 说明](how-to-set-up-instance-cli.md#set-up-user-access-permissions) 或 [Azure 门户说明](how-to-set-up-instance-portal.md#set-up-user-access-permissions)手动设置角色分配。 |

## <a name="issue-with-interactive-browser-authentication-on-azureidentity-120"></a>Azure 上的交互式浏览器身份验证问题。标识1.2。0

**问题说明：** 使用 1.2.0 **[](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)库** 的版本在 azure 数字孪生应用程序中编写身份验证代码时，可能会遇到 [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true)方法问题。 尝试在浏览器窗口中进行身份验证时，这会显示为 "AuthenticationFailedException" 的错误响应。 浏览器窗口可能无法完全启动，或者似乎已成功对用户进行身份验证，而客户端应用程序仍然失败并出现错误。

| 这是否会影响我？ | 原因 | 解决方法 |
| --- | --- | --- |
| &nbsp;受影响 &nbsp; 的 &nbsp; 方法 &nbsp; &nbsp; 在 &nbsp; &nbsp; 以下文章中使用：<br><br>[*教程：* 为客户端应用编写代码](tutorial-code.md)<br><br>[*操作说明：编写应用身份验证代码*](how-to-authenticate-client.md)<br><br>[*操作说明：使用 Azure 数字孪生 Api 和 Sdk*](how-to-use-apis-sdks.md) | 某些用户对库的版本 **1.2.0** 存在此问题 `Azure.Identity` 。 | 若要解决此问题，请更新应用程序以使用的 [更高版本](https://www.nuget.org/packages/Azure.Identity) `Azure.Identity` 。 更新库版本后，浏览器应按照预期方式进行加载和身份验证。 |

## <a name="issue-with-default-azure-credential-authentication-on-azureidentity-130"></a>Azure 上默认 Azure 凭据身份验证的问题。标识1.3。0

**问题说明：** 当使用 **[azure](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)** 的 **1.3.0** 版本编写身份验证代码时，某些用户曾遇到过许多示例中使用的 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet?view=azure-dotnet&preserve-view=true)方法的问题。当代码尝试进行身份验证时，此消息显示为 "AuthenticationFailedException： SharedTokenCacheCredential authentication failed" 的错误响应。

| 这是否会影响我？ | 原因 | 解决方法 |
| --- | --- | --- |
| `DefaultAzureCredential` 此服务的大多数文档示例中均使用此服务，其中包括身份验证。 如果使用的是1.3.0 的库版本编写身份验证代码， `DefaultAzureCredential` `Azure.Identity` 并看到此错误消息，这会影响你。 | 这可能是由的一些配置问题导致的 `Azure.Identity` 。 | 解决此问题的一个策略是 `SharedTokenCacheCredential` 从凭据中排除，如当前打开的此 [DefaultAzureCredential 问题](https://github.com/Azure/azure-sdk/issues/1970) 中所述 `Azure.Identity` 。<br>另一种方法是将应用程序更改为使用的早期版本 `Azure.Identity` ，如 [版本 1.2.3](https://www.nuget.org/packages/Azure.Identity/1.2.3)。 这对 Azure 数字孪生不起作用，因此也是已接受的解决方案。 |

## <a name="next-steps"></a>后续步骤

阅读有关 Azure 数字孪生的安全和权限的详细信息：
* [*概念： Azure 数字孪生解决方案的安全性*](concepts-security.md)