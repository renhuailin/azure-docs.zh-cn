---
title: 将 Azure AD 标识保护数据连接到 Azure Sentinel
description: 了解如何将日志和警报从 Azure AD 标识保护流式传输到 Azure Sentinel，以查看仪表板、创建自定义警报并改进调查。
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 06/24/2020
ms.author: yelevin
ms.openlocfilehash: 2f7d947afcbd3017df1c2183da6c88ec92656bc5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778329"
---
# <a name="connect-data-from-azure-active-directory-azure-ad-identity-protection"></a>从 Azure Active Directory (Azure AD) 标识保护中连接数据

可以将日志从[ Azure AD 标识保护](../active-directory/identity-protection/overview-identity-protection.md)流式传输到 Azure Sentinel，以将警报流式传输到 Azure Sentinel，进而查看仪表板、创建自定义警报并改进调查。 Azure Active Directory 标识保护提供风险用户、风险检测和漏洞的综合视图，能够立即修复风险，并设置策略以自动修复将来的事件。 该服务基于 Microsoft 保护用户标识的经验而构建，并通过每天超过 130 亿次登录的信号来大幅提升准确性。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>先决条件

- 必须有[Azure AD Premium P2 订阅](https://azure.microsoft.com/pricing/details/active-directory/)。
- 必须有具备全局管理员或安全管理员权限的用户。

## <a name="connect-to-azure-ad-identity-protection"></a>连接到 Azure AD 标识保护

如果有 Azure AD Premium P2 订阅，则 Azure AD 标识保护也包括在其中。 如果[启用了任何策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)并生成警报，则可以轻松将警报数据流式传输到 Azure Sentinel。

1. 在 Azure Sentinel 中，选择“数据连接器”，然后单击“Azure AD 标识保护”磁贴。

1. 单击“连接”，开始将 Azure AD 标识保护事件流式传输到 Azure Sentinel。

1. 若要使用 Log Analytics 中的相关架构以获得 Azure AD 标识保护警报，请搜索“SecurityAlert”。

如果要测试连接器，可以[模拟检测](../active-directory/identity-protection/howto-identity-protection-simulate-risk.md)以生成将流式传输到 Azure Sentinel 的示例警报。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Azure AD 标识保护连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
