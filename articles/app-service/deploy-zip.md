---
title: 将文件部署到应用服务
description: 了解如何将各种应用包或者离散的库、静态文件或启动脚本部署到 Azure 应用服务
ms.topic: article
ms.date: 08/13/2021
ms.reviewer: sisirap
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: a73d03d97e2b5d5d743b1db3980c8e587a0f454b
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225440"
---
# <a name="deploy-files-to-app-service"></a>将文件部署到应用服务

本文介绍如何将代码作为 ZIP、WAR、JAR 或 EAR 包部署到 [Azure 应用服务](overview.md)。 此外，还介绍如何将各个文件独立于应用程序包部署到应用服务。

## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，请[创建一个应用服务应用](./index.yml)，或者使用为其他教程创建的应用。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="deploy-a-zip-package"></a>部署 ZIP 包

部署 ZIP 包时，应用服务会将其内容解压缩到应用的默认路径（在 Windows 中为 `D:\home\site\wwwroot`，在 Linux 中为 `/home/site/wwwroot`）。

此 ZIP 包部署使用为基于持续集成的部署提供支持的同一 Kudu 服务。 Kudu 支持使用以下功能进行 ZIP 包部署： 

- 删除先前的部署留下的文件。
- 启用默认生成过程的选项，包括包还原。
- 部署自定义，包括运行部署脚本。  
- 部署日志。 
- 包大小上限为 2048 MB。

有关详细信息，请参阅 [Kudu 文档](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)。

> [!NOTE]
> 仅当 ZIP 包中文件的时间戳与已部署的文件不匹配时，才会复制该包中的文件。 使用缓存输出的生成过程生成 zip 可以使部署速度更快。 有关详细信息，请参阅[从 zip 文件或 url 部署](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)。

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

