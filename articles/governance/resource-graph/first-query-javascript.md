---
title: 快速入门：你的第一个 JavaScript 查询
description: 本快速入门介绍为 JavaScript 启用 Resource Graph 库并运行第一个查询的步骤。
ms.date: 07/09/2021
ms.topic: quickstart
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 9dc5068232e6ad19715535ba8e41cff213e23311
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459956"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-javascript"></a>快速入门：使用 JavaScript 运行你的第一个 Resource Graph 查询

本快速入门将指导你完成将该库添加到 JavaScript 安装的过程。 使用 Azure Resource Graph 的第一步是使用所需的库初始化 JavaScript 应用程序。

在此过程结束时，你应已将该库添加到 JavaScript 安装中，并可运行你的第一个 Resource Graph 查询。

## <a name="prerequisites"></a>先决条件

- **Azure 订阅**：如果没有 Azure 订阅，请在开始之前创建一个 [免费](https://azure.microsoft.com/free/)帐户。

- **Node.js**：需要 [Node.js](https://nodejs.org/) 版本 12 或更高版本。

## <a name="application-initialization"></a>应用程序初始化

若要启用 JavaScript 以查询 Azure Resource Graph，必须配置环境。 此设置适用于可使用 JavaScript 的任何环境，包括 [Bash on Windows 10](/windows/wsl/install-win10)。

1. 运行以下命令，初始化新的 Node.js 项目。

   ```bash
   npm init -y
   ```

1. 添加对 yargs 模块的引用。

   ```bash
   npm install yargs
   ```

1. 添加对 Azure Resource Graph 模块的引用。

   ```bash
   npm install @azure/arm-resourcegraph
   ```

1. 添加对 Azure 身份验证库的引用。

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > 在 package.json 中验证 `@azure/arm-resourcegraph` 是否为版本 2.0.0 或更高版本，并且 `@azure/ms-rest-nodeauth` 是否为版本 3.0.3 或更高版本 。

## <a name="query-the-resource-graph"></a>查询 Resource Graph

1. 创建一个名为 index.js 的文件，并输入以下代码。

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const resourceGraph = require("@azure/arm-resourcegraph");

   if (argv.query) {
       const query = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new resourceGraph.ResourceGraphClient(credentials);
          const result = await client.resources(
             {
                 query: argv.query
             },
             { resultFormat: "table" }
          );
          console.log("Records: " + result.totalRecords);
          console.log(result.data);
       };

       query();
   }
   ```

   > [!NOTE]
   > 此代码创建基于租户的查询。 要将查询限制为[管理组](../management-groups/overview.md)或订阅，定义 [queryrequest](/javascript/api/@azure/arm-resourcegraph/queryrequest) 并将其添加到 `client.resources` 调用，然后指定 `managementGroups` 或 `subscriptions`。

1. 在终端中输入以下命令：

   ```bash
   node index.js --query "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > 由于此查询示例未提供排序修饰符（例如 `order by`），因此多次运行此查询可能会为每个请求生成一组不同的资源。

1. 将第一个参数更改为 `index.js`，并将查询更改为 `order by` Name 属性。

   ```bash
   node index.js --query "Resources | project name, type | limit 5 | order by name asc"
   ```

   当脚本尝试进行身份验证时，会在终端中显示类似于以下所示的消息：

   > 若要登录，请使用 Web 浏览器打开页面 https://microsoft.com/devicelogin ，然后输入代码 FGB56WJUGK 进行身份验证。

   在浏览器中进行身份验证后，该脚本将继续运行。

   > [!NOTE]
   > 与第一个查询一样，多次运行此查询可能会为每个请求生成一组不同的资源。 查询命令的顺序非常重要。 在本例中，`order by` 位于 `limit` 之后。 命令按此顺序执行，首先会限制查询结果，然后对它们进行排序。

1. 将第一个参数更改为 `index.js`，并将查询更改为先 `order by` Name 属性，然后对前五个结果应用 `limit`。

   ```bash
   node index.js --query "Resources | project name, type | order by name asc | limit 5"
   ```

假设环境中没有任何变化，则多次运行最后一个查询时，返回的结果将是一致的且按 Name 属性排序，但仍限制为前五个结果。

## <a name="clean-up-resources"></a>清理资源

如果希望从应用程序中删除已安装的库，请运行以下命令。

```bash
npm uninstall @azure/arm-resourcegraph @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何将 Resource Graph 库添加到 JavaScript 环境并运行第一个查询。 若要详细了解 Resource Graph 语言，请继续阅读查询语言详细信息页。

> [!div class="nextstepaction"]
> [获取有关查询语言的详细信息](./concepts/query-language.md)
