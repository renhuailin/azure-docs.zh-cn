---
title: 使用 Visual Studio 连接到 Azure 德国 | Microsoft Docs
description: 有关使用 Visual Studio 管理 Azure 德国中的订阅的信息
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: f5749ed9d2080a92a33993d8131595359110b75e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029084"
---
# <a name="connect-to-azure-germany-by-using-visual-studio"></a>使用 Visual Studio 连接到 Azure 德国

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

开发人员通过使用 Visual Studio 可以轻松管理 Azure 订阅，并构建解决方案。 目前，在 Visual Studio 用户界面中，你无法配置与 Azure 德国的连接。

## <a name="visual-studio-2017-and-visual-studio-2019"></a>Visual Studio 2017 和 Visual Studio 2019

Visual Studio 需要使用配置文件方能连接到 Azure 德国。 此文件就位后，Visual Studio 即会连接到 Azure 德国，而不是全球 Azure。

### <a name="create-a-configuration-file-for-azure-germany"></a>为 Azure 德国创建配置文件

使用以下内容创建名为 AadProvider.Configuration.json 的文件：

```json
{
  "AuthenticationQueryParameters":null,
  "AsmEndPoint":"https://management.microsoftazure.de/",
  "Authority":"https://login.microsoftonline.de/",
  "AzureResourceManagementEndpoint":"https://management.microsoftazure.de/",
  "AzureResourceManagementAudienceEndpoints":["https://management.core.cloudapi.de/"],
  "ClientIdentifier":"872cd9fa-d31f-45e0-9eab-6e460a02d1f1",
  "EnvironmentName":"BlackForest",
  "GraphEndpoint":"https://graph.cloudapi.de",
  "MsaHomeTenantId":"f577cd82-810c-43f9-a1f6-0cc532871050",
  "NativeClientRedirect":"urn:ietf:wg:oauth:2.0:oob",
  "PortalEndpoint":"https://portal.core.cloudapi.de/",
  "ResourceEndpoint":"https://management.microsoftazure.de/",
  "ValidateAuthority":true,
  "VisualStudioOnlineEndpoint":"https://app.vssps.visualstudio.com/",
  "VisualStudioOnlineAudience":"499b84ac-1321-427f-aa17-267ca6975798"
}
```

### <a name="update-visual-studio-for-azure-germany"></a>更新 Azure 德国的 Visual Studio

1. 关闭 Visual Studio。
1. 将 AadProvider.Configuration.json 文件放置在 %localappdata%\\.IdentityService\AadConfigurations 文件夹。 如果此文件夹不存在，请创建此文件夹。
1. 启动 Visual Studio，然后开始使用 Azure 德国帐户。

> [!NOTE]
> 使用配置文件时，只能访问 Azure 德国订阅。 虽然仍可以看到以前配置的订阅，但这些订阅并不可用，因为 Visual Studio 现已连接到 Azure 德国，而不是全球 Azure。 若要连接到全球 Azure，请删除该配置文件。
>

### <a name="revert-a-visual-studio-connection-to-azure-germany"></a>还原连接到 Azure 德国的 Visual Studio

若要让 Visual Studio 连接到全球 Azure，需要删除允许连接到 Azure 德国的配置文件。

1. 关闭 Visual Studio。
1. 删除或者重新命名 %localappdata%\.IdentityService\AadConfigurations 文件夹。
1. 重启 Visual Studio 并开始使用全球 Azure 帐户。

> [!NOTE]
> 还原此配置后，无法继续访问 Azure 德国订阅。
>

## <a name="visual-studio-2015"></a>Visual Studio 2015

Visual Studio 2015 需要更改注册表以连接到 Azure 德国。 设置此注册表项后，Visual Studio 即会连接到 Azure 德国，而不是全球 Azure。

### <a name="update-visual-studio-2015-for-azure-germany"></a>更新 Visual Studio 2015 以连接到 Azure 德国

若要让 Visual Studio 连接到 Azure 德国，需要更新注册表。

1. 关闭 Visual Studio。
1. 创建名为 VisualStudioForAzureGermany.reg 的文本文件。
1. 将以下文本复制并粘贴到 VisualStudioForAzureGermany.reg 文件：

```
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
"AadInstance"="https://login.microsoftonline.de/"
"adaluri"="https://management.microsoftazure.de"
"AzureRMEndpoint"="https://management.microsoftazure.de"
"AzureRMAudienceEndpoint"="https://management.core.cloudapi.de"
"EnableAzureRMIdentity"="true"
"GraphUrl"="graph.cloudapi.de"
"AadApplicationTenant"="f577cd82-810c-43f9-a1f6-0cc532871050"
```

1. 保存文件，然后双击运行该文件。 系统会提示将文件合并到注册表。
1. 启动 Visual Studio，然后通过 Azure 德国帐户开始使用 [Cloud Explorer](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer)。

> [!NOTE]
> 设置此注册表项后，只能访问 Azure 德国订阅。 虽然仍可以看到以前配置的订阅，但这些订阅并不可用，因为 Visual Studio 现已连接到 Azure 德国，而不是全球 Azure。 若要连接到全局 Azure，请还原更改。
>

### <a name="revert-a-visual-studio-2015-connection-to-azure-germany"></a>还原连接到 Azure 德国的 Visual Studio 2015

若要让 Visual Studio 连接到全球 Azure，需要删除允许连接到 Azure 德国的注册表设置。

1. 关闭 Visual Studio。
1. 创建名为 VisualStudioForAzureGermany_Remove.reg 的文本文件。
1. 将以下文本复制并粘贴到 VisualStudioForAzureGermany_Remove.reg 文件：

```
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
"AadInstance"=-
"adaluri"=-
"AzureRMEndpoint"=-
"AzureRMAudienceEndpoint"=-
"EnableAzureRMIdentity"=-
"GraphUrl"=-
```

1. 保存文件，然后双击运行该文件。 系统会提示将文件合并到注册表。
1. 启动 Visual Studio。

> [!NOTE]
> 还原此注册表项后，Azure 德国订阅即会显示出来，但不可访问。 你可以安全地删除 Azure 德国订阅。
>

## <a name="next-steps"></a>后续步骤

有关连接到 Azure 德国的详细信息，请参阅以下资源：

* [使用 PowerShell 连接到 Azure 德国](./germany-get-started-connect-with-ps.md)
* [使用 Azure CLI 连接到 Azure 德国](./germany-get-started-connect-with-cli.md)
* [使用 Azure 门户连接到 Azure 德国](./germany-get-started-connect-with-portal.md)