使用 [az webapp deploy](/cli/azure/webapp#az_webapp_deploy) 命令将 ZIP 包部署到 Web 应用。 该 CLI 命令使用 [Kudu 发布 API](#kudu-publish-api-reference) 部署文件，并且完全可自定义。

以下示例将 ZIP 包推送到站点。 为 `--src-path` 指定本地 ZIP 包的路径。

```azurecli-interactive
az webapp deploy --resource-group <group-name> --name <app-name> --src-path <zip-package-path>
```

部署 ZIP 包后，此命令将重启应用。 

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

以下示例使用 `--src-url` 参数指定站点应从中拉取 ZIP 的 Azure 存储帐户的 URL。

```azurecli-interactive
az webapp deploy --resource-group <grou-name> --name <app-name> --src-url "https://storagesample.blob.core.windows.net/sample-container/myapp.zip?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

以下示例使用 [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) 上传 ZIP 包。 替换占位符 `<group-name>`、`<app-name>` 和 `<zip-package-path>`。

```powershell
Publish-AzWebApp -ResourceGroupName Default-Web-WestUS -Name MyApp -ArchivePath <zip-package-path> 
```

# <a name="kudu-api"></a>[Kudu API](#tab/api)

以下示例使用 cURL 工具部署 ZIP 包。 替换占位符 `<username>`、`<zip-package-path>` 和 `<app-name>`。 出现 cURL 的提示时，请键入[部署密码](deploy-configure-credentials.md)。

```bash
curl -X POST -u <username> --data-binary @"<zip-package-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=zip
```

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

以下示例使用 `packageUri` 参数指定 Web 应用应从中拉取 ZIP 的 Azure 存储帐户的 URL。

```bash
curl -X POST -u <username> https://<app-name>.scm.azurewebsites.net/api/publish -d '{"packageUri": "https://storagesample.blob.core.windows.net/sample-container/myapp.zip?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3"}'
```

# <a name="kudu-ui"></a>[Kudu UI](#tab/kudu-ui)

在浏览器中，导航到 `https://<app_name>.scm.azurewebsites.net/ZipDeployUI`。

通过将文件拖动到网页中的文件资源管理器区域，上传你在[创建项目 ZIP 包](#create-a-project-zip-package)中创建的 ZIP 包。

部署正在进行的时候，右上角的图标会以百分比形式显示进度。 该页还在资源管理器区域下方显示操作的详细消息。 完成后，最后一个部署消息会显示：`Deployment successful`。

上面的终结点目前不适用于 Linux 应用服务。 请考虑改用 FTP 或 [ZIP 部署 API](/azure/app-service/faq-app-service-linux#continuous-integration-and-deployment)。

-----

## <a name="enable-build-automation-for-zip-deploy"></a>为 ZIP 部署启用生成自动化

默认情况下，部署引擎假设 ZIP 包已准备好按原样运行，并且不运行任何生成自动化。 若要启用与 [Git 部署](deploy-local-git.md)中相同的生成自动化，请通过在 [Cloud Shell](https://shell.azure.com) 中运行以下命令来设置 `SCM_DO_BUILD_DURING_DEPLOYMENT` 应用设置：

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

有关详细信息，请参阅 [Kudu 文档](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)。

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="deploy-warjarear-packages"></a>部署 WAR/JAR/EAR 包

可将 [WAR](https://wikipedia.org/wiki/WAR_(file_format))、[JAR](https://wikipedia.org/wiki/JAR_(file_format)) 或 [EAR](https://wikipedia.org/wiki/EAR_(file_format)) 包部署到应用服务，以使用 Azure CLI、PowerShell 或 Kudu 发布 API 运行 Java Web 应用。

部署过程会正确地将包放置在共享文件驱动器上（请参阅 [Kudu 发布 API 参考](#kudu-publish-api-reference)）。 因此，不建议使用 [FTP](deploy-ftp.md) 或 WebDeploy 部署 WAR/JAR/EAR 包。

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

使用 [az webapp deploy](/cli/azure/webapp#az_webapp_deploy) 命令将 WAR 包部署到 Tomcat 或 JBoss EAP。 为 `--src-path` 指定本地 Java 包的路径。

```azurecli-interactive
az webapp deploy --resource-group <group-name> --name <app-name> --src-path ./<package-name>.war
```

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

以下示例使用 `--src-url` 参数指定 Web 应用应从中拉取 ZIP 的 Azure 存储帐户的 URL。

```azurecli-interactive
az webapp deploy --resource-group <grou-name> --name <app-name> --src-url "https://storagesample.blob.core.windows.net/sample-container/myapp.war?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3
```

该 CLI 命令使用 [Kudu 发布 API](#kudu-publish-api-reference) 来部署包，并且完全可自定义。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

以下示例使用 [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) 上传 .war 文件。 请替换占位符 `<group-name>`、`<app-name>` 和 `<package-path>`（Azure PowerShell 仅支持 WAR 和 JAR 文件）。

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <package-path>
```

# <a name="kudu-api"></a>[Kudu API](#tab/api)

以下示例使用 cURL 工具部署 .war、.jar 或 .ear 文件。 请替换占位符 `<username>`、`<file-path>`、`<app-name>` 和 `<package-type>`（相应地替换为 `war`、`jar` 或 `ear`）。 出现 cURL 的提示时，请键入[部署密码](deploy-configure-credentials.md)。

```bash
curl -X POST -u <username> --data-binary @"<file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=<package-type>
```

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

以下示例使用 `packageUri` 参数指定 Web 应用应从中拉取 WAR 的 Azure 存储帐户的 URL。 除 WAR 外，该文件也可以是 JAR 或 EAR 文件。

```bash
curl -X POST -u <username> https://<app-name>.scm.azurewebsites.net/api/publish -d '{"packageUri": "https://storagesample.blob.core.windows.net/sample-container/myapp.war?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3"}'
```

有关详细信息，请参阅 [Kudu 发布 API 参考](#kudu-publish-api-reference)

# <a name="kudu-ui"></a>[Kudu UI](#tab/kudu-ui)

Kudu UI 不支持部署 JAR、WAR 或 EAR 应用程序。 请使用其他某个选项。

-----

## <a name="deploy-individual-files"></a>部署单个文件

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

将 [az webapp deploy](/cli/azure/webapp#az_webapp_deploy) 命令与 `--type` 参数配合使用，将启动脚本、库和静态文件部署到 Web 应用。

如果以这种方式部署启动脚本，应用服务会自动使用你的脚本来启动应用。

该 CLI 命令使用 [Kudu 发布 API](#kudu-publish-api-reference) 部署文件，并且完全可自定义。

### <a name="deploy-a-startup-script"></a>部署启动脚本

```bash
az webapp deploy --resource group <group-name> --name <app-name> --src-path scripts/startup.sh --type=startup
```

### <a name="deploy-a-library-file"></a>部署库文件

```bash
az webapp deploy --resource group <group-name> --name <app-name> --src-path driver.jar --type=lib
```

### <a name="deploy-a-static-file"></a>部署静态文件

```bash
az webapp deploy --resource group <group-name> --name <app-name> --src-path config.json --type=static
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

不支持。 请参阅“Azure CLI”或“Kudu API”。

# <a name="kudu-api"></a>[Kudu API](#tab/api)

### <a name="deploy-a-startup-script"></a>部署启动脚本

以下示例使用 cURL 工具为应用程序部署启动文件。请替换占位符 `<username>`、`<startup-file-path>` 和 `<app-name>`。 出现 cURL 的提示时，请键入[部署密码](deploy-configure-credentials.md)。

```bash
curl -X POST -u <username> --data-binary @"<startup-file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=startup
```

### <a name="deploy-a-library-file"></a>部署库文件

以下示例使用 cURL 工具为应用程序部署库文件。 替换占位符 `<username>`、`<lib-file-path>` 和 `<app-name>`。 出现 cURL 的提示时，请键入[部署密码](deploy-configure-credentials.md)。

```bash
curl -X POST -u <username> --data-binary @"<lib-file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=lib&path="/home/site/deployments/tools/my-lib.jar"
```

### <a name="deploy-a-static-file"></a>部署静态文件

以下示例使用 cURL 工具为应用程序部署配置文件。 替换占位符 `<username>`、`<config-file-path>` 和 `<app-name>`。 出现 cURL 的提示时，请键入[部署密码](deploy-configure-credentials.md)。

```bash
curl -X POST -u <username> --data-binary @"<config-file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=static&path="/home/site/deployments/tools/my-config.json"
```

# <a name="kudu-ui"></a>[Kudu UI](#tab/kudu-ui)

Kudu UI 不支持部署单个文件。 请使用 Azure CLI 或 Kudu REST API。

-----

## <a name="kudu-publish-api-reference"></a>Kudu 发布 API 参考

`publish` Kudu API 允许指定 CLI 命令中所用的相同参数作为 URL 查询参数。 若要使用 Kudu API 进行身份验证，可以将应用的[部署凭据](deploy-configure-credentials.md#userscope)与基本身份验证配合使用。

下表显示了可用的查询参数及其允许的值和说明。

| 键 | 允许的值 | 说明 | 必需 | 类型  |
|-|-|-|-|-|
| `type` | `war`\|`jar`\|`ear`\|`lib`\|`startup`\|`static`\|`zip` | 所部署的项目的类型，此参数设置默认目标路径，并告知 Web 应用应如何处理部署。 <br/> - `type=zip`：通过将内容解压缩到 `/home/site/wwwroot` 来部署 ZIP 包。 `path` 参数是可选的。 <br/> - `type=war`：部署 WAR 包。 默认情况下，WAR 包将部署到 `/home/site/wwwroot/app.war`。 可以使用 `path` 指定目标路径。 <br/> - `type=jar`：将 JAR 包部署到 `/home/site/wwwroot/app.jar`。 将忽略 `path` 参数 <br/> - `type=ear`：将 EAR 包部署到 `/home/site/wwwroot/app.ear`。 将忽略 `path` 参数 <br/> - `type=lib`：部署 JAR 库文件。 默认情况下，该文件将部署到 `/home/site/libs`。 可以使用 `path` 指定目标路径。 <br/> - `type=static`：部署静态文件（例如脚本）。 默认情况下，该文件将部署到 `/home/site/scripts`。 可以使用 `path` 指定目标路径。 <br/> - `type=startup`：部署由应用服务自动用作应用启动脚本的脚本。 默认情况下，该脚本将部署到 `D:\home\site\scripts\<name-of-source>`（在 Windows 中）或 `home/site/wwwroot/startup.sh`（在 Linux 中）。 可以使用 `path` 指定目标路径。 | 是 | 字符串 |
| `restart` | `true`\|`false` | 默认情况下，在完成部署操作后，API 将重启应用 (`restart=true`)。 若要部署多个项目，请设置 `restart=false`，防止在除最后一个部署外的其他所有部署上重启。 | 否 | 布尔 |
| `clean` | `true`\|`false` | 指定在目标部署中部署项目之前是否清理（删除）目标部署。 | 否 | 布尔 |
| `ignorestack` | `true`\|`false` | 发布 API 使用 `WEBSITE_STACK` 环境变量根据站点的语言堆栈选择安全默认值。 将此参数设置为 `false` 会禁用任何特定于语言的默认值。 | 否 | 布尔 |
| `path` | `"<absolute-path>"` | 要将项目部署到的绝对路径。 例如：`"/home/site/deployments/tools/driver.jar"`、`"/home/site/scripts/helper.sh"`。 | 否 | String |

## <a name="next-steps"></a>后续步骤

有关更高级的部署方案，请参阅[使用 Git 部署到 Azure](deploy-local-git.md)。 通过基于 Git 的 Azure 部署可实现版本控制、包还原、MSBuild 等功能。

## <a name="more-resources"></a>更多资源

* [Kudu：从 zip 文件部署](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [ 部署凭据](deploy-ftp.md)
* [环境变量和应用设置参考](reference-app-settings.md)
