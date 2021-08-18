---
title: 作为安全托管服务提供商在 Azure Sentinel 中管理多个租户 | Microsoft Docs
description: 如何作为安全托管服务提供商 (MSSP) 使用 Azure Lighthouse 在 Azure Sentinel 中加入和管理多个租户。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2020
ms.author: yelevin
ms.openlocfilehash: cf2a9380b404876ef9c963d54d9069ebd66fef56
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725148"
---
# <a name="manage-multiple-tenants-in-azure-sentinel-as-an-mssp"></a>作为 MSSP 在 Azure Sentinel 中管理多个租户

如果你是安全托管服务提供商 (MSSP)，并且是使用 [Azure Lighthouse](../lighthouse/overview.md) 为客户提供安全运营中心 (SOC) 服务，则可直接从你自己的 Azure 租户管理客户的 Azure Sentinel 资源，而无需连接到客户的租户。 

## <a name="prerequisites"></a>先决条件

- [加入 Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)

- 为此，你的租户（MSSP 租户）必须在至少一个订阅上注册了 Azure Sentinel 资源提供程序。 此外，客户的每个租户都必须已注册资源提供程序。 如果你已在自己的租户中注册了 Azure Sentinel，而客户也已在其租户中完成注册，那么就可以开始了。 若要验证注册，请执行以下步骤：

    1. 从 Azure 门户中选择“订阅”，然后从菜单中选择相关订阅。

    1. 从订阅屏幕上的导航菜单中的“设置”下，选择“资源提供程序”。

    1. 在“订阅名称 | 资源提供程序”屏幕中，搜索并选择“Microsoft.OperationalInsights”和“Microsoft.SecurityInsights”，并检查“状态”列。 如果提供程序的状态为“未注册”，请选择“注册”。
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="检查资源提供程序":::

## <a name="how-to-access-azure-sentinel-in-managed-tenants"></a>如何在托管租户中访问 Azure Sentinel

1. 在“目录 + 订阅”下，选择委托目录（目录 = 租户）和客户的 Azure Sentinel 工作区所在的订阅。

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="选择租户和订阅":::

1. 打开 Azure Sentinel。 你会看到所选订阅中的所有工作区，并且能够无缝地使用这些工作区，就像使用你自己的租户中的任何工作区一样。

> [!NOTE]
> 你将无法从托管工作区部署 Azure Sentinel 中的连接器。 若要部署连接器，你必须直接登录到要在其上部署连接器的租户，在那里使用所需的权限进行身份验证。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何无缝地管理多个 Azure Sentinel 租户。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。

