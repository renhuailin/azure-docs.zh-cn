---
title: include 文件
description: include 文件
services: backup
author: v-amallick
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 06/01/2021
ms.author: v-amallick
ms.custom: include file
ms.openlocfilehash: 7d354ce3e69f075d2f523a75ba96f9a6efb7d797
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "110783558"
---
## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库是一个管理实体，它会存储一段时间内创建的恢复点，并提供用于执行备份相关操作的接口。 其中包括按需备份、执行还原和创建备份策略。

若要创建恢复服务保管库，请执行以下步骤。

1. 在 [Azure 门户](https://portal.azure.com/)中登录到自己的订阅。

1. 在 Azure 门户中搜索“备份中心”，并导航到“备份中心”仪表板。

    ![选择“备份中心”](../includes/media/backup-create-rs-vault/backup-center-search-backup-center.png)

1. 从“概述”选项卡中选择“+保管库”。

    ![创建保管库](./media/backup-create-rs-vault/backup-center-create-vault.png) 

1. 选择“恢复服务保管库”，然后单击“继续”。

    ![选择恢复服务保管库](./media/backup-create-rs-vault/backup-center-select-recovery-services-vault.png) 

1. 此时会打开“恢复服务保管库”对话框。 提供“名称”、“订阅”、“资源组”和“位置”的值   。

    ![配置恢复服务保管库](./media/backup-create-rs-vault/backup-center-add-vault-details.png)

   - **名称**：输入一个友好名称以标识此保管库。 名称对于 Azure 订阅必须是唯一的。 指定的名称应至少包含 2 个字符，最多不超过 50 个字符。 名称必须以字母开头且只能包含字母、数字和连字符。
   - **订阅**：选择要使用的订阅。 如果你仅是一个订阅的成员，则会看到该名称。 如果不确定要使用哪个订阅，请使用默认的（建议的）订阅。 仅当工作或学校帐户与多个 Azure 订阅关联时，才会显示多个选项。
   - **资源组**：使用现有资源组，或创建一个新的资源组。 若要查看订阅中可用的资源组列表，请选择“使用现有资源”，然后从下拉列表中选择一个资源。 若要创建新资源组，请选择“新建”，然后输入名称。 有关资源组的详细信息，请参阅 [Azure 资源管理器概述](../articles/azure-resource-manager/management/overview.md)。
   - **位置**：为保管库选择地理区域。 若要创建保管库来保护任何数据源，该保管库必须位于数据源所在的区域。

      > [!IMPORTANT]
      > 如果不确定数据源的位置，请关闭该对话框。 在门户中访问你的资源列表。 如果数据源位于多个区域中，请为每个区域创建恢复服务保管库。 先在第一个位置创建保管库，然后再为其他位置创建保管库。 无需指定存储帐户即可存储备份数据。 恢复服务保管库和 Azure 备份会自动处理这种情况。
      >
      >

1. 提供值后，选择“查看 + 创建”。

    ![该屏幕截图显示在“创建恢复服务保管库”过程中的“查看 + 创建”按钮。](./media/backup-create-rs-vault/review-and-create.png)

1. 准备好创建恢复服务保管库后，选择“创建”。

    ![创建恢复服务保管库](./media/backup-create-rs-vault/click-create-button.png)

    创建恢复服务保管库可能需要一段时间。 可在门户右上角“通知”区域监视状态通知。 创建保管库后，它会显示在“恢复服务保管库”的列表中。 如果未看到创建的保管库，请选择“刷新”。

     ![刷新备份保管库列表](./media/backup-create-rs-vault/refresh-button.png)

>[!IMPORTANT]
> 强烈建议先查看“存储复制类型”和“安全设置”的默认设置，再在保管库中配置备份。  有关详细信息，请参阅[设置存储冗余](../articles/backup/backup-create-rs-vault.md#set-storage-redundancy)部分。