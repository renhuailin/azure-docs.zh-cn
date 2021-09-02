---
author: baanders
description: Azure 数字孪生的 include 文件 - 设置应用注册的先决条件
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 128aa82762b80958f02cf22e9e7017bb334fde0c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437823"
---
若要对本文中使用的所有资源进行身份验证，需要 **设置一个 [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md)应用注册**。 按照[创建应用注册](../articles/digital-twins/how-to-create-app-registration-portal.md)中的说明进行此设置。 

具有应用注册后，需要注册的“应用程序（客户端）ID”、“目录（租户）ID”和“客户端密码”（[可在 Azure 门户中找到](../articles/digital-twins/how-to-create-app-registration-portal.md#collect-important-values)）  。 请记下这些值，以便稍后用它们授予对 Azure 数字孪生 API 的访问权限。