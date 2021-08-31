---
title: 为 DICOM 服务配置 Azure RBAC - Azure Healthcare APIs
description: 本文介绍如何为 DICOM 服务配置 Azure RBAC
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2020
ms.author: aersoy
ms.openlocfilehash: 76d2fa40799d97a7fb2284dbc6abf3ac141552d9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778476"
---
# <a name="configure-azure-rbac-for-the-dicom-service"></a>为 DICOM 服务配置 Azure RBAC

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。 

在本文中，你将了解如何使用 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/index.yml) 来分配对 DICOM 服务的访问权限。 

## <a name="assign-roles"></a>分配角色

若要向用户、服务主体或组授予对 DICOM 数据平面的访问权限，请选择“访问控制(IAM)”边栏选项卡。 选择“角色分配”选项卡，然后选择“+ 添加”。

[ ![DICOM 访问控制。](media/dicom-access-control.png) ](media/dicom-access-control.png#lightbox)


在“角色”选择中，搜索 DICOM 数据平面的一个内置角色：

[ ![添加 RBAC 角色分配。](media/rbac-add-role-assignment.png) ](media/rbac-add-role-assignment.png#lightbox)

你可以选择：

* DICOM 数据所有者：对 DICOM 数据具有完全访问权限。
* DICOM 数据读者：可以读取和搜索 DICOM 数据。

如果这些角色不能满足你的需求，可以使用 PowerShell 创建自定义角色。  有关创建自定义角色的信息，请参阅[使用 Azure PowerShell 创建自定义角色](../../role-based-access-control/tutorial-custom-role-powershell.md)。

在“选择”框中，搜索要为其分配角色的用户、服务主体或组。

## <a name="caching-behavior"></a>缓存行为

DICOM 服务将决策最多缓存 5 分钟。 如果将用户添加到允许的对象 ID 列表，从而授予用户对 DICOM 服务的访问权限，或者将用户从列表中删除，则预计需要 5 分钟的时间来传播权限的变化。

## <a name="next-steps"></a>后续步骤

在本文中，你已经了解了如何为 DICOM 服务数据平面分配 Azure 角色。 
 
>[!div class="nextstepaction"]
>[DICOM 服务概述](dicom-services-overview.md)