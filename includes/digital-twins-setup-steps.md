---
author: baanders
description: Azure 数字孪生安装程序中的步骤概述的 include 文件
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: c1cfab8c9e68d9e6b0c3b84e8848645a50c24710
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "100560790"
---
新的 Azure 数字孪生实例的完整设置包括两部分：
1. **创建实例**
2. **设置用户访问权限**：Azure 用户需要在 Azure 数字孪生实例上具有“Azure 数字孪生数据所有者”角色，才能管理该实例及其数据。 在此步骤中，你作为 Azure 订阅的所有者/管理员需将此角色分配给将要管理 Azure 数字孪生实例的人员。 这可能是你自己或你组织中的其他人。
 
>[!NOTE]
>这些操作应由有权管理 Azure 订阅上的资源和用户访问权限的用户完成。 尽管有些步骤使用较低的权限就能完成，但是需要具有这些权限的人合作才能完全设置一个可用的实例。 有关详细信息，请查看下面的[先决条件：必需权限](#prerequisites-permission-requirements)部分。