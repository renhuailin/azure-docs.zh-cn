---
title: 参数化 Azure 数据工厂中的链接服务
description: 了解如何参数化 Azure 数据工厂中的链接服务，并在运行时传递动态值。
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/18/2021
author: chez-charlie
ms.author: chez
ms.openlocfilehash: 48e8c9ff5111b6b67e835676e3bc56e4536248a0
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110091461"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>参数化 Azure 数据工厂中的链接服务

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

现在可以参数化链接服务并在运行时传递动态值。 例如，如果要连接到同一逻辑 SQL Server 上的不同数据库，则现在可以在链接服务定义中参数化数据库名称。 这样就不需为逻辑 SQL Server 上的每个数据库创建链接服务。 也可以参数化链接服务定义中的其他属性 - 例如，用户名。

可以使用 Azure 门户中的数据工厂 UI 或编程接口来参数化链接服务。

> [!TIP]
> 我们建议不要参数化密码或机密。 而应将所有连接字符串都存储在 Azure Key Vault 中，并参数化 *机密名称*。

> [!Note]
> 存在导致在参数名中使用“-”的待解决的 bug，在该 bug 被解决之前，建议使用不带“-”的名称。

有关此功能的 7 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-linked-service-types"></a>支持的链接服务类型

可以将任何类型的链接服务参数化。
在 UI 上创作链接服务时，数据工厂会为以下类型的链接服务提供内置参数化体验。 在“链接服务创建/编辑”边栏选项卡中，可以找到新参数的选项并添加动态内容。

- Amazon Redshift
- Amazon S3
- Amazon S3 兼容存储
- Azure Blob 存储
- Azure Cosmos DB (SQL API)
- Azure Data Lake Storage Gen2
- Azure Database for MySQL
- Azure Databricks
- Azure Key Vault
- Azure SQL 数据库
- Azure SQL 托管实例
- Azure Synapse Analytics 
- Azure 表存储
- 泛型 HTTP
- 泛型 REST
- MySQL
- Oracle
- Oracle 云存储
- SQL Server

对于不在上述列表中的其他链接服务类型，可以通过在 UI 上编辑 JSON 来参数化该链接服务：

- 在“链接服务创建/编辑”边栏选项卡中 -> 展开底部的“高级”-> 选中“以 JSON 格式指定动态内容”复选框 -> 指定链接服务 JSON 有效负载。 
- 或者，在创建没有参数化的链接服务后，转到[管理中心](author-visually.md#management-hub) ->“链接服务”-> 查找特定的链接服务 -> 单击“代码”（“{}”按钮）以编辑 JSON。 

请参考 [JSON 示例](#json)来添加 ` parameters` 节，以定义参数并使用 ` @{linkedService().paraName} ` 来引用参数。

## <a name="data-factory-ui"></a>数据工厂 UI

![将动态内容添加到“链接服务”定义](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![新建参数](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
