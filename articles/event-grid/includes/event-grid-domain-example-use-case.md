---
title: include 文件
description: include 文件
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 03/04/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2459cf6c5055dcde83dccf37addc160aeeaa64ad
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112413511"
---
解释事件域最简单的方法是采用示例。 假设你经营着 Contoso 建筑机械公司，你在其中制造拖拉机、挖掘设备和其他重型机械。 运营业务时，可向客户推送有关设备维护、系统运行状况、合同更新的实时信息。 所有这些信息都会发布到各种终结点，包括应用、客户终结点以及客户已安装的其他基础结构。

事件域允许将 Contoso 建筑机械公司建模为单个事件实体。 每个客户表示为域中的主题。 使用 Azure Active Directory 处理身份验证和授权。 每个客户都可订阅他们的主题并获取传送的事件。 通过事件域管理访问权限可确保客户仅能访问其主题。

此外，你还可获得一个单个终结点，可将所有客户事件发布至此终结点。 事件网格将负责确保每个主题仅获取其租户范围内的事件。

:::image type="content" source="./media/event-grid-domain-example-use-case/contoso-construction-example.png" alt-text="Contoso 建筑公司示例" lightbox="./media/event-grid-domain-example-use-case/contoso-construction-example.png":::