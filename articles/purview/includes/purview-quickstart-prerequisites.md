---
title: include 文件
description: include 文件
services: purview
author: whhender
ms.author: whhender
ms.service: purview
ms.topic: include
ms.custom: include file
ms.date: 09/10/2021
ms.openlocfilehash: 8b2b6cf87380c0c89327a7cf0afd5331a140f836
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212471"
---
## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请在开始前创建一个[免费订阅](https://azure.microsoft.com/free/)。

* 与订阅关联的 [Azure Active Directory 租户](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)。

* 用于登录到 Azure 的用户帐户必须属于参与者或所有者角色，或者是 Azure 订阅的管理员  。 若要查看你在订阅中拥有的权限，请转到 [Azure 门户](https://portal.azure.com)，在右上角选择你的用户名，然后选择“...”图标以显示更多选项，然后选择“我的权限”。 如果可以访问多个订阅，请选择相应的订阅。

* 没有任何阻止创建存储帐户或事件中心命名空间的 [Azure 策略](../../governance/policy/overview.md)。 Purview 将在创建时部署托管存储帐户和事件中心。 如果存在阻止策略并需要保留，请按照 [Purview 异常标记指南](../create-purview-portal-faq.md)操作，并执行其中步骤为 Purview 帐户创建异常。

## <a name="sign-in-to-azure"></a>登录 Azure

使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。
