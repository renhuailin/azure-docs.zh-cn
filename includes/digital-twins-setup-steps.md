---
author: baanders
description: Azure 数字孪生安装程序中的步骤概述的包含文件
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: c1cfab8c9e68d9e6b0c3b84e8848645a50c24710
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560790"
---
新的 Azure 数字孪生实例的完全安装包括两个部分：
1. **创建实例**
2. **设置用户访问权限**： azure 用户需要在 Azure 数字孪生实例上拥有 *Azure 数字孪生数据所有者* 角色才能管理它及其数据。 在此步骤中，你作为 Azure 订阅的所有者/管理员，会将此角色分配给将管理 Azure 数字孪生实例的人员。 这可能是你自己或组织中的其他人。
 
>[!NOTE]
>这些操作旨在由有权管理 Azure 订阅上的资源和用户访问权限的用户完成。 尽管某些步骤可以使用较低权限完成，但需要具有这些权限的人员才能完全设置一个可用的实例。 有关详细信息，请查看下面的 [*先决条件： Required 权限*](#prerequisites-permission-requirements) 部分。