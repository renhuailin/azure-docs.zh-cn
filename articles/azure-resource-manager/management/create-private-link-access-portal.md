---
title: 创建用于管理资源的专用链接 - Azure 门户
description: 使用 Azure 门户创建用于管理资源的专用链接。
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: efe1fdb47bf550b996f4f44eed9f2d41f2a1083d
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227290"
---
# <a name="use-portal-to-create-private-link-for-managing-azure-resources"></a>使用门户创建用于管理 Azure 资源的专用链接

本文介绍如何使用 [Azure 专用链接](../../private-link/index.yml)来限制对订阅中资源的管理访问权限。 演示如何使用 Azure 门户通过专用访问设置资源管理。

[!INCLUDE [Create content](../../../includes/resource-manager-create-rmpl.md)]

## <a name="create-resource-management-private-link"></a>创建资源管理专用链接

创建资源管理专用链接时，系统会自动创建专用链接关联。

1. 在[门户](https://portal.azure.com)中搜索“资源管理专用链接”，然后在可用选项中选择它。

   :::image type="content" source="./media/create-private-link-access-portal/search.png" alt-text="搜索资源管理专用链接":::

1. 如果你的订阅还没有资源管理专用链接，你将看到一个空白页。 选择“创建资源管理专用链接”。

   :::image type="content" source="./media/create-private-link-access-portal/start-create.png" alt-text="为资源管理专用链接选择“创建”":::

1. 为新资源管理专用链接提供值。 所选目录的根管理组将用于新资源。 选择“查看 + 创建”  。

   :::image type="content" source="./media/create-private-link-access-portal/provide-values.png" alt-text="为资源管理专用链接指定值":::

1. 通过验证后，选择“创建”。

## <a name="create-private-endpoint"></a>创建专用终结点

现在，创建一个引用资源管理专用链接的专用终结点。

1. 导航到“专用链接中心”。 选择“创建专用链接”。

   :::image type="content" source="./media/create-private-link-access-portal/private-link-center.png" alt-text="选择“专用链接中心”":::

1. 在“基本信息”选项卡中，为专用终结点提供值。

   :::image type="content" source="./media/create-private-link-access-portal/private-endpoint-basics.png" alt-text="为基本信息提供值":::

1. 在“资源”选项卡中选择“连接到目录中的 Azure 资源” 。 对于“资源类型”，请选择 Microsoft.Authorization/resourceManagementPrivateLinks。 对于目标子资源，请选择 ResourceManagement。

   :::image type="content" source="./media/create-private-link-access-portal/private-endpoint-resource.png" alt-text="为资源提供值":::

1. 在“配置”选项卡中，选择你的虚拟网络。 建议与专用 DNS 区域集成。 选择“查看 + 创建”。

1. 通过验证后，选择“创建”。

## <a name="verify-private-dns-zone"></a>验证专用 DNS 区域

若要确保环境的配置正确，请检查 DNS 区域的本地 IP 地址。

1. 在部署了专用终结点的资源组中，选择名为 privatelink.azure.com 的专用 DNS 区域资源。

1. 验证名为 management 的记录集是否具有有效的本地 IP 地址。

   :::image type="content" source="./media/create-private-link-access-portal/verify.png" alt-text="验证本地 IP 地址":::

## <a name="next-steps"></a>后续步骤

若要详细了解专用链接，请参阅 [Azure 专用链接](../../private-link/index.yml)。