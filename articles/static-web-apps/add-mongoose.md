---
title: 教程：使用 Mongoose 通过 Azure Static Web Apps 访问 Cosmos DB 中的数据
description: 了解如何使用 Mongoose 从 Azure Static Web Apps API 函数访问 Cosmos DB 中的数据。
author: GeekTrainer
ms.author: chrhar
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 01/25/2021
ms.openlocfilehash: f64cc67ad6f0296ad289d858795ee783943f3daf
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259859"
---
# <a name="tutorial-access-data-in-cosmos-db-using-mongoose-with-azure-static-web-apps"></a>教程：使用 Mongoose 通过 Azure Static Web Apps 访问 Cosmos DB 中的数据

[Mongoose](https://mongoosejs.com/) 是 Node.js 最热门的 ODM（对象文档映射）客户端。 Mongoose 提供所需的所有工具与支持 Mongoose API 的数据库进行交互，使你能够设计数据结构并强制进行验证。 [Cosmos DB](../cosmos-db/mongodb-introduction.md) 支持必要的 Mongoose API，并可在 Azure 上用作后端服务器选项。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> - 创建 Cosmos DB 无服务器帐户
> - 创建 Azure Static Web Apps
> - 更新应用程序设置以存储连接字符串

如果没有 Azure 订阅，请创建一个[免费的试用帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

- 一个 [Azure 帐户](https://azure.microsoft.com/free/)
- 一个 [GitHub 帐户](https://github.com/join)

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-cosmos-db-serverless-database"></a>创建 Cosmos DB 无服务器数据库

首先创建一个 [Cosmos DB 无服务器](https://docs.microsoft.com/azure/cosmos-db/serverless)帐户。 利用无服务器帐户，你只需为使用的资源付费，而无需创建完整基础结构。

1. 导航到 [https://portal.azure.com](https://portal.azure.com)
2. 单击“创建资源”
3. 在搜索框中输入“Azure Cosmos DB”
4. 单击“Azure Cosmos DB”
5. 单击“创建” 
6. 为 Azure Cosmos DB 帐户配置以下信息
    - 订阅：选择要用的订阅
    - 资源：单击“新建”，并将名称设置为“aswa-mongoose” 
    - 帐户名：需要一个唯一值
    - API：“用于 MongoDB 的 Azure Cosmos DB”API
    - Notebooks（预览版）：关闭
    - 位置：美国西部 2
    - 容量模式：无服务器（预览版）
    - 版本：3.6
    - 可用性区域：禁用
:::image type="content" source="media/add-mongoose/cosmos-db.png" alt-text="新建 Cosmos DB 实例":::
7. 单击“查看 + 创建”
8. 单击“创建” 

该创建过程将花费几分钟时间。 后续步骤将返回到数据库以收集连接字符串。

## <a name="create-a-static-web-app"></a>创建静态 Web 应用

本教程使用 GitHub 模板存储库来帮助你创建应用程序。

1. 导航到[初学者模板](https://github.com/login?return_to=/staticwebdev/mongoose-starter/generate)
2. 选择“所有者”（如果使用组织而不是主帐户）
3. 将存储库命名为 aswa-mongoose-tutorial
4. 单击“从模板创建存储库”
5. 返回到 [Azure 门户](https://portal.azure.com)
6. 单击“创建资源”
7. 在搜索框中键入“静态 Web 应用”
8. 选择“静态 Web 应用(预览版)”
9. 单击“创建” 
10. 为 Azure 静态 Web 应用配置以下信息
    - 订阅：选择与之前相同的订阅
    - 资源组：选择 aswa-mongoose
    - 名称：aswa-mongoose-tutorial
    - 区域：美国西部 2
    - 单击“使用 GitHub 登录”
    - 如果系统提示允许 Azure Static Web Apps 创建 GitHub 操作以启用部署，则单击“授权”
    - 组织：你的帐户名称
    - 存储库：aswa-mongoose-tutorial
    - 分支：main
    - 生成预设：选择“自定义”
    - 应用位置：/public
    - API 位置：api
    - 应用项目位置：保留空白
    :::image type="content" source="media/add-mongoose/azure-static-web-apps.png" alt-text="已完成的 Azure Static Web Apps 窗体":::
11. 单击“查看并创建”
12. 单击“创建” 

创建过程需要几分钟的时间，但在预配应用后，可以单击“转到资源”。

## <a name="configure-database-connection-string"></a>配置数据库连接字符串

为了允许 Web 应用与数据库通信，数据库连接字符串以应用程序设置的形式存储。 使用 `process.env` 对象可在 Node.js 中访问设置值。

1. 单击 Azure 门户左上角的“主页”（或导航回 [https://portal.azure.com](https://portal.azure.com)）
2. 单击“资源组”
3. 单击“aswa-mongoose”
4. 单击数据库帐户的名称，其帐户类型将为 Azure Cosmos DB
5. 在“设置”下，单击“连接字符串” 
6. 复制“主要连接字符串”下列出的连接字符串
7. 在痕迹导航中，单击“aswa-mongoose”
8. 单击“aswa-mongoose-tutorial”，返回到网站实例
9. 在“设置”下，单击“配置” 
10. 单击“添加”，并使用以下值创建新应用程序设置
    - 名称：CONNECTION_STRING
    - 值：粘贴前面复制的连接字符串
11. 单击 **“确定”**
12. 单击“保存” 

## <a name="navigate-to-your-site"></a>导航到站点

现在可以浏览静态 Web 应用。

1. 单击“概述”
1. 单击右上角显示的 URL
    1. 该 URL 将类似于 `https://calm-pond-05fcdb.azurestaticapps.net`
1. 通过键入标题并单击“添加任务”来创建新任务
1. 确认任务已显示（可能需要一些时间）
1. 通过单击复选框，将任务标记为已完成
1. 刷新页面以确认正在使用数据库

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请按以下步骤删除资源组：

1. 返回到 [Azure 门户](https://portal.azure.com)
2. 单击“资源组”
3. 单击“aswa-mongoose”
4. 单击“删除资源组”
5. 在文本框中键入“aswa-mongoose”
6. 单击“删除”

## <a name="next-steps"></a>后续步骤

继续学习下一篇文章了解如何配置本地开发…
> [!div class="nextstepaction"]
> [设置本地开发](./local-development.md)
 
