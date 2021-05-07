---
title: 连接到 Dynamics 365
description: 使用 Azure 逻辑应用创建和管理 Dynamics 365 记录
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 05/09/2020
tags: connectors
ms.openlocfilehash: fe1e31ce0bc842e1da18327d12e7a52562d84b53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "87284024"
---
# <a name="create-and-manage-records-in-dynamics-365-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用在 Dynamics 365 中创建和管理记录

Dynamics 365 使用[Common Data Service](/powerapps/maker/common-data-service/data-platform-intro)。 要连接到 Dynamics 365，请使用 [Common Data Service 连接器](/connectors/commondataservice/)。

> [!IMPORTANT]
> [Dynamics 365 连接器](/connectors/dynamicscrmonline/)已弃用，但在删除之前将继续工作。 请不要将 Dynamics 365 连接器用于新的逻辑应用。 尚未宣布删除 Dynamics 365 连接器的时间线。 不需要将现有逻辑应用转换为 Common Data Service 连接器或其他计划的新连接器，但在删除连接器时需要转换逻辑应用。 有关详细信息，请参阅 [Dynamics 365 连接器已弃用](/power-platform/important-changes-coming)。
>
> [Common Data Service 连接器](/connectors/commondataservice/)提供与已弃用的 Dynamics 365 连接器相同的功能，但包含提高可靠性的改进。 有关在逻辑应用中使用 Common Data Service 连接器的信息，请参阅[使用 Azure 逻辑应用创建和管理 Common Data Service 记录](../connectors/connect-common-data-service.md)。

有关 Common Data Service 的详细信息，请参阅以下主题：

* [Learn：Common Data Service 入门](/learn/modules/get-started-with-powerapps-common-data-service/)
* [了解：通过使用 Power Platform 和 Common Data Service 来连接和分析 Dynamics 365 数据](/learn/wwl/connect-analyze-dynamics-365-data/)
