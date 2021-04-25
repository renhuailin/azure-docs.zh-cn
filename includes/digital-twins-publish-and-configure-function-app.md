---
author: baanders
description: 用于发布和配置函数应用的 include 文件
ms.service: digital-twins
ms.topic: include
ms.date: 03/15/2021
ms.author: baanders
ms.openlocfilehash: 33785f7d7d34072c2c074ede69198a11ab49ff80
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327481"
---
有关如何执行此操作的说明，请参阅“操作指南：设置用于处理数据的函数”一文中的[将函数应用发布到 Azure](../articles/digital-twins/how-to-create-azure-function.md#publish-the-function-app-to-azure) 部分。

> [!IMPORTANT]
> 在[先决条件](#prerequisites)部分首次创建函数应用时，你可能已经为该函数分配了访问角色，并为其配置了访问 Azure 数字孪生实例所需的应用程序设置。 这些操作需要针对整个函数应用执行一次，因此在继续之前，请验证是否已在你的应用中完成这些操作。 可以在操作指南：引入 IoT 中心数据一文的[为函数应用设置安全访问](../articles/digital-twins/how-to-create-azure-function.md#set-up-security-access-for-the-function-app)部分中找到相关说明。