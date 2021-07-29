---
title: Azure 上的 Datadog 的先决条件 - Azure 合作伙伴解决方案
description: 本文介绍如何配置 Azure 环境以创建 Datadog 实例。
ms.service: partner-services
ms.topic: conceptual
ms.date: 05/28/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 637d3cfb3baf8a53cb62943419077fad0f70d33d
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110656759"
---
# <a name="configure-environment-before-datadog-deployment"></a>在部署 Datadog 之前配置环境

本文介绍如何在部署第一个 Datadog 实例之前设置环境。 这些条件是完成快速入门的先决条件。

## <a name="access-control"></a>访问控制

若要设置 Azure Datadog 集成，必须在 Azure 订阅上拥有“所有者”访问权限。 在开始设置之前，[请确认拥有适当的访问权限](../../role-based-access-control/check-access.md)。

## <a name="add-enterprise-application"></a>添加企业应用程序
 
若要在 Datadog 资源中使用安全断言标记语言 (SAML) 单一登录 (SSO) 功能，必须设置一个企业应用程序。 若要添加企业应用程序，需具有以下角色之一：“全局管理员”、“云应用程序管理员”、“应用程序管理员”或服务主体的所有者。

使用以下步骤设置企业应用程序：

1. 转到 [Azure 门户](https://portal.azure.com)。 选择“Azure Active Directory”  。
1. 在左窗格中，选择“企业应用程序”。
1. 选择“新建应用程序”。
1. 在“从库中添加”中，搜索 *Datadog*。 选择搜索结果，然后选择“添加”。

   :::image type="content" source="media/prerequisites/datadog-azure-ad-app-gallery.png" alt-text="AAD 企业库中的 Datadog 应用程序。" border="true":::

1. 创建应用后，从边侧面板转到“属性”。 将“需要进行用户分配?”设置为“否”，然后选择“保存”。  

   :::image type="content" source="media/prerequisites/user-assignment-required.png" alt-text="设置 Datadog 应用程序的属性" border="true":::

1. 从边侧面板转到“单一登录”。 然后选择“SAML”。

   :::image type="content" source="media/prerequisites/saml-sso.png" alt-text="SAML 身份验证。" border="true":::

1. 当系统提示“保存单一登录设置”时，请选择“是”。 

   :::image type="content" source="media/prerequisites/save-sso.png" alt-text="保存 Datadog 应用的单一登录" border="true":::

1. 单一登录的设置现已完成。

## <a name="next-steps"></a>后续步骤

若要创建 Datadog 的实例，请参阅[快速入门：Datadog 入门](create.md)。
