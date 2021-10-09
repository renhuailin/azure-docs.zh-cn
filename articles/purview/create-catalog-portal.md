---
title: 快速入门：在 Azure 门户中创建 Purview 帐户
description: 本快速入门介绍如何创建 Azure Purview 帐户并配置权限以开始使用它。
author: nayenama
ms.author: nayenama
ms.date: 09/27/2021
ms.topic: quickstart
ms.service: purview
ms.custom:
- mode-portal
ms.openlocfilehash: c1a8b47df487cb3159ee1ac7492cf5eb04c1dec6
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217422"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>快速入门：在 Azure 门户中创建 Azure Purview 帐户

本快速入门介绍如何在 Azure 门户中创建 Azure Purview 帐户，以及如何在 Purview 中开始分类、保护和发现数据的过程！

Azure Purview 是一种数据管理服务，可帮助你管理和治理数据环境。 通过跨本地源、多云源和服务型软件 (SaaS) 源连接到数据，Purview 创建了最新的信息地图。 它对敏感数据进行标识和分类，并提供端到端的沿袭。 数据使用者能够发现整个组织的数据，而数据管理员能够审核数据、保护数据和确保对你的数据的正确使用。

有关 Purview 的详细信息，[请参阅概述页](overview.md)。 有关在组织中部署 Purview 的详细信息，[请参阅部署最佳做法](deployment-best-practices.md)。

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

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
