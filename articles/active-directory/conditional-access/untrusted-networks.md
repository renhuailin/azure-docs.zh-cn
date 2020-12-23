---
title: 要求来自不受信任网络的访问进行 MFA - Azure Active Directory
description: 了解如何在 Azure Active Directory (Azure AD) 中针对来自不受信任网络的访问尝试配置条件访问策略。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4c020a9be7683bf045dbcc747dad3cb45058dd7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077670"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>如何：使用条件访问要求来自不受信任网络的访问进行 MFA   

Azure Active Directory (Azure AD) 允许从任何位置以单一登录方式登录到设备、应用和服务。 用户不但可以从组织的网络访问云应用，而且可以从任何不受信任的 Internet 位置访问云应用。 对于来自不受信任网络的访问，常见的最佳做法是要求其进行多重身份验证 (MFA)。

本文提供了在配置条件访问策略以要求来自不受信任网络的访问进行 MFA 时需要的信息。 

## <a name="prerequisites"></a>先决条件

本文假定您熟悉条件性访问的 [基本概念](overview.md) 。 

## <a name="scenario-description"></a>方案描述

为掌控安全性与工作效率之间的平衡，对于你来说，对于来自你的组织网络的登录，只需要要求其提供密码可能就足够了。 但是，对于来自不受信任网络位置的访问，登录不是由合法用户执行的这一风险会增大。 要解决此顾虑，可以阻止来自不受信任网络的访问。 另外，还可以要求进行多重身份验证 (MFA) 来获得额外的保证，以确保访问尝试是由该帐户的合法所有者执行的。 

使用 Azure AD 条件访问，可以通过进行授权的以下单个策略来解决此要求： 

- 授予对所选云应用的访问权限
- 为所选用户和组授予权限  
- 要求进行多重身份验证 
- 当访问来自： 
   - 不受信任的位置

## <a name="implementation"></a>实现

此方案的难题是将 *访问从不受信任的网络位置* 转换为条件访问条件。 在条件访问策略中，可以配置 [位置条件](location-condition.md) ，以解决与网络位置相关的情况。 使用位置条件，你可以选择已命名位置，这些位置是 IP 地址范围、国家和地区的逻辑分组。  

通常，你的组织拥有一个或多个地址范围，例如 199.30.16.0 - 199.30.16.15。
可以通过以下方式配置命名位置：

- 指定此范围 (199.30.16.0/28) 
- 分配一个描述性名称，例如 **公司网络** 

可以选择以下选项，而不是尝试定义不受信任的所有位置：

- 包括任何位置 

   :::image type="content" source="./media/untrusted-networks/02.png" alt-text="Azure &quot;D 位置&quot; 窗格的屏幕截图，将 &quot;配置&quot; 设置为 &quot;是&quot;，&quot;包括&quot; 选项卡可见，并选中并突出显示 &quot;任何位置&quot; 选项。" border="false":::

- 排除所有受信任的位置 

   :::image type="content" source="./media/untrusted-networks/01.png" alt-text="Azure &quot;D 位置&quot; 窗格的屏幕截图，将 &quot;配置&quot; 设置为 &quot;是&quot;，将显示 &quot;排除&quot; 选项卡，并选择 &quot;所有受信任位置&quot; 选项。" border="false":::

## <a name="policy-deployment"></a>策略部署

使用本文中概述的方法，你现在可以针对不受信任位置配置条件访问策略。 若要确保你的策略按预期工作，建议的最佳做法是在将其推广到生产环境之前对其进行测试。 理想情况下，使用一个测试租户来验证新策略是否按预期方式工作。

## <a name="next-steps"></a>后续步骤

如果要了解有关条件性访问的详细信息，请参阅 [什么是 Azure Active Directory 中的条件性访问？](./overview.md)
