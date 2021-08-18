---
title: 将 Microsoft Defender for Office 365（前身为 Office 365 ATP）的数据连接到 Azure Sentinel |Microsoft Docs
description: 将 Microsoft Defender for Office 365 的数据引入到 Azure Sentinel，以获得可见性并构建自动响应方案。
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
ms.date: 09/06/2020
ms.author: yelevin
ms.openlocfilehash: 7c4482f788014aec45f7c2e2ca9c4820bd80bb33
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778323"
---
# <a name="connect-alerts-from-microsoft-defender-for-office-365"></a>连接来自 Microsoft Defender for Office 365 的警报 

> [!IMPORTANT]
>
> - “Microsoft Defender For office 365”以前称为“Office 365 高级威胁防护 (ATP)”。
>
>     此产品可能会在一段时间内使用旧名称（包括其在 Azure Sentinel 中的数据连接器）。
>
> - Microsoft Defender for Office 365 警报的引入目前为公共预览版。 此功能不附带服务级别协议，不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

[Microsoft Defender For Office 365](/office365/servicedescriptions/office-365-advanced-threat-protection-service-description) 保护组织免受电子邮件信息、恶意 URL 链接和协作工具中的恶意软件造成的零日威胁和其他高级威胁。 将 Microsoft Defender for Office 365 警报引入到 Azure Sentinel 后，能够在安全操作中利用有关电子邮件、文件共享和基于 URL 的威胁的信息。 然后可以更全面地分析组织中的安全事件，并构建演练手册以实现有效且即时的响应。

连接器将导入以下警报：

- 检测到潜在的恶意 URL 点击 

- 传递后删除包含恶意软件的电子邮件

- 传递后删除包含网络钓鱼 URL 的电子邮件 

- 用户报告为恶意软件或网络钓鱼电子邮件 

- 检测到可疑的电子邮件发送模式 

- 用户无法发送电子邮件 

“Office 安全和合规中心”中的 Office 客户可以查看这些警报。

## <a name="prerequisites"></a>先决条件

- 启用连接器后必须对 Azure Sentinel 工作区拥有读取和写入权限。

- 必须是 Azure Sentinel 工作区租户的全局管理员或安全管理员。

- 必须有 [Office 365 ATP 计划 2](/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2) 的有效许可证（包含在 Office 365 E5、Office 365 A5 和 Microsoft 365 E5 许可证中，并可单独购买）。 

## <a name="connect-to-microsoft-defender-for-office-365"></a>连接到 Microsoft Defender for Office 365

如果部署了 Microsoft Defender for Office 365 且配置了策略，则可以轻松地将警报引入到 Azure Sentinel。

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 在连接器库中选择“Microsoft Defender For Office 365”（可能仍称为“Office 365 高级威胁防护”），然后选择“打开连接器页面”。

1. 在“配置”部分中，单击“连接”。  

1. 在“创建事件”部分中，单击“启用”。

1. 若要使用相关架构查询 Office 365 ATP 警报，请搜索 SecurityAlert 并指定“提供程序名称”为“OATP”。

1. 选择“后续步骤”选项卡以查看并使用与 Microsoft Defender for Office 365 连接器捆绑的查询示例和分析规则模板。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Microsoft Defender for Office 365 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](./detect-threats-built-in.md)。