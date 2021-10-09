---
title: include 文件
description: include 文件
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 09/07/2021
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 9d33df05e54d9cdeb97631ca479dd8ac9869343c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "126057027"
---
1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在左侧菜单上选择“所有服务”  ，然后在“移动”部分中选择“通知中心”。   选择服务名称旁边的星形图标，以便将服务添加到左侧菜单上的“收藏夹”部分。  在将“通知中心”  添加到“收藏夹”  之后，在左侧菜单上选择它。

      ![Azure 门户 - 选择“通知中心”](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. 在“通知中心”页面上，在工具栏上选择“创建”。

      ![通知中心 -“添加”工具栏按钮](./media/notification-hubs-portal-create-new-hub/create-toolbar-button.png)

1. 在“通知中心”页面上的“基本信息”选项卡中，执行以下步骤：

    1. 在“订阅”中，选择要使用的 Azure 订阅的名称，然后选择现有资源组或创建新资源组。  

    1. 在“命名空间详细信息”中为新命名空间输入唯一名称。 
    
    1. 命名空间包含一个或多个通知中心，因此请在“通知中心详细信息”中键入中心的名称。 或从下拉列表中选择一个现有的命名空间。

    1. 从“位置”下拉列表框中选择值。  此值指定要在其中创建通知中心的位置。

    1. 选择“创建”  。

        ![Azure 门户 - 设置通知中心属性](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. 选择“通知”（钟形图标），然后选择“转到资源”   。 还可以刷新“通知中心”  页上的列表，然后选择通知中心。

      ![Azure 门户 - 转到资源](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. 从列表中选择“访问策略”  。 记下向你提供的两个连接字符串。 稍后在处理推送通知时需要它们。

      >[!IMPORTANT]
      >请勿  在应用程序中使用 **DefaultFullSharedAccessSignature** 策略。 这只能在后端使用。
      >

      ![Azure 门户 - 通知中心连接字符串](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
