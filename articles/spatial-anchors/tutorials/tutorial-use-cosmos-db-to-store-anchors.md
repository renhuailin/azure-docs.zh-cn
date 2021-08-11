---
title: 教程：使用 Azure Cosmos DB 共享定位点
description: 在本教程中，你会了解如何使用后端服务和 Azure Cosmos DB 在 Unity 中的 Android/iOS 设备间共享 Azure 空间定位点标识符。
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: c7e25529803b99adb4ce3a375f041b989dda3fe3
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "113225599"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>教程：使用 Azure Cosmos DB 后端在会话和设备间共享 Azure 空间定位点

本教程是[在会话和设备之间共享 Azure 空间定位点的续篇。](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md) 本教程将指导你完成添加更多功能的过程，从而使 Azure Cosmos DB 充当后端存储，同时在会话和设备之间共享 Azure 空间定位点。

![演示对象持久性的 GIF](./media/persistence.gif)

值得注意到，虽然在本教程中会使用 Unity 和 Azure Cosmos DB，不过这只是为了提供有关如何在设备间共享空间定位点标识符的示例。 可以使用其他语言和后端技术实现相同目的。

## <a name="create-a-database-account"></a>创建数据库帐户

将 Azure Cosmos 数据库添加到之前创建的资源组。

[!INCLUDE [cosmos-db-create-dbaccount-table](../../cosmos-db/includes/cosmos-db-create-dbaccount-table.md)]

复制 `Connection String`，因为你会需要它。

## <a name="make-minor-changes-to-the-sharingservice-files"></a>对 SharingService 文件做一些小修改

在“解决方案资源管理器”中打开 `SharingService\Startup.cs`。

找到文件顶部的 `#define INMEMORY_DEMO`，然后将该行注释掉。保存文件。

在“解决方案资源管理器”  中打开 `SharingService\appsettings.json`。

找到 `StorageConnectionString` 属性，将值设置为与在[创建数据库帐户步骤](#create-a-database-account)中复制的 `Connection String` 值相同。 保存文件。

你可重新发布共享服务并运行示例应用。

## <a name="next-steps"></a>后续步骤

在本教程中，使用了 Azure Cosmos DB 在设备间共享定位点标识符。 若要详细了解如何在新的 Unity HoloLens 应用中使用 Azure 空间定位点，请继续学习下一教程。

> [!div class="nextstepaction"]
> [启动新的 Unity HoloLens 应用](./tutorial-new-unity-hololens-app.md)
