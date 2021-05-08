---
title: 将威胁情报数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将威胁情报数据连接到 Azure Sentinel。
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
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: 205cc6eea5d1ac3be2d0e266621067dc8e20d2f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96121753"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>连接威胁情报提供程序中的数据

> [!IMPORTANT]
> Azure Sentinel 中的威胁情报数据连接器目前处于公共预览版阶段。
> 此功能不附带服务级别协议，不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

利用 Azure Sentinel，可以导入组织使用的威胁指示器，从而能够增强安全分析员检测到已知威胁并确定这些威胁优先级的能力。 然后，便可使用或增强 Azure Sentinel 提供的多项功能：

- “分析”包括一组计划的规则模板，你可以启用这些模板，以便根据威胁指示器中日志事件的匹配项来生成警报和事件。

- “工作簿”提供有关以下方面的摘要信息：导入到 Azure Sentinel 中的威胁指示器，以及从与威胁指示器匹配的分析规则生成的任何警报。

- “搜寻”查询允许安全调查人员在常见搜寻方案的上下文中使用威胁指示器。

- “笔记本”可以在你调查异常情况并搜寻恶意行为时使用威胁指示器。

你可以将威胁指示器流式传输到 Azure Sentinel，具体方法是：使用下一部分中列出的某一种集成式威胁情报平台 (TIP) 产品，连接到 TAXII 服务器；或者使用与 [Microsoft Graph 安全性 TIINDICATORS API](/graph/api/resources/tiindicator) 的直接集成。

## <a name="integrated-threat-intelligence-platform-products"></a>集成式威胁情报平台产品

- [MISP 开源威胁情报平台](https://www.misp-project.org/)
    
    如需向客户端提供 MISP 实例以将威胁指示器迁移到 Microsoft Graph 安全性 API 的示例脚本，请参阅 [MISP 到 Microsoft Graph 安全性脚本](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP)。

- [Anomali ThreatStream](https://www.anomali.com/products/threatstream)

    若要下载 ThreatStream 集成器和扩展以及将 ThreatStream 情报连接到 Microsoft Graph 安全性 API 的说明，请参阅 [ThreatStream 下载](https://ui.threatstream.com/downloads)页。

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    有关指导说明，请参阅[使用 MineMeld 将 IOC 发送到 Microsoft Graph 安全性 API](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540)。

- [ThreatConnect 平台](https://threatconnect.com/solution/)

    有关信息，请参阅 [ThreatConnect 集成](https://threatconnect.com/integrations/)并在该页上查找 Microsoft Graph 安全性 API。

- [EclecticIQ 平台](https://www.eclecticiq.com/solutions)

- [ThreatQ 威胁情报平台](https://www.threatq.com/)

    有关信息和指导说明，请参阅[适用于 ThreatQ 集成的 Microsoft Sentinel 连接器](https://appsource.microsoft.com/product/web-apps/threatquotientinc1595345895602.microsoft-sentinel-connector-threatq?src=health&tab=Overview)。

## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>将 Azure Sentinel 连接到威胁情报平台

### <a name="prerequisites"></a>先决条件  

- Azure AD 角色（全局管理员或安全管理员），用于向 TIP 产品或自定义应用程序授予使用与 Microsoft Graph 安全性 tiIndicators API 的直接集成的权限。

- Azure Sentinel 工作区的读取和写入权限，用于存储威胁指示器。

### <a name="instructions"></a>说明

1. 在 Azure Active Directory 中[注册应用程序](/graph/auth-v2-service#1-register-your-app)，以获取应用程序 ID、应用程序机密和 Azure Active Directory 租户 ID。 在配置集成式 TIP 产品或与 Microsoft Graph 安全性 tiIndicators API 直接集成的应用时，需要这些值。

2. 为已注册的应用程序[配置 API 权限](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph)：将 Microsoft Graph 应用程序权限“ThreatIndicators.ReadWrite.OwnedBy”添加到已注册的应用程序。

3. 让 Azure Active Directory 租户管理为注册的组织应用程序授予管理员同意。 从 Azure 门户：Azure Active Directory  > “应用注册” > \<_app name_> > “查看 API 权限” > “为 \<_tenant name_> 授予管理员许可”    。

4. 通过指定以下内容，配置 TIP 产品或与 Microsoft Graph 安全性 tiIndicators API 直接集成的应用，以将指标发送到 Azure Sentinel：
    
    a. 注册的应用程序的 ID、机密和租户 ID 的值。
    
    b. 对于目标产品，请指定 Azure Sentinel。
    
    c. 对于“操作”，请指定警报。

5. 在 Azure 门户中，导航到“Azure Sentinel” > “数据连接器”，然后选择“威胁情报平台（预览版）”连接器  。

6. 选择“打开连接器”页面，然后单击“连接”。

7. 若要查看导入到 Azure Sentinel 的威胁指示器，请导航到“Azure Sentinel - 日志” > “SecurityInsights”，然后展开“ThreatIntelligenceIndicator”  。

## <a name="connect-azure-sentinel-to-taxii-servers"></a>将 Azure Sentinel 连接到 TAXII 服务器

### <a name="prerequisites"></a>先决条件

- Azure Sentinel 工作区的读取和写入权限，用于存储威胁指示器。

- TAXII 2.0 服务器 URI 和集合 ID。

### <a name="instructions"></a>说明

1. 在 Azure 门户中，导航到“Azure Sentinel” > “数据连接器”，然后选择“威胁情报 - TAXII（预览版）”连接器  。

2. 选择“打开连接器页面”。

3. 在文本框中指定必需和可选信息。

4. 选择“添加”，以启用到 TAXII 2.0 服务器的连接。

5. 如果有其他 TAXII 2.0 服务器：请重复第 3 和第 4 步。

6. 若要查看导入到 Azure Sentinel 的威胁指示器，请导航到“Azure Sentinel - 日志” > “SecurityInsights”，然后展开“ThreatIntelligenceIndicator”  。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将威胁情报连接到 Azure Sentinel。 若要详细了解 Azure Sentinel，请参阅以下文章。

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](./tutorial-detect-threats-built-in.md)。