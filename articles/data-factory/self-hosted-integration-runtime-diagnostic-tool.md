---
title: 自承载集成运行时诊断工具
description: 适用于自承载集成运行时的诊断工具
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
- devx-track-azurepowershell
ms.date: 07/28/2021
ms.openlocfilehash: b3c98d4e85b1a7d04b017eea2da00f36a479eeac
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598568"
---
# <a name="diagnostic-tool-for-self-hosted-integration-runtime"></a>适用于自承载集成运行时的诊断工具
自承载集成运行时是 Azure 数据工厂用于在不同的网络环境之间提供数据集成功能的计算基础结构。 安装自承载集成运行时需要在专用网络中提供一台本地计算机或虚拟机。 有时，很难调查本地计算机中的问题，例如网络、防火墙、依赖项或 OS 相关问题。 本文介绍用于排查本地环境中问题的新诊断工具。

该工具在自承载集成运行时计算机上运行一系列测试方案。 每个方案都有针对常见问题的典型运行状况检查案例。 如果客户遇到问题，可以触发“排查问题”功能。 该工具收集客户环境信息并执行运行状况检查案例。 

## <a name="get-started"></a>入门 
可以通过两种方法运行诊断工具来检测可能的问题：

- 在本地计算机上安装自承载集成运行时时，可以从配置管理器访问故障排除功能。 如果遇到问题，请选择“故障排除”以运行诊断工具。 你还可以在安装后在“诊断”选项卡上选择此相同选项。

   :::image type="content" source="./media/self-hosted-integration-runtime-diagnostic-tool/troubleshoot-ui.png" alt-text="显示如何使用运行时 Configuration Manager 解决问题的屏幕截图。":::
   
- 还可以从命令行启动诊断工具：

   ```console
   dmgcmd.exe -ts [AUTH_KEY]
   ```

## <a name="diagnostic-result"></a>诊断结果
执行结果和详细日志消息以 HTML 报告的形式生成。 你可以查看错误并从报告中获取建议的缓解方法或公共文档 URL。

:::image type="content" source="./media/self-hosted-integration-runtime-diagnostic-tool/diagnostic-process.png" alt-text="显示诊断过程的屏幕截图。":::

:::image type="content" source="./media/self-hosted-integration-runtime-diagnostic-tool/diagnostic-report.png" alt-text="显示诊断结果报告的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

- 查看 [Azure 数据工厂中的集成运行时概念](./concepts-integration-runtime.md)。

- 了解如何[在 Azure 门户中创建自承载集成运行时](./create-self-hosted-integration-runtime.md)。
