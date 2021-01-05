---
title: Azure Monitor-配置与 ServiceNow 的 IT 服务管理连接器安全导出
description: 本文介绍如何在 Azure Monitor 中通过 ServiceNow 将 ITSM 产品/服务连接到安全导出。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: fc384ffbc246f5ce9fa84b8161cbc4a5226fa5c8
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838506"
---
# <a name="connect-servicenow-to-azure-monitor"></a>将 ServiceNow 连接到 Azure Monitor

以下部分提供有关如何在 Azure 中连接 ServiceNow 产品和安全导出的详细信息。

## <a name="prerequisites"></a>先决条件

确保满足以下先决条件：

* 已注册 Azure AD。
* 你具有受支持的 ServiceNow 事件管理版本-ITOM (版本奥兰多或更高版本) 。

## <a name="configure-the-servicenow-connection"></a>配置 ServiceNow 连接

1. 使用 link https:// (instance name) . service-now.com/api/sn_em_connector/em/inbound_event?source=azuremonitor 是安全导出定义的 URI。

2. 按照以下版本的说明操作：
   * [巴黎](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [举办](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [纽约](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)