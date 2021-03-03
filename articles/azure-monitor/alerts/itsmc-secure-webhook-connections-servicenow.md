---
title: Azure Monitor-配置与 ServiceNow 的 IT 服务管理连接器安全导出
description: 本文介绍如何在 Azure Monitor 中通过 ServiceNow 将 ITSM 产品/服务连接到安全导出。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 02b167219a4f1604d340d72f9dc47e67919c2542
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714130"
---
# <a name="connect-servicenow-to-azure-monitor"></a>将 ServiceNow 连接到 Azure Monitor

以下部分提供有关如何在 Azure 中连接 ServiceNow 产品和安全导出的详细信息。

## <a name="prerequisites"></a>先决条件

确保满足以下先决条件：

* 已注册 Azure AD。
* 你具有受支持的 ServiceNow 事件管理版本-ITOM (纽约或更高版本) 。
* 在 ServiceNow 实例上安装的[应用程序](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ac4c9c57dbb1d090561b186c1396191a/1.3.1?referer=%2Fstore%2Fsearch%3Flistingtype%3Dallintegrations%25253Bancillary_app%25253Bcertified_apps%25253Bcontent%25253Bindustry_solution%25253Boem%25253Butility%26q%3DEvent%2520Management%2520Connectors&sl=sh)。

## <a name="configure-the-servicenow-connection"></a>配置 ServiceNow 连接

1. 使用 link https:// (instance name) . service-now.com/api/sn_em_connector/em/inbound_event?source=azuremonitor 是安全导出定义的 URI。

2. 按照以下版本的说明操作：
   * [巴黎](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [举办](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [纽约](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
