---
title: Azure 自动化使用的 Azure 数据中心 DNS 记录 | Microsoft Docs
description: 本文提供了 Azure 自动化功能在将通信限制为托管该自动化帐户的特定 Azure 区域时所需的 DNS 记录。
services: automation
ms.subservice: process-automation
ms.date: 06/29/2021
ms.topic: conceptual
ms.openlocfilehash: 04c7b6cc8cbcaaadf2002c5b7b00cdaab52913a6
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113491814"
---
# <a name="dns-records-for-azure-regions-used-by-azure-automation"></a>Azure 自动化使用的 Azure 区域的 DNS 记录

[Azure 自动化](../automation-intro.md)服务为各个功能使用多种 DNS 记录来连接到服务。 如果为特定的区域定义了自动化帐户，则可以限制与该区域数据中心之间的通信。 你可能需要了解这些记录，当以下自动化功能托管在防火墙后面时，以便它们能够正常运行：

* 混合 Runbook 辅助角色
* 状态配置
* Webhook

>[!NOTE]
>对于新纪录，Linux 混合 Runbook 辅助角色注册将失败，除非辅助角色的版本为 1.6.10.2 或更高。 为了使计算机能够接收辅助角色的更新版本并使用这些新记录，请务必升级到[适用于 Linux 的 Log Analytics 代理](../../azure-monitor/agents/agent-linux.md)的更新版本。 现有计算机将继续工作，而不会出现任何问题。  

## <a name="dns-records-per-region"></a>每个区域的 DNS 记录数

下表提供了每个区域的 DNS 记录。

