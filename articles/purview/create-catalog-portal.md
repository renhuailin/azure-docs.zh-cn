---
title: 快速入门：在 Azure 门户中创建 Azure Purview 帐户
description: 本快速入门介绍如何创建 Azure Purview 帐户并配置权限以开始使用它。
author: nayenama
ms.author: nayenama
ms.date: 08/18/2021
ms.topic: quickstart
ms.service: purview
ms.subservice: purview-data-catalog
ms.custom:
- mode-portal
ms.openlocfilehash: 4f0ef5010a0862b1fa5514d83f6570eefa2c4e10
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123102526"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>快速入门：在 Azure 门户中创建 Azure Purview 帐户

Azure Purview 是一种统一数据治理工具，可帮助你管理数据环境。 本快速入门介绍如何在 Azure 门户中创建 Azure Purview 帐户，以及如何在 Purview 中开始分类、保护和发现数据的过程！

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 用于登录 Azure 的用户帐户必须是 Azure 订阅的参与者、所有者或管理员。

* 你拥有 [Azure Active Directory 租户](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)。

* 没有任何阻止创建存储帐户或事件中心命名空间的 [Azure 策略](../governance/policy/overview.md)。 Purview 将在创建时部署托管存储帐户和事件中心。 如果存在阻止策略且需要保留，请按照我们的 [Purview 异常标记指南](create-purview-portal-faq.md)准备环境。

## <a name="create-an-azure-purview-account"></a>创建 Azure Purview 帐户

1. 在 [Azure 门户](https://portal.azure.com)中转到“Purview 帐户”页。

    :::image type="content" source="media/create-catalog-portal/purview-accounts-page.png" alt-text="屏幕截图显示 Azure 门户中的“Purview 帐户”页":::

1. 选择“创建”以创建新的 Azure Purview 帐户。

   :::image type="content" source="media/create-catalog-portal/select-create.png" alt-text="Azure 门户中的“Purview 帐户”页的屏幕截图，其中突出显示了“创建”按钮。":::
  
      或者，你可以转到市场，搜索“Azure Purview”，然后选择“创建”。

     :::image type="content" source="media/create-catalog-portal/search-marketplace.png" alt-text="屏幕截图显示 Azure 市场中的 Azure Purview，其中突出显示了“创建”按钮。":::

1. 在新的“创建 Purview 帐户”页上的“基本信息”选项卡下，选择要创建 Purview 帐户的 Azure 订阅。

1. 选择现有资源组或创建新的资源组来保存 Purview 帐户。

    若要详细了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group)一文。

1. 输入“Purview 帐户名称”。 不允许使用空格和符号。
    Purview 帐户的名称必须是全局唯一的。 如果显示以下错误，请更改 Purview 帐户的名称并再次尝试创建。

    :::image type="content" source="media/create-catalog-portal/name-error.png" alt-text="屏幕截图显示“创建 Purview 帐户”屏幕，其中指出帐户名称已在使用中并突出显示错误消息。":::

1. 选择位置。
    列表仅显示支持 Purview 的位置。 你选择的位置将作为 Purview 帐户和元数据的存储区域。 源可以存储在其他区域中。

      > [!Note]
      > Azure Purview 不支持跨区域移动帐户，因此请务必部署到正确的区域。 有关此内容的详细信息，请参阅[支持移动操作的资源](../azure-resource-manager/management/move-support-resources.md)。

1. 选择“查看和创建”，然后选择“创建” 。 需要几分钟才能完成创建。 新创建的 Azure Purview 帐户实例将显示在“Purview 帐户”页上的列表中。

    :::image type="content" source="media/create-catalog-portal/create-resource.png" alt-text="屏幕截图显示“创建 Purview 帐户”屏幕，其中突出显示了“查看 + 创建”按钮":::

## <a name="open-purview-studio"></a>打开 Purview Studio

创建 Azure Purview 帐户后，你将使用 Purview Studio 来访问和管理该帐户。 可通过两种方式打开 Purview Studio：

* 在 [Azure 门户](https://portal.azure.com)中打开 Purview 帐户。 在概述页上选择“打开 Purview Studio”磁贴。
    :::image type="content" source="media/create-catalog-portal/open-purview-studio.png" alt-text="屏幕截图显示 Purview 帐户概述页，其中突出显示了 Purview Studio 磁贴。":::

* 或者，你可以浏览到 [https://web.purview.azure.com](https://web.purview.azure.com)，选择 Purview 帐户，然后登录到工作区。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何创建 Azure Purview 帐户以及如何通过 Purview Studio 访问该帐户。

请按照接下来的这些文章学习如何导航 Purview Studio、创建集合以及授予对 Purview 的访问权限。

* [使用 Purview Studio](use-purview-studio.md)
* [创建集合](quickstart-create-collection.md)
* [将用户添加到 Azure Purview 帐户](catalog-permissions.md)
