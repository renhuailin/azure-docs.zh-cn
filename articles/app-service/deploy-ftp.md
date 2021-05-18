---
title: 使用 FTP/S 部署内容
description: 了解如何使用 FTP 或 FTPS 将应用部署到 Azure 应用服务。 通过禁用未加密的 FTP 来提高网站安全性。
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 02/26/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: fd856ee47c1100292f7558bb0fa2a1772951a3cb
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832362"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>使用 FTP/S 将应用部署到 Azure 应用服务

本文介绍了如何使用 FTP 或 FTPS 将 Web 应用、移动应用后端或 API 应用部署到 [Azure App Service](./overview.md)。

应用的 FTP/S 终结点已处于活动状态。 启用 FTP/S 部署不需要进行任何配置。

> [!NOTE]
> Azure 门户中的“开发中心(经典)”页面（旧部署体验）将于 2021 年 3 月弃用。 此更改不会影响应用中的任何现有部署设置，你可以继续在“部署中心”页中管理应用部署。

## <a name="get-deployment-credentials"></a>获取部署凭据

1. 按照[配置 Azure 应用服务的部署凭据](deploy-configure-credentials.md)中的说明，复制应用程序范围的凭据或设置用户范围的凭据。 使用任一凭据都可以连接到应用的 FTP/S 终结点。

1. 根据所选的凭据范围，按以下格式创建 FTP 用户名：

    | 应用程序范围 | 用户范围 |
    | - | - |
    |`<app-name>\$<app-name>`|`<app-name>\<deployment-user>`|

    ---

    在应用服务中，多个应用间共享 FTP/S 终结点。 由于用户范围的凭据未链接到特定资源，因此需要在应用名称前面预置用户范围的用户名，如上所示。

## <a name="get-ftps-endpoint"></a>获取 FTP/S 终结点
    
# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

还是在应用中复制部署凭据的同一管理页（“部署中心” > “FTP 凭据”）上，复制 **FTPS 终结点**。

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

