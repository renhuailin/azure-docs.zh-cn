---
title: ML 工作室（经典版）：管理工作区 - Azure
description: 管理对机器学习工作室（经典）工作区的访问，部署并管理机器学习 API Web 服务
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: afc53c04110674706c930ba49f19e5be0da86e9c
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112581207"
---
# <a name="manage-a-machine-learning-studio-classic-workspace"></a>管理机器学习工作室（经典）工作区

**适用对象：** ![适用于.](../../../includes/media/aml-applies-to-skus/yes.png)机器学习工作室（经典）   ![不适用于.](../../../includes/media/aml-applies-to-skus/no.png)[Azure 机器学习](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


> [!NOTE]
> 有关在机器学习 Web 服务门户中管理 Web 服务的信息，请参阅[使用机器学习 Web 服务门户管理 Web 服务](manage-new-webservice.md)。
> 
> 

可以在 Azure 门户中管理机器学习工作室（经典）工作区。



## <a name="use-the-azure-portal"></a>使用 Azure 门户

若要在 Azure 门户中管理工作室（经典）工作区，请执行以下操作：

1. 使用 Azure 订阅管理员帐户登录到 [Azure 门户](https://portal.azure.com/)。
2. 在页面顶部的搜索框中，输入“机器学习工作室(经典)工作区”，并选择“机器学习工作室(经典)工作区”。
3. 单击想要管理的工作区。

除了标准的资源管理信息和可用选项外，还可以：

- 查看 **属性** - 此页显示工作区和资源信息，并可以更改此工作区连接到的订阅和资源组。
- **重新同步存储密钥** - 此工作区维护存储帐户的密钥。 如果存储帐户更改密钥，则可以单击“重新同步密钥”将密钥与工作区同步。

若要管理与此工作室（经典）工作区关联的 Web 服务，请使用“机器学习 Web 服务”门户。 有关完整信息，请参阅[使用机器学习 Web 服务门户管理 Web 服务](manage-new-webservice.md)。

> [!NOTE]
> 若要部署或管理新 Web 服务，必须分配有该 Web 服务部署到的订阅上的参与者或管理员角色。 如果邀请其他用户加入机器学习工作室（经典）工作区，必须向其分配订阅上的参与者或管理员角色，然后这些用户才能部署或管理 Web 服务。 
> 
>有关设置访问权限的详细信息，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>后续步骤
* 详细了解[使用 Azure 资源管理器模板部署机器学习](deploy-with-resource-manager-template.md)。