---
title: Azure Monitor 工作簿模板 - 移动区域
description: 如何将工作簿模板移动到其他区域
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: 9787118b1912a5697c02e116be99b1f396748937
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100606024"
---
# <a name="move-an-azure-workbook-template-to-another-region"></a>将 Azure 工作簿模板移动到另一区域

本文介绍如何将 Azure 工作簿模板资源移动到其他 Azure 区域。 由于各种原因，你可能需要将资源移动到其他区域。 例如，利用新的 Azure 区域，部署仅在特定区域中可用的功能或服务，满足内部策略和监管要求，或者满足容量规划要求。

目前没有门户 UI 用于创建工作簿模板资源，目前只能[通过 Azure 资源管理器模板（ARM 模板）部署](../visualize/workbooks-automate.md)进行创建。 因此，若要移动模板，最简单的方法是重复使用之前的 ARM 模板并更新它以部署到新区域。

## <a name="prerequisites"></a>先决条件

* 确保目标区域支持工作簿模板。

## <a name="prepare"></a>准备

* 确定之前用过的用于工作簿模板的 ARM 模板。

## <a name="move"></a>移动

1. 更新之前用过的模板以引用新区域。

   > [!NOTE]
   > 可能需要为工作簿模板使用新名称，避免出现任何重复名称。

2. 通过 ARM 模板部署来部署更新的模板，以在所需区域中创建新的工作簿模板。

## <a name="verify"></a>Verify

使用 Azure 工作簿浏览 UI 查找新部署的工作簿模板。 确保该位置是目标位置。

## <a name="clean-up"></a>清理

在新区域中创建工作簿模板后，删除先前区域中的原始工作簿模板。
1. 在 Azure 工作簿浏览 UI 中查找工作簿模板。
2. 选择要删除的工作簿模板。
3. 选择“删除”命令。

如果你为了将工作簿模板导入新区域而对其进行了重命名，可在删除原始项后，使用 Azure 工作簿模板资源视图中的“重命名”命令，将工作簿模板重命名为以前的名称。

## <a name="next-steps"></a>后续步骤

需要移动工作簿而不是模板？ 请参阅如何[将 Azure 工作簿移动到另一个区域](./workbooks-move-region.md)。

