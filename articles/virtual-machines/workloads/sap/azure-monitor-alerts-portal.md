---
title: 使用 Azure 门户在 Azure Monitor for SAP Solutions 中配置警报
description: 了解如何使用浏览器方法在 Azure Monitor for SAP Solutions 中配置警报。
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 08/30/2021
ms.openlocfilehash: c41674763a417f0060206b365a99fcc4bf680b67
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123256128"
---
# <a name="configure-alerts-in-azure-monitor-for-sap-solutions-by-using-the-azure-portal"></a>使用 Azure 门户在 Azure Monitor for SAP Solutions 中配置警报

本文介绍在 Azure Monitor for SAP Solutions 中配置警报的步骤。 我们将通过 [Azure 门户](https://azure.microsoft.com/features/azure-portal)使用其基于浏览器的界面配置警报和通知。

## <a name="prerequisites"></a>先决条件

部署包含至少一个提供程序的 Azure Monitor for SAP Solutions 资源。 为以下资源配置提供程序： 
- SAP 应用程序 (NetWeaver)
- SAP HANA
- Microsoft SQL Server
- 高可用性 (pacemaker) 群集

## <a name="sign-in-to-the-portal"></a>登录门户

登录 [Azure 门户](https://portal.azure.com)。

## <a name="create-an-alert-rule"></a>创建警报规则

1.  在 Azure 门户，浏览并选择 Azure Monitor for SAP Solutions 资源。 确保为此资源至少配置了一个提供程序。 
2.  导航到所选的工作簿。 例如 SAP HANA，并选择一个 HANA 实例。

    :::image type="content" source="./media/ams-alerts/ams-alert-1.png" alt-text="显示“警报”按钮位置的屏幕截图。" lightbox="./media/ams-alerts/ams-alert-1.png":::
  
3.  选择“警报”按钮以查看可用的警报模板 。

    :::image type="content" source="./media/ams-alerts/ams-alert-2.png" alt-text="显示警报模板列表的屏幕截图。" lightbox="./media/ams-alerts/ams-alert-2.png":::
    
4.  选择“创建规则”以配置所选的警报。
5.  输入警报阈值，选择“提供程序实例”，然后选择或创建“操作组”以配置通知设置  。 可根据需求编辑频率和严重性信息。

    >[!Tip]
    > 详细了解[操作组](../../../azure-monitor/alerts/action-groups.md)。 
    
7.  选择“启用警报规则”。

    :::image type="content" source="./media/ams-alerts/ams-alert-3.png" alt-text="显示警报配置页的屏幕截图。" lightbox="./media/ams-alerts/ams-alert-3.png":::
    
7.  选择“部署警报规则”，完成警报规则配置。 通过单击“查看模板”，可选择查看警报模板。

    :::image type="content" source="./media/ams-alerts/ams-alert-4.png" alt-text="显示警报配置最后一个步骤的屏幕截图。" lightbox="./media/ams-alerts/ams-alert-4.png":::
    
8.  导航到“警报规则”，查看新创建的警报规则。 可在“触发的警报”下查看何时以及是否触发警报。

    :::image type="content" source="./media/ams-alerts/ams-alert-5.png" alt-text="显示警报配置结果的屏幕截图。" lightbox="./media/ams-alerts/ams-alert-5.png":::

## <a name="next-steps"></a>后续步骤

详细了解 Azure Monitor for SAP Solutions。

> [!div class="nextstepaction"]
> [监视 Azure 上的 SAP](monitor-sap-on-azure.md)
