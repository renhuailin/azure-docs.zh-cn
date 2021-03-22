---
title: 使用 Azure 存储帐户 SAS 密钥导出成本数据
description: 本文帮助合作伙伴创建 SAS 密钥并配置成本管理导出。
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 16302a6bcc3bf41432500d2fdaa4ec27c28dd5b3
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509612"
---
# <a name="export-cost-data-with-an-azure-storage-account-sas-key"></a>使用 Azure 存储帐户 SAS 密钥导出成本数据

以下信息仅适用于 Microsoft 合作伙伴。

通常，合作伙伴在与其自己的 Microsoft 合作伙伴协议关联的租户中没有自己的 Azure 订阅。 具有 Microsoft 合作伙伴协议计划且是其计费帐户全局管理员的合作伙伴可以使用共享访问服务 (SAS) 密钥，将成本数据导出和复制到其他租户中的存储帐户。 换言之，合作伙伴可以借助一个带有 SAS 密钥的存储帐户，使用其合作伙伴协议外部的存储帐户来接收导出的信息。 本文帮助合作伙伴创建 SAS 密钥并配置成本管理导出。

## <a name="requirements"></a>要求

- 你必须是签订了 Microsoft 合作伙伴协议的合作伙伴，并有 Azure 计划客户。
- 你必须是合作伙伴组织的计费帐户的全局管理员。
- 你必须拥有所需的访问权限，可在合作伙伴组织的其他租户中配置存储帐户。 将数据导出到存储帐户时，你负责维护权限和数据访问权限。

## <a name="configure-azure-storage-with-a-sas-key"></a>为 Azure 存储配置 SAS 密钥

获取存储帐户 SAS 令牌，或使用 Azure 门户创建一个令牌。 若要在 Azure 门户中创建，请使用以下步骤。 若要详细了解 SAS 密钥，请参阅[使用共享访问签名 (SAS) 授予有限的数据访问权限。](../../storage/common/storage-sas-overview.md)

1. 在 Azure 门户中导航到存储帐户。
    - 如果你的帐户有权访问多个租户，请切换目录以访问存储帐户。 在 Azure 门户的右上角选择你的帐户，然后选择“切换目录”。
    - 可能需要使用相应的租户帐户登录到 Azure 门户才能访问存储帐户。
1. 在左侧菜单中选择“共享访问签名”。  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" alt-text="显示已配置的 Azure 存储共享访问签名的屏幕截图。" lightbox="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" :::
1. 使用上图中所示的相同设置配置令牌。
    1. 选择“Blob”作为“允许的服务”。
    1. 选择“服务”、“容器”和“对象”作为“允许的资源类型”。  
    1. 选择“读取”、“写入”、“删除”、“列出”、“添加”和“创建”作为“允许的权限”。     
    1. 选择过期日期和时间。 请务必在 SAS 导出令牌过期之前将其更新。 配置的过期时限越长，在需要新 SAS 令牌之前可以运行导出的时间就越长。
1. 选择“仅 HTTPS”作为“允许的协议”。
1. 选择“基本”作为“首选路由层”。
1. 选择“密钥 1”作为“签名密钥”。 如果轮换或更新了用于对 SAS 令牌进行签名的密钥，则需要为导出操作重新生成新的 SAS 令牌。
1. 选择“生成 SAS 和连接字符串”。
    显示的“SAS 令牌”值是配置导出时所需的令牌。

## <a name="create-a-new-export-with-a-sas-token"></a>使用 SAS 令牌创建新导出

在计费帐户范围导航到“导出”，然后使用以下步骤创建新导出。

1. 选择“创建”。
1. 配置“导出”详细信息，就像配置普通的导出一样。 可将导出配置为使用现有的目录或容器，或者可以指定新的目录或容器，导出将为你创建该目录或容器。
1. 配置存储时，请选择“使用 SAS 令牌”。  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/new-export.png" alt-text="显示可在其中选择 SAS 令牌的“新建导出”的屏幕截图。" lightbox="./media/export-cost-data-storage-account-sas-key/new-export.png" :::
1. 输入存储帐户的名称并粘贴你的 SAS 令牌。
1. 指定现有的容器或目录，或者确定要创建的新容器或目录。
1. 选择“创建”。

基于 SAS 令牌的导出仅在令牌仍然有效时才能正常工作。 请在当前令牌过期之前将其重置，否则导出将停止工作。 由于令牌提供对存储帐户的访问，因此请像对待任何其他敏感信息一样谨慎保护令牌。 将数据导出到存储帐户时，你要负责维护权限和数据访问权限。

## <a name="troubleshoot-exports-using-sas-tokens"></a>排查使用 SAS 令牌的导出的问题

下面是配置或使用基于 SAS 令牌的导出时可能会出现的常见问题。

- Azure 门户中未显示 SAS 密钥选项。
  - 确认你是否是已签订了 Microsoft 合作伙伴协议并且对计费帐户拥有全局管理员权限的合作伙伴。 只有符合这些要求的人员才能使用 SAS 密钥进行导出。

- 尝试配置导出时收到以下错误消息：

    “请确保 SAS 令牌对于 Blob 服务有效、对于容器和对象资源类型有效，并拥有以下权限: 添加、创建、读取、写入、删除。(存储服务错误代码: AuthorizationResourceTypeMismatch)”

    - 确保在 Azure 存储中正确配置并生成 SAS 密钥。

- 创建导出后看不到完整的 SAS 密钥。
  - 看不到密钥是预期的行为。 配置 SAS 导出后，出于安全原因会隐藏密钥。

- 无法从配置了导出的租户访问存储帐户。
  - 这是预期的行为。 如果存储帐户位于另一租户中，则需要先在 Azure 门户中导航到该租户才能看到存储帐户。

- 导出由于发生 SAS 令牌相关的错误而失败。
  - 仅当 SAS 令牌仍然有效时，导出才能正常工作。 请创建新密钥并运行导出。

## <a name="next-steps"></a>后续步骤

- 有关导出成本管理数据的详细信息，请参阅[创建和导出数据](tutorial-export-acm-data.md)。
- 有关导出大量使用情况数据的信息，请参阅[通过导出检索大型数据集](ingest-azure-usage-at-scale.md)。
