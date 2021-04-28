---
author: baanders
description: 描述 Azure 数字孪生的跨租户限制的 include 文件
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 16684f8c5947f8b6a09a9a785968dabf3e644c18
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589313"
---
因此，向 Azure 数字孪生 API 发出的请求需要一个用户或服务主体，并且该用户或服务主体应属于 Azure 数字孪生实例所驻留的同一租户。 为防止恶意扫描 Azure 数字孪生终结点，对于来自源租户外部的具有访问令牌的请求，会返回“404 子域未找到”错误消息。 即使用户或服务主体已通过 [Azure AD B2B](../articles/active-directory/external-identities/what-is-b2b.md) 协作获得 Azure 数字孪生数据所有者或 Azure 数字孪生数据读取者[角色](../articles/digital-twins/concepts-security.md)，也会返回此错误。 