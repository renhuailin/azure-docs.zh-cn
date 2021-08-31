---
title: 自承载集成运行时诊断工具
description: 自承载集成运行时诊断工具
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
- devx-track-azurepowershell
ms.date: 07/28/2021
ms.openlocfilehash: b8aa070759ea29c9c2853d0c8a6af184bc169569
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778694"
---
# <a name="diagnostic-tool-for-self-hosted-integration-runtime"></a>自承载集成运行时诊断工具
自承载集成运行时是 Azure 数据工厂用于在不同的网络环境之间提供数据集成功能的计算基础结构。 安装自承载集成运行时需要在专用网络中提供一台本地计算机或虚拟机。 有时，很难调查本地计算机中的问题，例如网络、防火墙、依赖项或 OS 相关问题。 本文介绍用于排查本地环境中问题的新诊断工具。

该工具在自承载集成运行时计算机上运行一系列测试方案。 每个方案都有针对常见问题的典型运行状况检查案例。 如果客户遇到问题，可以触发“排查问题”功能。 该工具收集客户环境信息并执行运行状况检查案例。 

## <a name="get-started"></a>入门 
可以通过两种方法运行诊断工具来检测可能的问题：

- 在本地计算机上安装自承载集成运行时时，可以从配置管理器访问故障排除功能。 如果遇到问题，请选择“排查问题”以运行诊断工具。 安装后，还可以在“诊断”选项卡上选择同一选项。

   :::image type="content" source="./media/self-hosted-integration-runtime-diagnostic-tool/troubleshoot-ui.png" alt-text="屏幕截图显示如何使用运行时配置管理器排查问题。":::
   
- 也可以从命令行启动诊断工具:

   ```console
   dmgcmd.exe -ts [AUTH_KEY]
   ```

## <a name="diagnostic-result"></a>诊断结果
执行结果和详细信息日志消息作为 HTML 报表生成。 你可以查看错误，然后从报告中获取建议的缓解方法或公共文档 URL。

:::image type="content" source="./media/self-hosted-integration-runtime-diagnostic-tool/diagnostic-process.png" alt-text="屏幕截图显示诊断过程。":::

:::image type="content" source="./media/self-hosted-integration-runtime-diagnostic-tool/diagnostic-report.png" alt-text="屏幕截图显示诊断结果报表。":::

## <a name="next-steps"></a>后续步骤

- 查看 [Azure 数据工厂中的集成运行时概念](./concepts-integration-runtime.md)。

- 了解如何[在 Azure 门户中创建自承载集成运行时](./create-self-hosted-integration-runtime.md)。
