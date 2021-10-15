---
title: 使用 Azure Sentinel 时有用的资源
description: 本文档提供使用 Azure Sentinel 时有用资源的列表。
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: yelevin
ms.openlocfilehash: 47ccfea5b666e3c600454fec5d2e8dc801b78bc2
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358107"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>对使用 Azure Sentinel 有用的资源

本文列出了对获取有关使用 Azure Sentinel 的详细信息有用的资源。

## <a name="learn-more-about-creating-queries"></a>了解有关创建查询的详细信息

Azure Sentinel 使用 Azure Monitor Log Analytics 的 Kusto 查询语言 (KQL) 来生成查询。 有关详细信息，请参阅：

- [KQL 概念](/azure/data-explorer/kusto/concepts/)
- [KQL 查询](/azure/data-explorer/kusto/query/)
- [KQL 快速参考指南](/azure/data-explorer/kql-quick-reference)
- [KQL 查询入门](../azure-monitor/logs/get-started-queries.md)

## <a name="learn-more-about-creating-automation"></a>了解有关创建自动化的详细信息

使用 Azure 逻辑应用在 Azure Sentinel 中创建自动化，以应对不断扩大的内置 Playbook 库。 

有关详细信息，请参阅 [Azure 逻辑应用连接器](/connectors/)。

## <a name="compare-playbooks-workbooks-and-notebooks"></a>比较 playbook、工作簿和笔记本

下表说明了 Azure Sentinel 中的 playbook、工作簿和笔记本之间的区别：

| 类别 |攻略  |工作簿  |笔记本  |
|---------|---------|---------|---------|
|**角色**     |   <ul><li>SOC 工程师</li><li>所有层级的分析师</li></ul>      | <ul><li> SOC 工程师</li><li>所有层级的分析师</li></ul>       | <ul><li>威胁搜寻者和第 2 层/第 3 层分析师</li><li>事件调查者</li><li>数据科学家</li><li>安全研究人员</li></ul>       |
|**使用**     | 自动完成简单的可重复任务：<ul><li>引入外部数据 </li><li>使用 TI、GeoIP 查找等功能进行数据扩充 </li><li> 调查 </li><li>补救 </li></ul>       | <ul><li>可视化效果</li></ul>        |   <ul><li>查询 Azure Sentinel 数据和外部数据 </li><li>使用 TI、GeoIP 查找、WhoIs 查找等功能进行数据扩充 </li><li> 调查 </li><li> 可视化效果 </li><li> 搜寻 </li><li>机器学习和大数据分析 </li></ul>      |
|**优点**     |<ul><li> 最适合单个可重复任务 </li><li>无需编码知识  </li></ul>      |<ul><li>最适合 Azure Sentinel 数据的概要视图 </li><li>无需编码知识</li></ul>       | <ul><li>最适合复杂的可重复任务链 </li><li>临时使用，更多过程控制</li><li>可以使用交互功能更轻松地进行透视 </li><li>丰富的 Python 库，适用于数据操作和可视化 </li><li>机器学习和自定义分析 </li><li>易于记录和共享分析证据 </li></ul>       |
|**挑战**     | <ul><li>不适合临时的和复杂的任务链 </li><li>不适合记录和共享证据</li></ul>        |   <ul><li>不能与外部数据集成 </li></ul>     |    <ul><li> 跨越式的学习曲线，需要编码知识 </li></ul>   |
|  **详细信息**   | [在 Azure Sentinel 中使用 playbook 实现威胁响应自动化](automate-responses-with-playbooks.md)        | [可视化收集的数据](get-visibility.md)        | [使用 Jupyter 笔记本搜寻安全威胁](notebooks.md)        |
|     |         |         |         |

## <a name="comment-on-our-blogs-and-forums"></a>在博客和论坛上发表评论

我们乐于倾听用户的声音。

在 Azure Sentinel 的 TechCommunity 空间中：

- [查看并评论最近的博客文章](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog)
- [发布自己有关 Azure Sentinel 的问题](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel)

还可以通过[用户之声](https://feedback.azure.com/forums/920458-azure-sentinel)计划发送有关改进的建议。

## <a name="join-the-azure-sentinel-github-community"></a>加入 Azure Sentinel GitHub 社区

[Azure Sentinel GitHub 存储库](https://github.com/Azure/Azure-Sentinel)提供有关威胁检测和自动化的强有力资源。 

Microsoft 安全分析师会不断创建和添加新的工作簿、Playbook、搜寻式查询及其他资源，并将其发布到社区，供你在环境中使用。 

从个人社区 GitHub 存储库下载示例内容，以创建适用于 Azure Sentinel 的自定义工作簿、搜寻式查询、Notebook 和 Playbook。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [获得认证！](/learn/paths/security-ops-sentinel/)

> [!div class="nextstepaction"]
> [阅读客户用例情景](https://customers.microsoft.com/en-us/search?sq=%22Azure%20Sentinel%20%22&ff=&p=0&so=story_publish_date%20desc)