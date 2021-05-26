---
title: 删除“启动/停止 VM”v2（预览）概述
description: 本文介绍如何删除“启动/停止 VM”v2（预览）功能。
services: azure-functions
ms.subservice: start-stop-vms
ms.date: 03/30/2021
ms.topic: conceptual
ms.openlocfilehash: fd24de679781cda37ba9489eb7acad6332896659
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110076845"
---
# <a name="how-to-remove-startstop-vms-v2-preview"></a>如何删除“启动/停止 VM”v2（预览）

在启用“启动/停止 VM”v2（预览）功能以管理 Azure VM 的运行状态后，可能需要决定停止使用该功能。 若要删除此功能，可以删除专用于存储以下资源以支持“启动/停止 VM”v2（预览）的资源组：

- Azure Functions 应用程序
- Azure 逻辑应用的日程安排
- Application Insights 实例
- Azure 存储帐户

## <a name="delete-the-dedicated-resource-group"></a>删除专用资源组

若要删除资源组，请按照 [Azure 资源管理器、资源组和资源删除](../../azure-resource-manager/management/delete-resource-group.md)一文所述的步骤进行操作。

## <a name="next-steps"></a>后续步骤

若要重新部署此功能，请参阅[部署启动/停止 v2](deploy.md)（预览）。