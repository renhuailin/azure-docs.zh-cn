---
title: 在 Azure 通知中心配置 Microsoft 推送通知服务 | Microsoft Docs
description: 了解如何为 Azure 通知中心配置 Microsoft 推送通知服务设置。
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 08/23/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: c2b46d3f4831ed98e54a47d8daa77107a625e362
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770894"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>在 Azure 门户中配置 Microsoft 推送通知服务 (MPNS) 设置

> [!NOTE]
> Microsoft 推送通知服务 (MPNS) 已弃用，不再受支持。

本文介绍如何使用 Azure 门户为 Azure 通知中心配置 Microsoft 推送通知服务 (MPNS) 设置。

## <a name="prerequisites"></a>先决条件

如果你尚未创建通知中心，现在请创建一个。 有关详细信息，请参阅[在 Azure 门户中创建 Azure 通知中心](create-notification-hub-portal.md)。

## <a name="configure-microsoft-push-notification-service-mpns"></a>配置 Microsoft 推送通知服务 (MPNS)

以下过程介绍了如何为通知中心配置 Microsoft 推送通知服务 (MPNS) 设置：

1. 在 Azure 门户的“通知中心”页上，在左侧菜单中选择“Windows Phone (MPNS)”。  
2. 启用未经身份验证或经过身份验证的通知：

   a. 若要启用未经身份验证的推送通知，请选择“启用未经身份验证的推送” > “保存”。  

      ![显示如何启用未经身份验证的推送通知的屏幕截图](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. 启用经过身份验证的推送通知：
      * 在工具栏上选择“上传证书”  。
      * 选择“文件”图标，然后选择证书文件。
      * 输入证书的密码。
      * 选择“确定”。
      * 在“Windows Phone (MPNS)”页上选择“保存”。  

## <a name="next-steps"></a>后续步骤

如需通过教程的分步说明来了解如何使用 Azure 通知中心和 Microsoft 推送通知服务 (MPNS) 将通知推送到 Windows Phone 设备，请参阅[使用通知中心将推送通知发送到 Windows Phone 应用](notification-hubs-windows-mobile-push-notifications-mpns.md)。
