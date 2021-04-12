---
title: 使用客户管理的密钥或 BYOK REST API
description: 本教程会将客户管理的密钥或自带密钥 (BYOK) 与 Azure 媒体服务存储帐户配合使用。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c8a5b682e2ac4879d2181bdb069cf554bad512d9
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281084"
---
# <a name="tutorial-use-customer-managed-keys-or-byok-with-media-services-rest-api"></a>教程：将客户管理的密钥或 BYOK 与媒体服务 REST API 配合使用

借助 2020-05-01 API，可以将客户管理的 RSA 密钥与具有系统托管标识的 Azure 媒体服务帐户配合使用。本教程包括用于将 REST 请求发送到 Azure 服务的 Postman 集合和环境。 使用的服务是：

- Postman 的 Azure Active Directory (Azure AD) 应用程序注册
- Microsoft Graph API
- Azure 存储
- Azure Key Vault
- Azure 媒体服务

本教程将介绍如何使用 Postman 完成以下操作：

> [!div class="checklist"]
> - 获取用于 Azure 服务的令牌。
> - 创建资源组和存储帐户。
> - 创建包含系统托管标识的媒体服务帐户。
> - 创建用于存储客户管理的 RSA 密钥的密钥保管库。
> - 更新媒体服务帐户，以将 RSA 密钥与存储帐户配合使用。
> - 在 Postman 中使用变量。

如果没有 Azure 订阅，[请创建一个免费的试用帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

1. 注册具有适当权限的服务主体。
1. 安装 [Postman](https://www.postman.com)。
1. 通过 [Azure 示例：media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok) 为本教程下载 Postman 集合。

### <a name="register-a-service-principal-with-the-needed-permissions"></a>注册具有所需权限的服务主体

1. [创建服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)。
1. 转到[选项 2:创建新的应用程序机密](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)以获取服务主体机密。

   > [!IMPORTANT]
   >复制并保存机密以供之后使用。 离开门户中的机密页面后，将无法访问该机密。

1. 将权限分配给服务主体，如以下屏幕截图所示：

   :::image type="complex" source="./media/tutorial-byok/service-principal-permissions-1.png" alt-text="显示服务主体所需的权限的屏幕截图。":::
   权限按服务、权限名称、类型和说明列出。 Azure Key Vault：用户模拟、委派、完全访问 Azure Key Vault。 Azure 服务管理：用户模拟、委派、以组织用户身份访问 Azure 服务管理。 Azure 存储：用户模拟、委派、访问 Azure 存储。 媒体服务：用户模拟、委派、访问媒体服务。 Microsoft Graph：user.read、委派、登录和读取用户配置文件。
   :::image-end:::

### <a name="install-postman"></a>安装 Postman

如果尚未安装 Postman 以与 Azure 配合使用，可通过 [postman.com](https://www.postman.com/) 来获取它。

### <a name="download-and-import-the-collection"></a>下载并导入集合

通过 [Azure 示例：media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok) 为本教程下载 Postman 集合。

## <a name="install-the-postman-collection-and-environment"></a>安装 Postman 集合和环境

1. 运行 Postman。
1. 选择“导入”。
1. 选择“上传文件”。
1. 转到集合和环境文件的保存位置。
1. 选择集合和环境文件。
1. 选择“打开”  。 将出现一条警告，指出不会将文件作为 API 导入，而是作为集合导入。 出现此警告属于正常现象。 这正是你所希望的情况。

此集合现在以 BYOK 的形式显示在集合中。 此外，环境变量将显示在环境中。

### <a name="understand-the-rest-api-requests-in-the-collection"></a>了解集合中的 REST API 请求

集合提供以下 REST API 请求。

> [!NOTE]
>
>- 请求必须按提供的顺序发送。
>- 大多数请求都有测试脚本，这些脚本可动态地为序列中的下一个请求创建全局变量。
>- 无需手动创建全局变量。

在 Postman 中，这些变量包含在括号中。 例如 `{{bearerToken}}`。

1. 获取 Azure AD 令牌：测试设置全局变量 bearerToken。
2. 获取 Microsoft Graph 令牌：测试设置全局变量 graphToken。
3. 获取服务主体详细信息：测试设置全局变量 servicePrincipalObjectId。
4. 创建存储帐户：测试设置全局变量 storageAccountId。
5. 创建包含系统托管标识的媒体服务帐户：测试设置全局变量 principalId。
6. 创建一个密钥保管库，以向服务主体授予访问权限：测试设置全局变量 keyVaultId。
7. 获取 Key Vault 令牌：测试设置全局变量 keyVaultToken。
8. 在密钥保管库中创建 RSA 密钥：测试设置全局变量 keyId。
9. 更新媒体服务帐户，以将密钥与存储帐户配合使用：没有适用于此请求的测试脚本。

## <a name="define-environment-variables"></a>定义环境变量

1. 选择环境的下拉列表以切换到下载的环境。
1. 在 Postman 中建立环境变量。 它们还用作括号中包含的变量。 例如 `{{tenantId}}`。

    - **tenantId**：租户 ID。
    - **servicePrincipalId**：使用你最喜欢的方法（如门户或 CLI）建立的服务主体的 ID。
    - **servicePrincipalSecret**：为服务主体创建的机密。
    - **订阅**：订阅 ID。
    - **storageName**：要为你的存储提供的名称。
    - **accountName**：要使用的媒体服务帐户名称。
    - **keyVaultName**：要使用的密钥保管库名称。
    - **resourceLocation**：位置 CentralUs 或要放置资源的位置。 仅使用 CentralUs 对此集合进行了测试。
    - **resourceGroup**：资源组名称。

    以下变量是用于 Azure 资源的标准变量。 因此无需更改它们。

    - armResource：`https://management.core.windows.net`
    - graphResource：`https://graph.windows.net/`
    - keyVaultResource：`https://vault.azure.net`
    - armEndpoint：`management.azure.com`
    - graphEndpoint：`graph.windows.net`
    - aadEndpoint：`login.microsoftonline.com`
    - keyVaultDomainSuffix：`vault.azure.net`

## <a name="send-the-requests"></a>发送请求

定义环境变量后，可以在上一个序列中一次运行一个请求。 也可以使用 Postman 的运行程序来运行集合。

## <a name="change-the-key"></a>更改密钥

媒体服务自动检测密钥何时发生了更改。 请为同一密钥创建另一个密钥版本来对此进程进行测试。 媒体服务应会在 15 分钟内检测到此密钥。

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用所创建的资源，并且不想继续付费，请删除这些资源。

## <a name="next-steps"></a>后续步骤

转到下一篇文章，了解如何：
> [!div class="nextstepaction"]
> [基于 URL 对远程文件进行编码并使用 REST 流式传输视频](stream-files-tutorial-with-rest.md)