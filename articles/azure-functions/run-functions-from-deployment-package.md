---
title: 从包运行 Azure Functions
description: 通过装载包含函数应用项目文件的部署包文件，让 Azure Functions 运行时运行函数。
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: 0be037d5a9270d60c16f8fc128030705be8b81ef
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113136879"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>从包文件运行 Azure Functions

在 Azure 中，可以直接从函数应用中的部署包文件运行函数。 另一种做法是在函数应用的 `d:\home\site\wwwroot` 目录中部署文件。

本文介绍从包运行函数的好处。 此外，介绍如何在函数应用中启用此功能。

## <a name="benefits-of-running-from-a-package-file"></a>从包文件运行的好处
  
从包文件运行函数可提供多种好处：

+ 减少文件副本锁定问题的风险。
+ 可部署到生产应用（需重启）。
+ 可以确定哪些文件在应用中运行。
+ 提高 [Azure 资源管理器部署](functions-infrastructure-as-code.md)的性能。
+ 可以减少冷启动时间，特别是对于具有大型 npm 包树的 JavaScript 函数。

有关详细信息，请参阅[此公告](https://github.com/Azure/app-service-announcements/issues/84)。

## <a name="enabling-functions-to-run-from-a-package"></a>使函数从包运行

若要使函数应用从包运行，只需将 `WEBSITE_RUN_FROM_PACKAGE` 设置添加到函数应用设置。 `WEBSITE_RUN_FROM_PACKAGE` 设置可以使用以下值之一：

| 值  | 说明  |
|---------|---------|
| **`1`**  | 建议用于在 Windows 上运行的函数应用。 从函数应用的 `d:\home\data\SitePackages` 文件夹中的某个包文件运行。 如果不[使用 zip deploy 进行部署](#integration-with-zip-deployment)，则此选项要求该文件夹同时包含名为 `packagename.txt` 的文件。 此文件仅包含文件夹中包文件的名称（没有任何空白字符）。 |
|**`<URL>`**  | 要运行的特定包文件的位置。 指定 URL 时，还必须在发布更新的包后[同步触发器](functions-deployment-technologies.md#trigger-syncing)。 <br/>使用 Blob 存储时，通常不应使用公共 Blob， 而应使用带有[共享访问签名 (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) 的专用容器，或者[使用托管标识](#fetch-a-package-from-azure-blob-storage-using-a-managed-identity)，以便允许 Functions 运行时来访问包。 可以使用 [Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)将包文件上传到 Blob 存储帐户。 |

> [!CAUTION]
> 在 Windows 上运行函数应用时，外部 URL 选项会导致糟糕的冷启动性能。 将函数应用部署到 Windows 时，应将 `WEBSITE_RUN_FROM_PACKAGE` 设置为 `1` 并通过 zip 部署进行发布。

下面显示了配置为从 Azure Blob 存储中托管的 .zip 文件运行的函数应用：

![WEBSITE_RUN_FROM_ZIP 应用设置](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> 目前仅支持 .zip 包文件。

### <a name="fetch-a-package-from-azure-blob-storage-using-a-managed-identity"></a>使用托管标识从 Azure Blob 存储提取包

[!INCLUDE [Run from package via Identity](../../includes/app-service-run-from-package-via-identity.md)]

## <a name="integration-with-zip-deployment"></a>与 zip 部署集成

[Zip 部署][Zip deployment for Azure Functions]是 Azure 应用服务的一项功能，可用于将函数应用项目部署到 `wwwroot` 目录。 项目打包为 .zip 部署文件。 可以使用相同的 API 将包部署到 `d:\home\data\SitePackages` 文件夹。 对 `WEBSITE_RUN_FROM_PACKAGE` 应用设置使用值 `1` 时，zip 部署 API 会将包复制到 `d:\home\data\SitePackages` 文件夹，而不是将文件提取到 `d:\home\site\wwwroot`。 它还会创建 `packagename.txt` 文件。 重启后，该包将作为只读文件系统装载到 `wwwroot`。 有关 zip 部署的详细信息，请参阅 [Azure Functions 的 Zip 部署](deployment-zip-push.md)。

> [!NOTE]
> 进行部署时，将触发函数应用的重启。 在重启之前，所有现有函数执行被允许完成或超时。若要了解详细信息，请参阅[部署行为](functions-deployment-technologies.md#deployment-behaviors)。

## <a name="adding-the-website_run_from_package-setting"></a>添加 WEBSITE_RUN_FROM_PACKAGE 设置

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="troubleshooting"></a>故障排除

- Run From Package 将 `wwwroot` 设为只读，因此在将文件写入此目录时将收到一个错误。
- 不支持 Tar 和 gzip 格式。
- ZIP 文件最大可为 1GB。
- 此功能不与本地缓存组合。
- 若要提高冷启动性能，请使用本地 Zip 选项 (`WEBSITE_RUN_FROM_PACKAGE`=1)。
- “从包运行”与部署自定义选项 (`SCM_DO_BUILD_DURING_DEPLOYMENT=true`) 不兼容，在部署期间将忽略生成步骤。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Functions 的连续部署](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
