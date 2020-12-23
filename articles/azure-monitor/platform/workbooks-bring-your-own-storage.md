---
title: Azure Monitor 工作簿携带自己的存储
description: 了解如何通过将工作簿内容保存到存储来保护工作簿
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: lagayhar
author: lgayhardt
ms.openlocfilehash: 09ea29d8149298f906672979ea76da0558a2934c
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371099"
---
# <a name="bring-your-own-storage-to-save-workbooks"></a>自带存储以保存工作簿

有时，您可能有要保护的查询或一些业务逻辑。 工作簿提供了一个选项，可通过将工作簿内容保存到存储来保护工作簿。 然后，可以使用 Microsoft 管理的密钥加密存储帐户，也可以通过提供自己的密钥来管理加密。 [有关存储服务加密，请参阅 Azure 文档。](../../storage/common/storage-service-encryption.md)

## <a name="saving-workbook-with-managed-identities"></a>用托管标识保存工作簿

1. 在将工作簿保存到存储之前，需要创建一个托管标识 (所有服务 > 托管标识) ，并向其授予对 `Storage Blob Data Contributor` 存储帐户的访问权限。 [有关托管标识，请参阅 Azure 文档。](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

    [![显示添加角色分配的屏幕截图](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png)](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png#lightbox)

2. 创建新的工作簿。
3. 选择 " **保存** " 按钮以保存工作簿。
4. 有一个选项 `Save content to an Azure Storage Account` ，选中此复选框以保存到 Azure 存储帐户。

    ![显示已保存对话框的屏幕截图](./media/workbooks-bring-your-own-storage/saved-dialog-default.png)

5. 选择所需的存储帐户和容器。 存储帐户列表来自上面所选的订阅。

    ![显示带有存储选项的 "保存" 对话框的屏幕截图](./media/workbooks-bring-your-own-storage/save-dialog-with-storage.png)

6. 然后选择 " **更改** " 以选择之前创建的托管标识。

    [![显示 "更改标识" 对话框的屏幕截图](./media/workbooks-bring-your-own-storage/change-managed-identity.png)](./media/workbooks-bring-your-own-storage/change-managed-identity.png#lightbox)

7. 选择存储选项后，按 " **保存** " 以保存工作簿。

## <a name="limitations"></a>限制

- 保存到自定义存储时，无法将工作簿的各个部分固定到仪表板，因为单个 pin 会在仪表板本身中包含受保护的信息。 使用自定义存储时，只能将工作簿自身的链接固定到仪表板。
- 将工作簿保存到自定义存储后，它将始终保存到自定义存储，并且无法关闭。 若要保存到其他位置，可以使用 "另存为" 并选择不将副本保存到自定义存储。
- Application Insights 资源中的工作簿是 "旧的" 工作簿，不支持自定义存储。 Application Insights 资源中的最新工作簿是 ".。。更多 "选择。 保存时，旧工作簿没有订阅选项。

   ![显示旧版工作簿的屏幕截图](./media/workbooks-bring-your-own-storage/legacy-workbooks.png)

## <a name="next-steps"></a>后续步骤

- 了解如何在工作簿中创建 [地图](workbooks-map-visualizations.md) 可视化效果。
- 了解如何 [在工作簿中使用组](workbooks-groups.md)。