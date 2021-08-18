---
title: 删除“启动/停止 VM”v2（预览）概述
description: 本文介绍如何删除“启动/停止 VM”v2（预览）功能。
services: azure-functions
ms.subservice: start-stop-vms
ms.date: 06/25/2021
ms.topic: conceptual
ms.openlocfilehash: 1a22faba480eb9c130cc7c883abfc3e53ef58fe5
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112970150"
---
# <a name="how-to-remove-startstop-vms-v2-preview"></a>如何删除“启动/停止 VM”v2（预览）

在启用“启动/停止 VM”v2（预览）功能以管理 Azure VM 的运行状态后，可能需要决定停止使用该功能。 若要删除此功能，可以删除专用于存储以下资源以支持“启动/停止 VM”v2（预览）的资源组：

- Azure Functions 应用程序
- Azure 逻辑应用的日程安排
- Application Insights 实例
- Azure 存储帐户

> [!NOTE]
> 如果你在部署过程中遇到问题、在使用“启动/停止 VM v2 (预览版)”时遇到问题，或者有相关的问题，则可在 [GitHub](https://github.com/microsoft/startstopv2-deployments/issues) 上提交问题。 从 [Azure 支持站点](https://azure.microsoft.com/support/options/)提交 Azure 支持事件的功能不适用于此预览版本。 

## <a name="delete-the-dedicated-resource-group"></a>删除专用资源组

若要删除资源组，请按照 [Azure 资源管理器、资源组和资源删除](../../azure-resource-manager/management/delete-resource-group.md)一文所述的步骤进行操作。

## <a name="next-steps"></a>后续步骤

若要重新部署此功能，请参阅[部署启动/停止 v2](deploy.md)（预览）。