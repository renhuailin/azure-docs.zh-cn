---
title: 使用 Azure Cosmos DB 的 API for MongoDB 将 CRUD 函数添加到 Angular 应用
description: 本教程系列的第 6 部分，介绍如何通过 Angular 和 Node 在 Azure Cosmos DB 上创建 MongoDB 应用，所使用的 API 与用于 MongoDB 的 API 完全相同
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: 301f4bb3bf91037b992255e036b6d7d6e4dd5d4b
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029753"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---add-crud-functions-to-the-app"></a>使用 Azure Cosmos DB 的用于 MongoDB 的 API 创建 Angular 应用 - 将 CRUD 函数添加至应用
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

本教程包含多个部分，演示了如何通过 Express 和 Angular 创建以 Node.js 编写的新应用，然后将其连接到[使用 Cosmos DB 的用于 MongoDB 的 API 配置的 Cosmos 帐户](mongodb-introduction.md)。 本教程的第 6 部分基于[第 5 部分](tutorial-develop-nodejs-part-5.md)，涵盖以下任务：

> [!div class="checklist"]
> * 为 hero 服务创建 Post、Put 和 Delete 函数
> * 运行应用

> [!VIDEO https://www.youtube.com/embed/Y5mdAlFGZjc]

## <a name="prerequisites"></a>先决条件

开始教程的此部分之前，请确保已完成教程[第 5 部分](tutorial-develop-nodejs-part-5.md)的步骤。

> [!TIP]
> 本教程介绍生成应用程序的各个步骤。 若要下载完成的项目，可从 GitHub 上的 [angular-cosmosdb 存储库](https://github.com/Azure-Samples/angular-cosmosdb)获取完成的应用程序。

## <a name="add-a-post-function-to-the-hero-service"></a>向 hero 服务添加 Post 函数

1. 在 Visual Studio Code 中按“拆分编辑器”按钮 :::image type="icon" source="./media/tutorial-develop-nodejs-part-6/split-editor-button.png"::: 并排打开 routes.js 和 hero.service.js  。

    可以看到，routes.js 第 7 行调用的是 hero.service.js 中第 5 行的 `getHeroes` 函数。  需为 post、put 和 delete 函数进行与此相同的配对。 

    :::image type="content" source="./media/tutorial-develop-nodejs-part-6/routes-heroservicejs.png" alt-text="Visual Studio Code 中的 routes.js 和 hero.service.js":::
    
    首先，请对 hero 服务进行编码。 

2. 将以下代码复制到 hero.service.js 中 `module.exports` 函数之前 `getHeroes` 函数之后的位置。 此代码：  
   * 使用 hero 模型发布新的 hero。
   * 查看响应，了解是否存在错误，然后返回状态值 500。

   ```javascript
   function postHero(req, res) {
     const originalHero = { uid: req.body.uid, name: req.body.name, saying: req.body.saying };
     const hero = new Hero(originalHero);
     hero.save(error => {
       if (checkServerError(res, error)) return;
       res.status(201).json(hero);
       console.log('Hero created successfully!');
     });
   }

   function checkServerError(res, error) {
     if (error) {
       res.status(500).send(error);
       return error;
     }
   }
   ```

3. 在 hero.service.js 中更新 `module.exports`，使之包括新的 `postHero` 函数。 

    ```javascript
    module.exports = {
      getHeroes,
      postHero
    };
    ```

4. 在 routes.js 中的 `get` 路由器之后为 `post` 函数添加一个路由器。 此路由器一次发布一个 hero。 以这种方式安排路由器文件的结构可以清楚地显示所有可用的 API 终结点，将真正的工作留给 hero.service.js 文件。

    ```javascript
    router.post('/hero', (req, res) => {
      heroService.postHero(req, res);
    });
    ```

5. 运行应用，看是否一切正常。 在 Visual Studio Code 中，保存所有更改，选择左侧的“调试”按钮 :::image type="icon" source="./media/tutorial-develop-nodejs-part-6/debug-button.png":::，然后选择“启动调试”按钮 :::image type="icon" source="./media/tutorial-develop-nodejs-part-6/start-debugging-button.png"::: 。

6. 现在请返回到 Internet 浏览器，打开开发人员工具的“网络”选项卡（在大多数计算机上按 F12 即可）。 导航到 `http://localhost:3000`，观察通过网络进行的调用。

    :::image type="content" source="./media/tutorial-develop-nodejs-part-6/add-new-hero.png" alt-text="Chrome 中的“网络”选项卡，显示网络活动":::

7. 通过选择“添加新 Hero”按钮添加新的 hero。 输入“999”作为 ID，“Fred”作为 name，“Hello”作为 saying，然后选择“保存”。 此时会在“网络”选项卡中看到为新的 hero 发送了 POST 请求。 

    :::image type="content" source="./media/tutorial-develop-nodejs-part-6/post-new-hero.png" alt-text="Chrome 中的“网络”选项卡，显示 Get 和 Post 函数的网络活动":::

    现在，让我们回过头来向应用添加 Put 和 Delete 函数。

## <a name="add-the-put-and-delete-functions"></a>添加 Put 和 Delete 函数

1. 在 routes.js 中的 post 路由器后添加 `put` 和 `delete` 路由器。

    ```javascript
    router.put('/hero/:uid', (req, res) => {
      heroService.putHero(req, res);
    });

    router.delete('/hero/:uid', (req, res) => {
      heroService.deleteHero(req, res);
    });
    ```

2. 将以下代码复制到 hero.service.js 中 `checkServerError` 函数之后的位置。 此代码：
   * 创建 `put` 和 `delete` 函数
   * 查看是否已找到 hero
   * 进行错误处理 

   ```javascript
   function putHero(req, res) {
     const originalHero = {
       uid: parseInt(req.params.uid, 10),
       name: req.body.name,
       saying: req.body.saying
     };
     Hero.findOne({ uid: originalHero.uid }, (error, hero) => {
       if (checkServerError(res, error)) return;
       if (!checkFound(res, hero)) return;

      hero.name = originalHero.name;
       hero.saying = originalHero.saying;
       hero.save(error => {
         if (checkServerError(res, error)) return;
         res.status(200).json(hero);
         console.log('Hero updated successfully!');
       });
     });
   }

   function deleteHero(req, res) {
     const uid = parseInt(req.params.uid, 10);
     Hero.findOneAndRemove({ uid: uid })
       .then(hero => {
         if (!checkFound(res, hero)) return;
         res.status(200).json(hero);
         console.log('Hero deleted successfully!');
       })
       .catch(error => {
         if (checkServerError(res, error)) return;
       });
   }

   function checkFound(res, hero) {
     if (!hero) {
       res.status(404).send('Hero not found.');
       return;
     }
     return hero;
   }
   ```

3. 在 hero.service.js 中导出新模块：

   ```javascript
    module.exports = {
      getHeroes,
      postHero,
      putHero,
      deleteHero
    };
    ```

4. 更新了代码后，选择 Visual Studio Code 中的“重启”按钮 :::image type="icon" source="./media/tutorial-develop-nodejs-part-6/restart-debugger-button.png":::。

5. 刷新 Internet 浏览器中的页面，选择“添加新 Hero”按钮。 添加 ID 为“9”、name 为“Starlord”、saying 为“Hi”的新 hero。 选择“保存”按钮保存新 hero。

6. 现在请选择 Starlord hero，将 saying 从“Hi”更改为“Bye”，然后选择“保存”按钮。 

    现在可以在“网络”选项卡中选择 ID 来显示有效负载了。 可以在有效负载中看到，saying 现在已设置为“Bye”。

    :::image type="content" source="./media/tutorial-develop-nodejs-part-6/put-hero-function.png" alt-text="Heroes 应用和“网络”选项卡，显示有效负载"::: 

    还可以在 UI 中删除某个 hero，看完成删除操作需要多少时间。 尝试时，可针对名为“Fred”的 hero 选择“删除”按钮。

    :::image type="content" source="./media/tutorial-develop-nodejs-part-6/times.png" alt-text="Heroes 应用和“网络”选项卡，显示完成函数操作的时间"::: 

    如果刷新页面，“网络”选项卡会显示获取 hero 的时间。 虽然这里显示的时间很短，但很多情况下，具体时间取决于数据在全球所处的位置，以及能否在靠近用户的区域进行异地复制。 可以在即将推出的下一教程中详细了解异地复制。

## <a name="next-steps"></a>后续步骤

在本教程的此部分，你已完成以下操作：

> [!div class="checklist"]
> * 向应用添加 Post、Put 和 Delete 函数 

本教程系列会添加更多的视频，请稍后回来观看。

尝试为迁移到 Azure Cosmos DB 进行容量规划？ 可以使用有关现有数据库群集的信息进行容量规划。
* 若只知道现有数据库群集中的 vcore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](../convert-vcore-to-request-unit.md) 
* 若知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-capacity-planner.md)

