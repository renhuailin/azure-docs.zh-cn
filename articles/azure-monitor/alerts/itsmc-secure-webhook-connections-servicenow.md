---
title: IT 服务管理连接器 - Azure Monitor 中的安全导出 - 使用 ServiceNow 进行配置
description: 本文介绍如何基于 Azure Monitor 中的安全导出连接 ITSM 产品/服务与 ServiceNow。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 20cfd57ea759138ff3e266632eb87b9f90669934
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129426466"
---
# <a name="connect-servicenow-to-azure-monitor"></a>将 ServiceNow 连接到 Azure Monitor

以下部分提供有关如何连接 ServiceNow 产品和 Azure 中的安全导出的详细信息。

## <a name="prerequisites"></a>先决条件

确保符合以下先决条件：

* 已注册 Azure AD。
* 你具有受支持的 ServiceNow 事件管理版本 - ITOM（纽约版本或更高版本）。
* 在 ServiceNow 实例上安装的[应用程序](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ac4c9c57dbb1d090561b186c1396191a/1.3.1?referer=%2Fstore%2Fsearch%3Flistingtype%3Dallintegrations%25253Bancillary_app%25253Bcertified_apps%25253Bcontent%25253Bindustry_solution%25253Boem%25253Butility%26q%3DEvent%2520Management%2520Connectors&sl=sh)。

## <a name="configure-the-servicenow-connection"></a>配置 ServiceNow 连接

1. 使用链接 https://(instance name).service-now.com/api/sn_em_connector/em/inbound_event?source=azuremonitor the URI for the secure export definition。

2. 请按照以下说明操作，具体视版本而异：
   * [魁北克](https://docs.servicenow.com/bundle/quebec-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [巴黎](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [奥兰多](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/concept/azure-integration.html)
