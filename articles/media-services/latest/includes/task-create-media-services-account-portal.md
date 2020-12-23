---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: 72857564a6b195353e7bf2a27c8369004d52d3f2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95971334"
---
<!-- Use the portal to create a media services account. -->

## <a name="create-a-media-services-account"></a>创建媒体服务帐户

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 单击“+创建资源” > “媒体” > “媒体服务”。
1. 在“创建媒体服务帐户”部分中输入所需的值。

    | 名称 | 说明 |
    | ---|---|
    |**帐户名**|输入新的媒体服务帐户的名称。 媒体服务帐户名称由小写字母或数字构成（不含空格），长度为 3 到 24 个字符。|
    |**订阅**|如果有多个订阅，请从有权访问的 Azure 订阅的列表中选择一个订阅。|
    |**资源组**|选择新的或现有的资源。 资源组是共享生命周期、权限和策略的资源的集合。 在[此处](../../../azure-resource-manager/management/overview.md#resource-groups)了解更多信息。|
    |**位置**|选择用于存储媒体服务帐户的媒体和元数据记录的地理区域。 此区域用于处理和流式传输媒体。 下拉列表中仅显示可用的媒体服务区域。 |
    |**存储帐户**|选择一个存储帐户，以便为媒体服务帐户中的媒体内容提供 Blob 存储。 可以选择位于媒体服务帐户所在的地理区域内的现有存储帐户，也可以创建一个新的存储帐户。 在同一区域内会创建一个新的存储帐户。 适用于存储帐户名的规则对媒体服务帐户同样适用。<br/><br/>必须具有一个主存储帐户，并且可以拥有任意数量的与媒体服务帐户关联的辅助存储帐户 。 可以使用 Azure 门户来添加辅助存储帐户。 有关详细信息，请参阅 [Azure 存储帐户与 Azure 媒体服务帐户](../storage-account-concept.md)。<br/><br/>媒体服务帐户和所有关联的存储帐户必须位于同一 Azure 订阅中。 强烈建议在媒体服务帐户所在的位置使用存储帐户，避免额外的延迟和数据出口成本。|
    |**高级设置**| 可通过选择“系统托管”单选按钮，使用系统托管标识创建帐户。  做出此选择后，你可以使用客户管理的密钥或创建自己的密钥 (BYOK) 和媒体服务来启用受信任的存储。  有关客户管理的密钥的详细信息，请参阅[创建自己的密钥（客户管理的密钥）与媒体服务](../concept-use-customer-managed-keys-byok.md)。 此外，还将启用[托管标识](../concept-managed-identities.md)。

1. 选择“固定到仪表板”以查看帐户部署进度。
1. 单击窗体底部的“创建”。
