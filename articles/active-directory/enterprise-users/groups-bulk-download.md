---
title: 在 Azure Active Directory 门户中下载组列表 | Microsoft Docs
description: 在 Azure Active Directory 中的 Azure 管理中心批量下载组属性。
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 09/01/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32000f69719b957c9543180bbd118f93ed0fda04
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430677"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>在 Azure Active Directory 中批量下载组列表

使用 Azure Active Directory (Azure AD) 门户，可将组织中的所有组列表批量下载到以逗号分隔的值 (CSV) 文件。

## <a name="to-download-a-list-of-groups"></a>若要下载组列表

1. 使用组织中的管理员帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在 Azure AD 中，选择“组” > “下载组” 。
1. 在“组下载”页面上，选择“开始”以接收列举组的 CSV 文件。

   ![下载组命令位于“所有组”页面上](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>检查下载状态

可在“批量操作结果”页面中查看所有挂起的批量请求的状态。

[![在“批量操作结果”页面中查看状态。](./media/groups-bulk-download/bulk-center.png)](./media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>批量下载服务限制

下载组列表的每个批量活动最多可运行一小时。 这使你可以下载至少包含 300000 万个组的列表。

## <a name="next-steps"></a>后续步骤

- [批量删除组成员](groups-bulk-remove-members.md)
- [下载组成员](groups-bulk-download-members.md)
