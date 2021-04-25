---
title: Azure Monitor 工作簿自带存储
description: 了解如何通过将工作簿内容保存到存储来保护工作簿
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: lagayhar
author: lgayhardt
ms.openlocfilehash: 16dbd7f7cd178a76b34b58f4bc6f9a0bc00fac73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100608498"
---
# <a name="bring-your-own-storage-to-save-workbooks"></a>自带存储来保存工作簿

有时你可能需要保护查询或某种业务逻辑。 工作簿提供了一个通过将工作簿内容保存到存储中来保护工作簿的选项。 你随后可以使用 Microsoft 管理的密钥加密存储帐户，也可以通过提供你自己的密钥来管理加密。 [有关存储服务加密，请参阅 Azure 文档。](../../storage/common/storage-service-encryption.md)

## <a name="saving-workbook-with-managed-identities"></a>使用托管标识保存工作簿

1. 在将工作簿保存到存储之前，需要创建一个托管标识（“所有服务”->“托管标识”），并授予它对存储帐户的“`Storage Blob Data Contributor`”访问权限。 [有关托管标识，请参阅 Azure 文档。](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

    [![屏幕截图，显示如何添加角色分配](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png)](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png#lightbox)

2. 创建新的工作簿。
3. 选择“保存”按钮来保存工作簿。
4. 有一个选项“`Save content to an Azure Storage Account`”，选中此复选框可将内容保存到 Azure 存储帐户。

    ![屏幕截图，显示“保存”对话框](./media/workbooks-bring-your-own-storage/saved-dialog-default.png)

5. 选择所需的“存储帐户”和“容器”。 “存储帐户”列表来自上面选定的“订阅”。

    ![屏幕截图显示带存储选项的“保存”对话框](./media/workbooks-bring-your-own-storage/save-dialog-with-storage.png)

6. 然后，选择“更改”以选择之前创建的托管标识。

    [![屏幕截图显示“更改标识”对话框](./media/workbooks-bring-your-own-storage/change-managed-identity.png)](./media/workbooks-bring-your-own-storage/change-managed-identity.png#lightbox)

7. 选择存储选项后，请按“保存”以保存工作簿。

## <a name="limitations"></a>限制

- 保存到自定义存储时，不能将工作簿的各个部分固定到仪表板上，因为各个固定点会将受保护信息包含在仪表板本身中。 使用自定义存储时，只能将指向工作簿本身的链接固定到仪表板。
- 工作簿保存到自定义存储后，它将始终保存到自定义存储中。你无法将此功能关闭。 若要保存到其他位置，可以使用“另存为”并选择不将副本保存到自定义存储。
- Application Insights 资源中的工作簿是“旧版”工作簿，不支持自定义存储。 Application Insights 资源中的最新工作簿是“...更多”选项。 保存时，旧版工作簿没有“订阅”选项。

   ![屏幕截图，显示旧版工作簿](./media/workbooks-bring-your-own-storage/legacy-workbooks.png)

## <a name="next-steps"></a>后续步骤

- 了解如何在工作簿中创建[地图](workbooks-map-visualizations.md)可视化效果。
- 了解如何使用[工作簿中的组](../visualize/workbooks-groups.md)。