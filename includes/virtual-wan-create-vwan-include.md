---
ms.author: cherylmc
author: cherylmc
ms.date: 08/17/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 2403ce53175457d0a433f9a6a9310f93f0165a0d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122321866"
---
从浏览器导航到 [Azure 门户](https://portal.azure.com)并使用 Azure 帐户登录。

1. 在门户中的“搜索资源”栏中，在搜索框中键入“虚拟 WAN”，然后选择 Enter  。

1. 从结果中选择“虚拟 WAN”。 在“虚拟 WAN”页上，选择“+ 创建”以打开“创建 WAN”页。

1. 在“创建 WAN”页的“基本信息”选项卡上，填写以下字段 。 你可以修改示例值以应用于你的环境。

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="屏幕截图显示已选择“基本”选项卡的“创建 WAN”窗格。":::

   * 订阅：选择要使用的订阅。
   * 资源组：新建资源组或使用现有的资源组。
   * 资源组位置：从下拉列表中选择资源位置。 WAN 是一个全局资源，不会驻留在某个特定区域。 但是，必须选择一个区域才能管理和查找所创建的 WAN 资源。
   * 名称：键入要用于称呼 WAN 的名称。
   * 类型：基本或标准。 选择“标准”。 如果选择“基本”，请了解基本虚拟 WAN 只能包含基本中心。 基本中心只能用于站点到站点连接。

1. 填写完字段后，在页面底部，选择“查看 + 创建”。

1. 验证通过后，选择“创建”以创建虚拟 WAN。
