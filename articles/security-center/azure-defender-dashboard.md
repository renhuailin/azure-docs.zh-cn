---
title: Azure Defender 的仪表板及其功能
description: 了解 Azure Defender 仪表板的功能。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: df7af81b51428f53beb7722c7f224dc7afdd28cb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728644"
---
# <a name="the-azure-defender-dashboard"></a>Azure Defender 仪表板

Azure Defender 仪表板提供：

- 跨不同资源类型的 Azure Defender 覆盖范围的可见性
- 用于配置高级威胁防护功能的链接
- 载入状态和代理安装
- Azure Defender 威胁检测警报 

若要访问 Azure Defender 仪表板，请从“安全中心”菜单的“云安全”部分中选择“Azure Defender”。

## <a name="whats-shown-on-the-dashboard"></a>仪表板上显示哪些内容？

:::image type="content" source="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png" alt-text="Azure Defender 仪表板示例。" lightbox="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png":::

该仪表板包含以下部分：

1. Azure Defender 覆盖范围 - 在这里，可以查看订阅中有资格受 Azure Defender 保护的资源类型。 在相关的地方，都有升级的选项。 如果要升级所有可能的符合条件的资源，请选择“升级全部”。

2. **安全警报** - 当 Azure Defender 区域检测到环境中任何区域出现威胁时，会生成警报。 这些警报会描述受影响资源的详细信息、建议的修正步骤，在某些情况下还会提供触发逻辑应用作为响应的选项。 在该图中选择任意位置将打开“安全警报”页面。

3. 高级保护 - Azure Defender 包括针对虚拟机、SQL 数据库、容器、Web 应用程序、网络等的许多高级威胁防护功能。 在此高级保护部分中，可以查看所选订阅中每种保护的资源状态。 选择其中任何一个可直接转到该保护类型的配置区域。

4. 见解 - 此滚动窗格包含新闻、推荐阅读和高优先级警报，提供安全中心有关你和你的订阅的紧迫性安全问题的见解。 无论是通过漏洞分析工具在 VM 上发现的高严重性 CVE 列表，还是由安全中心团队成员发布的新博客文章，都可以在 Azure Defender 仪表板的“见解”窗格中找到。




## <a name="next-steps"></a>后续步骤

本文介绍了 Azure Defender 仪表板。 

> [!div class="nextstepaction"]
> [启用 Azure Defender](enable-azure-defender.md)

有关 Azure Defender 的详细信息，请参阅 [Azure Defender 简介](azure-defender.md)