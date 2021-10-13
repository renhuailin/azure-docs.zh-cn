---
title: 访问 Azure VMware Solution by CloudSimple - 门户
description: 介绍如何在 Azure 门户中访问 VMware Solution by CloudSimple
author: suzizuber
ms.author: v-szuber
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 669141627d4665e3c44c44463232ca2d4883fc99
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618340"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>从 Azure 门户访问 VMware Solution by CloudSimple 门户

支持单一登录访问 CloudSimple 门户。 登录到 Azure 门户后，便可以访问 CloudSimple 门户，而无需再次登录。 首次访问 CloudSimple 门户时，系统将提示用户授权 “[CloudSimple Service Authorization](#consent-to-cloudsimple-service-authorization-application)” 应用程序。  授权是一次性操作。

## <a name="before-you-begin"></a>准备阶段

用户如具有内置“**所有者**”和“**参与者**”角色，则可以访问 CloudSimple 门户。  必须在部署 CloudSimple 服务的资源组上配置角色。  还可以在 CloudSimple 服务对象上配置角色。  有关检查角色的详细信息，请参阅“[查看角色分配](../role-based-access-control/check-access.md)”一文。 用户只有具有内置“**所有者**”和“**参与者**”角色，才可以访问 CloudSimple 门户。  必须在订阅上配置角色。  有关检查角色的详细信息，请参阅“[查看角色分配](../role-based-access-control/check-access.md)”一文。

如果用户使用的是自定义角色，则该角色应具有以下任一操作（见 ```Actions```）。  有关自定义角色的详细信息，请参阅 [Azure 自定义角色](../role-based-access-control/custom-roles.md)。  如果任一操作是 ```NotActions``` 的一部分，则用户将无法访问 CloudSimple 门户。

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="access-the-cloudsimple-portal"></a>访问 CloudSimple 门户

1. 选择“所有服务”  。

2. 搜索“CloudSimple 服务”。

3. 选择要在上面创建私有云的 CloudSimple 服务。

4. 在“**概述**”页上，单击  **转到 CloudSimple 门户**。  首次从 Azure 门户访问 CloudSimple 门户时，系统将提示用户授权 “[CloudSimple Service Authorization](#consent-to-cloudsimple-service-authorization-application)” 应用程序。 

    ![启动 CloudSimple 门户](media/launch-cloudsimple-portal.png)

> [!NOTE]
> 如果直接从 Azure 门户选择“私有云”操作（如创建或扩展私有云），CloudSimple 门户会打开到指定的页面。

在 CloudSimple 门户中，选择侧边菜单上的“**主页**”，以显示有关私有云的摘要信息。 将显示私有云的资源和容量，以及需要注意的警报和任务。 对于常见任务，请单击页面顶部的命名图标。

![主页](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>同意使用 “CloudSimple Service Authorization” 应用程序

首次从 Azure 门户启动 CloudSimple 门户时，用户需要同意使用 “CloudSimple Service Authorization” 应用程序。  选择“**接受**”，以授予所需权限并访问 CloudSimple 门户。

![同意使用 “CloudSimple Service Authorization”-管理员](media/cloudsimple-azure-consent.png)

如果用户拥有全局管理员权限，则可以代表组织同意。  选择“代表组织授予同意”。

![同意使用 “CloudSimple Service Authorization”-全局管理员](media/cloudsimple-azure-consent-global-admin.png)

如果用户的权限不足以访问 CloudSimple 门户，请联系租户的全局管理员，让其授予所需的权限。  全局管理员可以代表用户的组织给出同意。

![同意使用 “CloudSimple Service Authorization”-需要管理员](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>后续步骤

* 了解如何[创建私有云](./create-private-cloud.md)
* 了解如何[配置私有云环境](quickstart-create-private-cloud.md)