>[!NOTE]
>尽管此处提供的一系列自动化 DNS 记录已经停用，但为了让你有时间迁移到[专用链接支持](#support-for-private-link)下列出的新记录并防止自动化过程失败，它们仍将有效。

| **区域** | **DNS 记录** |
| --- | --- |
| 澳大利亚中部 |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| 澳大利亚东部 |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| 澳大利亚东南部 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| 加拿大中部 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| 印度中部 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 美国东部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| 日本东部 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| 北欧 |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 美国中南部 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 东南亚 |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| 瑞士北部 |stzn-jobruntimedata-prod-su1.azure-automation.net</br>stzn-agentservice-prod-su1.azure-automation.net|
| 英国南部 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov 弗吉尼亚州 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| 美国中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 西欧 |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 美国西部 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

### <a name="support-for-private-link"></a>专用链接支持

为了在 Azure 自动化中支持[专用链接](../../private-link/private-link-overview.md)，现已更新每个受支持数据中心的 DNS 记录。 URL 不再特定于区域，而是特定于自动化帐户。

| **区域** | **DNS 记录** |
| --- | --- |
| 南非北部 |`https://<accountId>.webhook.san.azure-automation.net`<br>`https://<accountId>.agentsvc.san.azure-automation.net`<br>`https://<accountId>.jrds.san.azure-automation.net` |
| 东亚 |`https://<accountId>.webhook.ea.azure-automation.net`<br>`https://<accountId>.agentsvc.ea.azure-automation.net`<br>`https://<accountId>.jrds.ea.azure-automation.net` |
| 东南亚 |`https://<accountId>.webhook.sea.azure-automation.net`<br>`https://<accountId>.agentsvc.sea.azure-automation.net`<br>`https://<accountId>.jrds.sea.azure-automation.net` |
| 澳大利亚中部 |`https://<accountId>.webhook.ac.azure-automation.net`<br>`https://<accountId>.agentsvc.ac.azure-automation.net`<br>`https://<accountId>.jrds.ac.azure-automation.net` |
| 澳大利亚中部 2  |`https://<accountId>.webhook.cbr2.azure-automation.net`<br>`https://<accountId>.agentsvc.cbr2.azure-automation.net`<br>`https://<accountId>.jrds.cbr2.azure-automation.net` |
| 澳大利亚东南部 |`https://<accountId>.webhook.ase.azure-automation.net`<br>`https://<accountId>.agentsvc.ase.azure-automation.net`<br>`https://<accountId>.jrds.ase.azure-automation.net` |
| 澳大利亚东部 |`https://<accountId>.webhook.ae.azure-automation.net`<br>`https://<accountId>.agentsvc.ae.azure-automation.net`<br>`https://<accountId>.jrds.ae.azure-automation.net` |
| 巴西南部 |`https://<accountId>.webhook.brs.azure-automation.net`<br>`https://<accountId>.agentsvc.brs.azure-automation.net`<br>`https://<accountId>.jrds.brs.azure-automation.net` |
| 巴西东南部 |`https://<accountId>.webhook.brse.azure-automation.net`<br>`https://<accountId>.agentsvc.brse.azure-automation.net`<br>`https://<accountId>.jrds.brse.azure-automation.net` |
| 加拿大中部 |`https://<accountId>.webhook.cc.azure-automation.net`<br>`https://<accountId>.agentsvc.cc.azure-automation.net`<br>`https://<accountId>.jrds.cc.azure-automation.net` |
| 中国东部 2 |`https://<accountId>.webhook.sha2.azure-automation.cn`<br>`https://<accountId>.agentsvc.sha2.azure-automation.cn`<br>`https://<accountId>.jrds.sha2.azure-automation.cn` |
| 中国北部 |`https://<accountId>.webhook.bjb.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjb.azure-automation.cn`<br>`https://<accountId>.jrds.bjb.azure-automation.cn` |
| 中国北部 2 |`https://<accountId>.webhook.bjs2.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjs2.azure-automation.cn`<br>`https://<accountId>.jrds.bjs2.azure-automation.cn` |
| 西欧 |`https://<accountId>.webhook.we.azure-automation.net`<br>`https://<accountId>.agentsvc.we.azure-automation.net`<br>`https://<accountId>.jrds.we.azure-automation.net` |
| 北欧 |`https://<accountId>.webhook.ne.azure-automation.net`<br>`https://<accountId>.agentsvc.ne.azure-automation.net`<br>`https://<accountId>.jrds.ne.azure-automation.net` |
| 法国中部 |`https://<accountId>.webhook.fc.azure-automation.net`<br>`https://<accountId>.agentsvc.fc.azure-automation.net`<br>`https://<accountId>.jrds.fc.azure-automation.net` |
| 法国南部 |`https://<accountId>.webhook.mrs.azure-automation.net`<br>`https://<accountId>.agentsvc.mrs.azure-automation.net`<br>`https://<accountId>.jrds.mrs.azure-automation.net` |
| 德国中西部 |`https://<accountId>.webhook.dewc.azure-automation.de`<br>`https://<accountId>.agentsvc.dewc.azure-automation.de`<br>`https://<accountId>.jrds.dewc.azure-automation.de` |
| 印度中部 |`https://<accountId>.webhook.cid.azure-automation.net`<br>`https://<accountId>.agentsvc.cid.azure-automation.net`<br>`https://<accountId>.jrds.cid.azure-automation.net` |
| 印度南部  |`https://<accountId>.webhook.ma.azure-automation.net`<br>`https://<accountId>.agentsvc.ma.azure-automation.net`<br>`https://<accountId>.jrds.ma.azure-automation.net` |
| 日本东部 |`https://<accountId>.webhook.jpe.azure-automation.net`<br>`https://<accountId>.agentsvc.jpe.azure-automation.net`<br>`https://<accountId>.jrds.jpe.azure-automation.net` |
| 日本西部 |`https://<accountId>.webhook.jpw.azure-automation.net`<br>`https://<accountId>.agentsvc.jpw.azure-automation.net`<br>`https://<accountId>.jrds.jpw.azure-automation.net` |
| 韩国中部 |`https://<accountId>.webhook.kc.azure-automation.net`<br>`https://<accountId>.agentsvc.kc.azure-automation.net`<br>`https://<accountId>.jrds.kc.azure-automation.net` |
| 韩国南部 |`https://<accountId>.webhook.ps.azure-automation.net`<br>`https://<accountId>.agentsvc.ps.azure-automation.net`<br>`https://<accountId>.jrds.ps.azure-automation.net` |
| 挪威东部 |`https://<accountId>.webhook.noe.azure-automation.net`<br>`https://<accountId>.agentsvc.noe.azure-automation.net`<br>`https://<accountId>.jrds.noe.azure-automation.net` |
| 挪威西部  |`https://<accountId>.webhook.now.azure-automation.net`<br>`https://<accountId>.agentsvc.now.azure-automation.net`<br>`https://<accountId>.jrds.now.azure-automation.net` |
| 瑞士西部 |`https://<accountId>.webhook.stzw.azure-automation.net`<br>`https://<accountId>.agentsvc.stzw.azure-automation.net`<br>`https://<accountId>.jrds.stzw.azure-automation.net` |
| 阿联酋中部 |`https://<accountId>.webhook.auh.azure-automation.net`<br>`https://<accountId>.agentsvc.auh.azure-automation.net`<br>`https://<accountId>.jrds.auh.azure-automation.net` |
| 阿拉伯联合酋长国北部 |`https://<accountId>.webhook.uaen.azure-automation.net`<br>`https://<accountId>.agentsvc.uaen.azure-automation.net`<br>`https://<accountId>.jrds.uaen.azure-automation.net` |
| 英国西部 |`https://<accountId>.webhook.cw.azure-automation.net`<br>`https://<accountId>.agentsvc.cw.azure-automation.net`<br>`https://<accountId>.jrds.cw.azure-automation.net` |
| 英国南部 |`https://<accountId>.webhook.uks.azure-automation.net`<br>`https://<accountId>.agentsvc.uks.azure-automation.net`<br>`https://<accountId>.jrds.uks.azure-automation.net` |
| 美国中部 |`https://<accountId>.webhook.cus.azure-automation.net`<br>`https://<accountId>.agentsvc.cus.azure-automation.net`<br>`https://<accountId>.jrds.cus.azure-automation.net` |
| 美国东部 |`https://<accountId>.webhook.eus.azure-automation.net`<br>`https://<accountId>.agentsvc.eus.azure-automation.net`<br>`https://<accountId>.jrds.eus.azure-automation.net` |
| 美国东部 2 |`https://<accountId>.webhook.eus2.azure-automation.net`<br>`https://<accountId>.agentsvc.eus2.azure-automation.net`<br>`https://<accountId>.jrds.eus2.azure-automation.net` |
| 美国中北部 |`https://<accountId>.webhook.ncus.azure-automation.net`<br>`https://<accountId>.agentsvc.ncus.azure-automation.net`<br>`https://<accountId>.jrds.ncus.azure-automation.net` |
| 美国中南部 |`https://<accountId>.webhook.scus.azure-automation.net`<br>`https://<accountId>.agentsvc.scus.azure-automation.net`<br>`https://<accountId>.jrds.scus.azure-automation.net` |
| 美国中西部 |`https://<accountId>.webhook.wcus.azure-automation.net`<br>`https://<accountId>.agentsvc.wcus.azure-automation.net`<br>`https://<accountId>.jrds.wcus.azure-automation.net` |
| 美国西部 |`https://<accountId>.webhook.wus.azure-automation.net`<br>`https://<accountId>.agentsvc.wus.azure-automation.net`<br>`https://<accountId>.jrds.wus.azure-automation.net` |
| 美国西部 2 |`https://<accountId>.webhook.wus2.azure-automation.net`<br>`https://<accountId>.agentsvc.wus2.azure-automation.net`<br>`https://<accountId>.jrds.wus2.azure-automation.net` |
| 美国西部 3 |`https://<accountId>.webhook.usw3.azure-automation.net`<br>`https://<accountId>.agentsvc.usw3.azure-automation.net`<br>`https://<accountId>.jrds.usw3.azure-automation.net` |
| US Gov 弗吉尼亚州 |`https://<accountId>.webhook.usge.azure-automation.us`<br>`https://<accountId>.agentsvc.usge.azure-automation.us`<br>`https://<accountId>.jrds.usge.azure-automation.us` |
| US Gov 德克萨斯州 |`https://<accountId>.webhook.ussc.azure-automation.us`<br>`https://<accountId>.agentsvc.ussc.azure-automation.us`<br>`https://<accountId>.jrds.ussc.azure-automation.us` |
| US Gov 亚利桑那州 |`https://<accountId>.webhook.phx.azure-automation.us`<br>`https://<accountId>.agentsvc.phx.azure-automation.us`<br>`https://<accountId>.jrds.phx.azure-automation.us` |

将 DNS 记录中的 `<accountId>` 替换为在 URL 值中表示你的自动化帐户 ID 的 GUID。 你可从 Azure 门户“帐户设置”下的“密钥”中获取所需的 ID 。

![自动化帐户“主密钥”页面](./media/automation-region-dns-records/automation-account-keys.png)

从 URL 字段 (`https://<GUID>.agentsvc.<region>.azure-automation.net/accounts/<GUID>`) 中复制 accounts/ 之后的值

> [!NOTE]
> 所有 Webhook 和 agentservice DNS 记录均已更新为支持专用链接的新样式的 DNS 记录。 对于 JRDS DNS 记录，支持新旧样式的 DNS 记录。 如果未使用专用链接，则会看到旧样式的 DNS 记录，而使用专用链接的人将看到新样式的 DNS 记录。

建议在定义[异常](../automation-runbook-execution.md#exceptions)时使用列出的地址。 如需查看区域 IP 地址列表（而非区域名称列表），请从 Microsoft 下载中心下载以下云环境相应的 JSON 文件：

* [Azure IP 范围和服务标记 - Azure 公有云](https://www.microsoft.com/download/details.aspx?id=56519)
* [Azure IP 范围和服务标记 - Azure 政府云](https://www.microsoft.com/download/details.aspx?id=57063)
* [Azure IP 范围和服务标记 - Azure 德国云](https://www.microsoft.com/download/details.aspx?id=57064)
* [Azure IP 范围和服务标记 - Azure 中国世纪互联](https://www.microsoft.com/download/details.aspx?id=57062)

IP 地址文件列出了 Microsoft Azure 数据中心使用的 IP 地址范围。 它包括计算、SQL 和存储范围，并反映当前已部署的范围和任何即将对 IP 范围进行的更改。 数据中心至少在一周后才会使用文件中显示的新范围。

建议每周下载新的 IP 地址文件。 然后，更新网站以正确地标识 Azure 中运行的服务。

> [!NOTE]
> 如果使用的是 Azure ExpressRoute，请记住，IP 地址文件过去经常在每个月的第一周更新 Azure 空间的边界网关协议 (BGP) 播发。

## <a name="next-steps"></a>后续步骤

* 若要了解如何对混合 Runbook 辅助角色进行故障排除，请参阅[排查混合 Runbook 辅助角色问题](../troubleshoot/hybrid-runbook-worker.md#general)。

* 若要了解如何排查 State Configuration 问题，请参阅[排查 State Configuration 问题](../troubleshoot/desired-state-configuration.md)。