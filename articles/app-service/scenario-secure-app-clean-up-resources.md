---
title: 教程 - 清理资源 | Microsoft
description: 在本教程中，你将了解如何在创建 Web 应用时清理分配的 Azure 资源。
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: bf03fe9d920298d49e79a9a0febf7e09e94eb6ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905513"
---
# <a name="tutorial-clean-up-resources"></a>教程：清理资源

如果你完成了本教程（包含多个部分）中的所有步骤，那么就已在资源组中创建了应用服务、应用服务托管计划和存储帐户。 还在 Azure Active Directory 中创建了应用注册。 如果不再需要这些资源和应用注册，请将其删除，以免继续产生费用。

在本教程中，你将了解：

> [!div class="checklist"]
>
> * 删除按照本教程创建的 Azure 资源。

## <a name="delete-the-resource-group"></a>删除资源组

在 [Azure 门户](https://portal.azure.com)中，从门户菜单中选择“资源组”，然后选择包含应用服务和应用服务计划的资源组。

选择“删除资源组”，删除该资源组和所有资源。

:::image type="content" alt-text="显示如何删除资源组的屏幕截图。" source="./media/scenario-secure-app-clean-up-resources/delete-resource-group.png":::

此命令可能需要几分钟才能运行。

## <a name="delete-the-app-registration"></a>删除应用注册

从门户菜单中选择“Azure Active Directory” > “应用注册” 。 然后，选择创建的应用程序。
:::image type="content" alt-text="显示如何选择应用注册的屏幕截图。" source="./media/scenario-secure-app-clean-up-resources/select-app-registration.png":::

在应用注册概述中，选择“删除”。
:::image type="content" alt-text="显示如何删除应用注册的屏幕截图。" source="./media/scenario-secure-app-clean-up-resources/delete-app-registration.png":::

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
>
> * 删除按照本教程创建的 Azure 资源。

了解如何将 [.NET Core 应用](tutorial-dotnetcore-sqldb-app.md)、[Python 应用](tutorial-python-postgresql-app.md)、[Java 应用](tutorial-java-spring-cosmosdb.md)或 [Node.js 应用](tutorial-nodejs-mongodb-app.md)连接到数据库。