运行 [az webapp deployment list-publishing-profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) 命令。 下面的示例使用 [JMES 路径](https://jmespath.org/)从输出中提取 FTP/S 终结点。

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app-name> --resource-group <group-name> --query "[?ends_with(profileName, 'FTP')].{profileName: profileName, publishUrl: publishUrl}"
```

每个应用都有两个 FTP/S 终结点，一个支持读写，另一个是只读（`profileName` 包含 `ReadOnly`），用于数据恢复方案。 若要使用 FTP 部署文件，请复制读写终结点的 URL。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

运行 [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) 命令。 下面的示例是从 XML 输出中提取 FTP/S 终结点。

```azurepowershell-interactive
$xml = [xml](Get-AzWebAppPublishingProfile -Name <app-name> -ResourceGroupName <group-name> -OutputFile null)
$xml.SelectNodes("//publishProfile[@publishMethod=`"FTP`"]/@publishUrl").value
```

-----

## <a name="deploy-files-to-azure"></a>将文件部署到 Azure

1. 从 FTP 客户端（例如 [Visual Studio](https://www.visualstudio.com/vs/community/)、[Cyberduck](https://cyberduck.io/) 或 [WinSCP](https://winscp.net/index.php)），使用收集到的连接信息连接到应用。
2. 将文件及其各自的目录结构复制到 Azure 中的 [/site/wwwroot 目录](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)（对于 Web 作业，复制到 /site/wwwroot/App_Data/Jobs/ 目录） 。
3. 浏览到应用的 URL，以验证该应用是否正在正常运行。 

> [!NOTE] 
> 与[基于 Git 的部署](deploy-local-git.md)和 [Zip 部署](deploy-zip.md)不同，FTP 部署不支持生成自动化，例如： 
>
> - 还原依赖项（如 NuGet、NPM、PIP 和 Composer 自动化）
> - 编译 .NET 二进制文件
> - 生成 web.config（以下提供一个 [Node.js 示例](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps)）
> 
> 在本地计算机上手动生成这些必要的文件，并将它们与应用一起部署。
>

## <a name="enforce-ftps"></a>强制实施 FTPS

为了增强安全性，只应允许基于 TLS/SSL 的 FTP。 如果不使用 FTP 部署，也可禁用 FTP 和 FTPS。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

1. 在 [Azure 门户](https://portal.azure.com)的应用资源页中，从左侧导航中选择“配置” > “常规设置”。

2. 若要禁用未加密的 FTP，请在“FTP 状态”中选择“仅 FTPS”。 若要完全禁用 FTP 和 FTPS，请选择“禁用”。 完成后，单击“保存”。 如果使用“仅 FTPS”，则必须通过导航到 Web 应用的“TLS/SSL 设置”边栏选项卡来强制实施 TLS 1.2 或更高版本。 TLS 1.0 和 1.1 不支持“仅 FTPS”。

    ![禁用 FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

使用 `--ftps-state` 参数运行 [az webapp config set](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) 命令。

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <group-name> --ftps-state FtpsOnly
```

`--ftps-state` 的可能值有 `AllAllowed`（FTP 和 FTPS 已启用时）、`Disabled`（FTP 和 FTPS 已禁用时）和 `FtpsOnly`（仅 FTPS 时）。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

使用 `-FtpsState` 参数运行 [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) 命令。

```azurepowershell-interactive
Set-AzWebApp -Name <app-name> -ResourceGroupName <group-name> -FtpsState FtpsOnly
```

`--ftps-state` 的可能值有 `AllAllowed`（FTP 和 FTPS 已启用时）、`Disabled`（FTP 和 FTPS 已禁用时）和 `FtpsOnly`（仅 FTPS 时）。

-----

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>排查 FTP 部署问题

- [如何排查 FTP 部署问题？](#how-can-i-troubleshoot-ftp-deployment)
- [我无法通过 FTP 来发布代码。如何解决此问题？](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [如何在 Azure 应用服务中通过被动模式连接到 FTP？](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

#### <a name="how-can-i-troubleshoot-ftp-deployment"></a>如何排查 FTP 部署问题？

若要排查 FTP 部署问题，第一步是厘清部署问题和运行时应用程序问题。

部署问题通常会导致无文件部署到应用，或者部署到应用的文件错误。 可以通过调查 FTP 部署情况，或者选择备用部署路径（例如源代码管理）来进行故障排除。

出现运行时应用程序问题时，通常部署到应用的文件集是正确的，但应用行为不正确。 可以通过重点查看运行时的代码行为，并调查具体的故障路径来进行故障排除。

若要确定问题是部署问题还是运行时问题，请参阅 [Deployment vs. runtime issues](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)（部署问题和运行时问题）。

#### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>我无法通过 FTP 来发布代码。 如何解决此问题？
检查是否输入了正确的[主机名](#get-ftps-endpoint)和[凭据](#get-deployment-credentials)。 另请检查计算机上的以下 FTP 端口是否未被防火墙阻止：

- FTP 控制连接端口：21、990
- FTP 数据连接端口：989、10001-10300
 
#### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>如何在 Azure 应用服务中通过被动模式连接到 FTP？
Azure 应用服务支持通过“主动”模式和“被动”模式进行连接。 首选“被动”模式，因为部署计算机通常位于防火墙后面（不管是在操作系统中，还是在家庭网络或企业网络中）。 请参阅 [WinSCP 文档中的示例](https://winscp.net/docs/ui_login_connection)。 

## <a name="more-resources"></a>更多资源

* [从本地 Git 部署到 Azure 应用服务](deploy-local-git.md)
* [ 部署凭据](deploy-configure-credentials.md)
* [示例：使用 FTP (Azure CLI) 创建 Web 应用并部署文件](./scripts/cli-deploy-ftp.md)。
* [示例：使用 FTP (PowerShell) 将文件上传到 Web 应用](./scripts/powershell-deploy-ftp.md)。
