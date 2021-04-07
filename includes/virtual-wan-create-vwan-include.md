---
title: include 文件
description: include 文件
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/09/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5915830e4521399ad322dd4a6f3926428d811455
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "94942859"
---
从浏览器导航到 [Azure 门户](https://portal.azure.com)并使用 Azure 帐户登录。

1. 在门户中，选择“+ 创建资源”。 在搜索框中键入“虚拟 WAN”，然后选择“Enter” 。
1. 从结果中选择“虚拟 WAN”。 在“虚拟 WAN”页上，选择“创建”以打开“创建 WAN”页。
1. 在“创建 WAN”页的“基本信息”选项卡上，填写以下字段 ：

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="屏幕截图显示已选择“基本”选项卡的“创建 WAN”窗格。":::

   * **订阅** - 选择要使用的订阅。
   * **资源组** - 新建资源组或使用现有的资源组。
   * **资源组位置** - 从下拉列表中选择资源位置。 WAN 是一个全局资源，不会驻留在某个特定区域。 但是，必须选择一个区域才能管理和查找所创建的 WAN 资源。
   * **名称** - 键入要用于称呼 WAN 的名称。
   * **类型** - 免费、基本或标准。 选择“标准”。 如果选择基本 VWAN，请了解基本 VWAN 只能包含基本中心，这会将连接类型限制为站点到站点。
1. 填写完字段后，单击“审阅 + 创建”。
1. 验证通过后，选择“创建”以创建虚拟 WAN。
