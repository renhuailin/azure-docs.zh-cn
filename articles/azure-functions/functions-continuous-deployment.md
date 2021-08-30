---
title: Azure Functions 的连续部署
description: 使用 Azure 应用服务连续部署功能来发布函数。
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: 0018f1c74708035206a8a2bb9a540f00f98f43a4
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112321012"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions 的连续部署

通过[源代码管理集成](functions-deployment-technologies.md#source-control)，可以使用 Azure Functions 连续部署代码。 源代码管理集成可实现一个工作流，其中会通过代码更新触发 Azure 上的部署。 如果不熟悉 Azure Functions，可通过查看 [Azure Functions 概述](functions-overview.md)来入门。

连续部署选项非常适合需要整合频繁提供的多项贡献内容的项目。 使用连续部署时，可以为代码维护单一数据源，这样可便于团队轻松进行协作。 可以从以下源代码位置配置 Azure Functions 中的连续部署：

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Azure 中用于函数部署的单位是函数应用。 将同时部署函数应用中的所有函数。 启用连续部署后，在 Azure 门户中将函数代码访问权限配置为“只读”，因为数据源设置为其他位置。

## <a name="requirements-for-continuous-deployment"></a>连续部署要求

若要成功执行连续部署，目录结构须与 Azure Functions 预期的基本文件夹结构兼容。

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> 消耗计划中运行的 Linux 应用目前尚不支持连续部署。 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>设置连续部署

若要为现有函数应用配置连续部署，请完成以下步骤。 这些步骤演示了与 GitHub 存储库的集成，与 Azure Repos 或其他源代码存储库的集成与此类似。

1. 在 [Azure 门户](https://portal.azure.com)的函数应用中，依次选择“部署中心”、“GitHub”和“授权”  。 如果已为 GitHub 授权，请选择“继续”，然后跳过下一步。 

    :::image type="content" source="./media/functions-continuous-deployment/github.png" alt-text="Azure 应用服务部署中心":::

3. 在 GitHub 中，选择“授权 AzureAppService”。

    :::image type="content" source="./media/functions-continuous-deployment/authorize.png" alt-text="授权 Azure 应用服务":::

    输入 GitHub 密码，然后选择“继续”。

4. 选择以下生成提供程序之一：

    * **应用服务生成服务**：最适合在不需要生成或需要泛型生成的情况下选择。
    * **Azure Pipelines（预览版）** ：最适合在需要更好地控制生成的情况下选择。 此提供程序当前为预览版。

    选择“继续”。

5. 配置特定于指定的源代码管理选项的信息。 对于 GitHub，需要为“组织”、“存储库”和“分支”输入或选择值  。 这些值基于代码位置。 然后选择“继续”。

    :::image type="content" source="./media/functions-continuous-deployment/github-specifics.png" alt-text="配置 GitHub":::

6. 查看所有详细信息，然后选择“完成”以完成部署配置。

完成此过程后，指定源中的所有代码都将部署到你的应用中。 此时，部署源中的更改会触发将这些更改部署到 Azure 中的函数应用的操作。

> [!NOTE]
> 配置持续集成后，就无法再对 Functions 门户中的源文件进行编辑了。 如果最初是从本地计算机发布代码，可能需要将函数应用中的 `WEBSITE_RUN_FROM_PACKAGE` 设置更改为值 `0`。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Functions 最佳实践](functions-best-practices.md)
