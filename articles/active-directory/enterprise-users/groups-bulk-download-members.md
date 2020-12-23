---
title: 大容量下载组成员身份列表-Azure Active Directory 门户 |Microsoft Docs
description: 在 Azure 管理中心中批量添加用户。
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4055fb3bfdbcfd7e4a74d9290017a6193578146
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547690"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>在 Azure Active Directory 中批量下载组的成员

使用) 门户 Azure AD Azure Active Directory (，你可以将组织中组的成员批量下载到逗号分隔值 (CSV) 文件。

## <a name="to-bulk-download-group-membership"></a>大容量下载组成员身份

1. 使用组织中的用户管理员帐户登录到 [Azure 门户](https://portal.azure.com)。 组所有者还可以批量下载它们所拥有的组的成员。
1. 在 Azure AD 中，选择“组” > “所有组” 。
1. 打开要下载其成员身份的组，然后选择 " **成员**"。
1. 在 " **成员** " 页上，选择 " **下载成员** " 以下载列出组成员的 CSV 文件。

   !["下载成员" 命令位于组的配置文件页上](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>检查下载状态

可在“批量操作结果”页面中查看所有挂起的批量请求的状态。

[![在“批量操作结果”页面中查看状态。](./media/groups-bulk-download-members/bulk-center.png)](./media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>批量下载服务限制

用于下载组成员列表的每个批量活动最多可运行一小时。 这使你可以下载至少500000个成员的列表。

## <a name="next-steps"></a>后续步骤

- [批量导入组成员](groups-bulk-import-members.md)
- [批量删除组成员](groups-bulk-download-members.md)
