---
title: 已知问题 - Azure 数字孪生
description: 获取有关识别和缓解 Azure 数字孪生已知问题的帮助。
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 09/15/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: 02a1a6d5088a722c0b919811afc0ac89c7c52550
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593565"
---
# <a name="known-issues-in-azure-digital-twins"></a>Azure 数字孪生中的已知问题

可在本文了解与 Azure 数字孪生相关的已知问题。

## <a name="400-client-error-bad-request-in-cloud-shell"></a>Cloud Shell 中的“400 客户端错误: 错误请求”

**问题描述：** 在 *https://shell.azure.com* 运行的 Cloud Shell 中的命令可能会间歇性失败，并出现错误“400 客户端错误: 对 URL http://localhost:50342/oauth2/token 的请求错误”，后面接完整的堆栈跟踪。

| 这是否会影响我？ | 原因 | 解决方法 |
| --- | --- | --- |
| 在&nbsp;Azure&nbsp;数字孪生中，此问题会影响以下命令组：<br><br>`az dt route`<br><br>`az dt model`<br><br>`az dt twin` | 这是 Cloud Shell 中的一个已知问题导致的：[从 Cloud Shell 获取令牌时会间歇性失败，出现“400 客户端错误: 错误请求”](https://github.com/Azure/azure-cli/issues/11749)。<br><br>这会影响 Azure 数字孪生实例身份验证令牌和 Cloud Shell 默认的基于[托管标识](../active-directory/managed-identities-azure-resources/overview.md)的身份验证。 <br><br>这不会影响来自 `az dt` 或 `az dt endpoint` 命令组的 Azure 数字孪生命令，原因是它们使用不同类型的身份验证令牌（基于 Azure 资源管理器），这不会对 Cloud Shell 的托管标识身份验证造成问题。 | 解决此问题的一种方法是在 Cloud Shell 中重新运行 `az login` 命令并完成后续登录步骤。 该操作将使会话脱离托管标识身份验证，从而避免了根本问题。 之后就能重新运行该命令。<br><br>或者，你可在 Azure 门户中打开 Cloud Shell 窗格，在这里完成 Cloud Shell 工作。<br>:::image type="content" source="media/troubleshoot-known-issues/portal-launch-icon.png" alt-text="Azure 门户图标栏中的 Cloud Shell 图标的屏幕截图。" lightbox="media/troubleshoot-known-issues/portal-launch-icon.png":::<br><br>最后，还有一种解决方案是在计算机上[安装 Azure CLI](/cli/azure/install-azure-cli)，以便可在本地运行 Azure CLI 命令。 本地 CLI 不会遇到此问题。 |

## <a name="issue-with-interactive-browser-authentication-on-azureidentity-120"></a>Azure.Identity 1.2.0 上的交互式浏览器身份验证存在问题

**问题描述：** 使用 [Azure.Identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true) 库的 1.2.0 版本在 Azure 数字孪生应用程序中编写验证码时，可能会遇到与 [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) 方法相关的问题。 当尝试在浏览器窗口中进行身份验证时，此问题将显示为错误响应“Azure.Identity.AuthenticationFailedException”。 浏览器窗口可能无法完全启动，或者貌似对用户的身份验证已成功，但客户端应用程序仍然失败并出现错误。

| 这是否会影响我？ | 原因 | 解决方法 |
| --- | --- | --- |
| 下列文章中使用了受影响的方法：&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br><br>[为客户端应用编写代码](tutorial-code.md)<br><br>[编写应用身份验证码](how-to-authenticate-client.md)<br><br>[Azure 数字孪生 API 和 SDK](concepts-apis-sdks.md) | 某些用户在 `Azure.Identity` 库的 1.2.0 版本中遇到了此问题。 | 若要解决它，请更新应用程序以使用 `Azure.Identity` 的[更高版本](https://www.nuget.org/packages/Azure.Identity)。 更新库版本后，浏览器应会按预期方式进行加载和身份验证。 |

## <a name="issue-with-default-azure-credential-authentication-on-azureidentity-130"></a>Azure.Identity 1.3.0 上的默认 Azure 凭据身份验证出现问题

**问题描述：** 使用 1.3.0 版 [Azure.Identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true) 库编写验证码时，某些用户在这些 Azure 数字孪生文档的许多示例中遇到了与 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) 方法相关的问题。当代码尝试进行身份验证时，此问题将显示为错误响应“Azure.Identity.AuthenticationFailedException: SharedTokenCacheCredential 身份验证失败”。

| 这是否会影响我？ | 原因 | 解决方法 |
| --- | --- | --- |
| 此服务包含身份验证的大多数文档示例中都使用了 `DefaultAzureCredential`。 如果使用 `DefaultAzureCredential` 和 1.3.0 版的 `Azure.Identity` 库编写验证码，并看到此错误消息，那么此问题会影响你。 | 这可能是由于 `Azure.Identity` 的某些配置问题导致的。 | 解决此问题的一种策略是从凭证中排除 `SharedTokenCacheCredential`，如 `Azure.Identity` 出现的当前待解决的 [DefaultAzureCredential 问题](https://github.com/Azure/azure-sdk/issues/1970)中所述。<br>还有一种方法是更改应用程序以使用更低版本的 `Azure.Identity`，例如[版本 1.2.3](https://www.nuget.org/packages/Azure.Identity/1.2.3)。 使用早期版本对 Azure 数字孪生没有功能上的影响，因此也是公认的解决方案。 |

## <a name="next-steps"></a>后续步骤

阅读有关 Azure 数字孪生的安全性和权限的详细信息：
* [Azure 数字孪生解决方案的安全性](concepts-security.md)