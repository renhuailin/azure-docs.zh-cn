---
title: 在 Azure API 管理中管理协议和加密 | Microsoft Docs
description: 了解如何在 Azure API 管理中管理协议 (TLS) 和密码 (DES)。
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/07/2021
ms.author: danlep
ms.openlocfilehash: 02442bf73d16486ce51b765e6922bbf434fb69f1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609795"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>在 Azure API 管理中管理协议和加密

Azure API 管理支持将多版本的传输层安全性 (TLS) 协议用于以下目标：
* 客户端
* 后端
* 3DES 加密

此指南将演示如何管理 Azure API 管理实例的协议和加密配置。

![在 APIM 中管理协议和加密](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>必备条件

* API 管理实例。 [如果还没有实例，请创建一个](get-started-create-service-instance.md)。

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>如何管理 TLS 协议和 3DES 加密

1. 在 Azure 门户中，导航到“ Azure 管理实例”。 
1. 滚动到侧边菜单中的“安全性”部分。
1. 在“安全性”部分下，选择“协议 + 加密”。  
1. 启用或禁用所需协议或加密。
1. 单击“保存”  。 更改将在一个小时内生效。  

> [!NOTE]
> 某些协议或加密套件（如后端 TLS 1.2）无法从 Azure 门户启用或禁用。 而是，需要应用 REST 调用。 使用[创建/更新 API 管理服务 REST API](/rest/api/apimanagement/2020-06-01-preview/api-management-service/create-or-update#request-body) 一文中的 `properties.customProperties` 结构。

## <a name="next-steps"></a>后续步骤

* 详细了解 [TLS](/dotnet/framework/network-programming/tls)。
* 观看有关 API 管理的更多[视频](https://azure.microsoft.com/documentation/videos/index/?services=api-management)。
