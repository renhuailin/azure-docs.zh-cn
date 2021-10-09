---
title: 为 Azure VMware 解决方案配置 VMware Syslog
description: 了解如何配置诊断设置以收集 Azure VMware 解决方案私有云的 VMware Syslog。
ms.topic: how-to
ms.date: 09/24/2021
ms.openlocfilehash: 983a62eb5b094c94048e7580fd53558df002d816
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699040"
---
# <a name="configure-vmware-syslogs-for-azure-vmware-solution"></a>为 Azure VMware 解决方案配置 VMware Syslog

诊断设置用于将资源的平台日志和指标流式导出配置到你选择的目标位置。 最多可以创建五个不同的诊断设置，将不同的日志和指标发送到独立的目标位置。 

在此主题中，你将配置诊断设置以收集 Azure VMware 解决方案私有云的 VMware Syslog。 将 syslog 存储到存储帐户，以查看 vCenter 日志并进行分析以用于诊断目的。 

## <a name="prerequisites"></a>先决条件

确保你具有有权访问 vCenter 和 NSX-T Manager 界面的 Azure VMware 解决方案私有云。 

## <a name="configure-diagnostic-settings"></a>配置诊断设置

1. 从 Azure VMware 解决方案私有云中，选择“诊断设置”，然后选择“添加诊断设置”。 
 
   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-1.png" alt-text="显示在何处配置 VMware syslog 的屏幕截图。" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-1.png":::


1. 依次选择“vmwaresyslog”、“Allmetrics”和“存档到存储帐户”选项。  

   >[!IMPORTANT]
   >“发送到日志分析工作区”选项目前不起作用。
 
1. 选择要存储日志的存储帐户，然后选择“保存”。

   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-2.png" alt-text="显示存储 syslog 时可供选择的选项的屏幕截图。" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-2.png":::

1. 转到你的存储和帐户，验证是否已创建“Insight 日志 vmwarelog”并选择此日志。 
 
   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-3.png" alt-text="显示“Insight 日志 vmwarelog”选项已创建且可用的屏幕截图。" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-3.png":::


1. 浏览到该位置并下载 json 文件以查看日志。

   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-4.png" alt-text="显示 json 文件的向下钻取路径的屏幕截图。" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-4.png"::: 

