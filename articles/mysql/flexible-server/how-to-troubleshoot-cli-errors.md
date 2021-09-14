---
title: 排查 Azure Database for MySQL 灵活服务器 CLI 错误
description: 本主题指导排查使用 MySQL 灵活服务器时 Azure CLI 的常见问题。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 08/24/2021
ms.openlocfilehash: eae5f1fe6a44cedafb00e10c9995905b993ca7b9
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439738"
---
# <a name="troubleshoot-azure-database-for-mysql-flexible-server-cli-errors"></a>排查 Azure Database for MySQL 灵活服务器 CLI 错误
[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

本文档将帮助你排查使用 MySQL 灵活服务器时 Azure CLI 的常见问题。

## <a name="command-not-found"></a>找不到命令

 如果收到错误提示命令“拼写错误或系统无法识别”。 这可能意味着客户端计算机上的 CLI 版本可能不是最新的。 运行 ```az upgrade``` 以升级到最新版本。 升级 CLI 版本有助于解决因任何 API 更改而导致的命令不兼容问题。

 
## <a name="debug-deployment-failures"></a>调试部署失败 
目前，Azure CLI 不支持启用调试日志记录，但可以通过以下步骤来检索调试日志记录。

>[!NOTE]
> - 将 ```examplegroup``` 和 ```exampledeployment``` 替换为数据库服务器的正确资源组和部署名称。 
> - 可以在资源组的部署页中查看部署名称。 请参阅[了解如何查找部署名称](../../azure-resource-manager/templates/deployment-history.md?tabs=azure-portal)。

1. 列出资源组中的部署以标识 MySQL 服务器部署 
    ```azurecli

        az deployment operation group list \
          --resource-group examplegroup \
          --name exampledeployment
    ```

2. 获取 MySQL 服务器部署的请求内容 
    ```azurecli

        az deployment operation group list \
          --name exampledeployment \
          -g examplegroup \
          --query [].properties.request
    ```
3. 检查响应内容 
    ```azurecli
    az deployment operation group list \
      --name exampledeployment \
      -g examplegroup \
      --query [].properties.response
    ```

## <a name="error-codes"></a>错误代码

| 错误代码 | 缓解措施 |
| ---------- | ---------- | 
|MissingSubscriptionRegistration|向资源提供程序注册订阅。 运行 ```az provider register --namespace Microsoft.DBMySQL``` 命令以解决此问题。|
|InternalServerError| 尝试查看服务器的活动日志，以确认是否有详细信息。 运行命令 ```az monitor activity-log list --correlation-id <enter correlation-id>```。 几分钟后，可以尝试相同的 CLI 命令。 如果问题仍然存在，请[报告问题](https://github.com/Azure/azure-cli/issues)或联系 Microsoft 支持人员。|
|ResourceNotFound| 找不到引用的资源。  可以检查资源属性，或者检查资源是否被删除，或者检查资源是否为其他订阅。 |
|LocationNotAvailableForResourceType| - 检查 [Azure 区域](https://azure.microsoft.com/global-infrastructure/services/?products=mysql)中 Azure Database for MySQL 灵活服务器的可用性。 <br>- 检查 Azure DB for MySQL 资源类型是否已注册到你的订阅。 |
|ResourceGroupBeingDeleted| 正在删除资源组。 等待删除操作完成。|
|PasswordTooLong| 提供的密码过长。 该密码必须包含 8 到 128 个字符。 密码必须包含以下三个类别的字符：英文大写字母、英文小写字母、数字 (0 到 9)和非字母数字字符（!, $, #, % 等）。|
|PasswordNotComplex| 提供的密码不够复杂。  该密码必须包含 8 到 128 个字符。 密码必须包含以下三个类别的字符：英文大写字母、英文小写字母、数字 (0 到 9)和非字母数字字符（!, $, #, % 等）。|
|PasswordTooShort| 该密码必须包含 8 到 128 个字符。 密码必须包含以下三个类别的字符：英文大写字母、英文小写字母、数字 (0 到 9)和非字母数字字符（!, $, #, % 等）。|
|SubscriptionNotFound| 找不到所请求的订阅。 运行 ```az account list all``` 以查看所有当前订阅。|
|InvalidParameterValue| 为参数提供的值无效。请检查 [CLI 参考文档](/cli/azure/mysql/flexible-server?view=azure-cli-latest&preserve-view=true)以了解参数支持的正确值。|
|InvalidLocation| 指定的位置无效。 检查 [Azure 区域](https://azure.microsoft.com/global-infrastructure/services/?products=mysql)中 Azure Database for MySQL 灵活服务器的可用性 |
|InvalidServerName| 标识了无效的服务器名称。 检查服务器名称。 运行命令 [az mysql flexible-server list](/cli/azure/mysql/flexible-server?view=azure-cli-latest&preserve-view=true#az_mysql_flexible_server_list) 以查看可用灵活服务器的所有列表。 |
|InvalidResourceIdSegment| Azure 资源管理器模板中标识了语法错误。 使用 JSON 格式化程序工具验证 JSON 以识别语法错误。|
|InvalidUserName| 输入有效的用户名。 管理员用户名不能是 azure_superuser、azure_pg_admin、admin、administrator、root、guest 或 public。 且不能以 pg_ 开头。|
|BlockedUserName| 管理员用户名不能是 azure_superuser、azure_pg_admin、admin、administrator、root、guest 或 public。 且不能以 pg_ 开头。 避免在管理员名称中使用这些模式。|

## <a name="next-steps"></a>后续步骤

- 如果仍遇到问题，请[报告问题](https://github.com/Azure/azure-cli/issues)。 
- 如果有疑问，请访问我们的 Stack Overflow 页面： https://aka.ms/azcli/questions 。 
- 请通过 https://aka.ms/azureclihats 告知我们你对于本次简短调查的满意程度。 
