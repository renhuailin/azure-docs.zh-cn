---
title: 删除“启动/停止 VM v2（预览）”概述
description: 本文介绍如何删除“启动/停止 VM v2（预览）”功能。
services: azure-functions
ms.subservice: ''
ms.date: 03/30/2021
ms.topic: conceptual
ms.openlocfilehash: b4308be8f7494c1cb6f6b4839fc5a2e9717668e3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110878"
---
# <a name="how-to-remove-startstop-vms-v2-preview"></a>如何删除“启动/停止 VM v2（预览）”

在启用“启动/停止 VM v2（预览）”功能来管理 Azure VM 的运行状态后，会有决定停止使用的可能。 若要删除此功能，可以删除专用于存储以下资源以支持“启动/停止 VM v2（预览）”的资源组：

- Azure Functions 应用程序
- Azure 逻辑应用的日程安排
- Application Insights 实例
- Azure 存储帐户

## <a name="delete-the-dedicated-resource-group"></a>删除专用资源组

若要删除资源组，请按照 [Azure 资源管理器、资源组和资源删除](../../azure-resource-manager/management/delete-resource-group.md)一文所述的步骤进行操作。

## <a name="next-steps"></a>后续步骤

若要重新部署此功能，请参阅[部署开始/停止 v2](deploy.md)（预览）。