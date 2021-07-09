---
author: baanders
description: 用于发布和配置函数应用的 include 文件
ms.service: digital-twins
ms.topic: include
ms.date: 03/15/2021
ms.author: baanders
ms.openlocfilehash: 463661af3f576b6382aacee9abefaa6f3caa7957
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110615912"
---
有关如何执行此操作的说明，请参阅《操作说明：设置用于处理数据的函数》一文中的[将函数应用发布到 Azure](../articles/digital-twins/how-to-create-azure-function.md#publish-the-function-app-to-azure) 部分。

> [!IMPORTANT]
> 在[先决条件](#prerequisites)部分首次创建函数应用时，你可能已经为该函数分配了访问角色，并为其配置了访问 Azure 数字孪生实例所需的应用程序设置。 这些操作需要针对整个函数应用执行一次，因此在继续之前，请验证是否已在你的应用中完成这些操作。 可以在“操作指南：引入 IoT 中心数据”一文的[为函数应用设置安全访问](../articles/digital-twins/how-to-create-azure-function.md#set-up-security-access-for-the-function-app)部分中找到相关说明。