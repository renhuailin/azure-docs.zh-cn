---
title: Azure 流分析中的数据保护
description: 本文介绍如何加密 Azure 流分析作业使用的专用数据。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 4436289d544de057acef132117346ac53c20b5a7
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576482"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Azure 流分析中的数据保护 

Azure 流分析是一种完全托管的平台即服务，可用于构建实时分析管道。 所有繁重的提升（如群集预配、调整节点以适应你的使用情况和管理内部检查点）都在幕后进行管理。

## <a name="private-data-assets-that-are-stored"></a>存储的专用数据资产

为了运行，Azure 流分析将保留以下元数据和数据： 

* 查询定义及其相关配置  

* 用户定义的函数或聚合  

* 流分析运行时所需的检查点

* 引用数据的快照 

* 流分析作业使用的资源的连接详细信息

为了帮助你满足任何管控行业或环境中的符合性义务，你可以阅读有关 [Microsoft 的符合性产品/服务的](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)详细信息。 

## <a name="in-region-data-residency"></a>In-Region 数据驻留
Azure 流分析存储上述客户数据和其他元数据。 默认情况下，客户数据由 Azure 流分析存储在单个区域中，因此，此服务会自动满足区域数据派驻要求，其中包括 [信任中心](https://azuredatacentermap.azurewebsites.net/)中指定的要求。
此外，通过在所选存储帐户中对其进行加密，你可以选择将所有数据资产 (客户数据和其他元数据) 与你的流分析作业相关联。

## <a name="encrypt-your-data"></a>加密数据

流分析自动在其基础结构中使用同类最佳的加密标准来加密和保护数据。 您可以简单地信任流分析来安全地存储所有数据，这样就无需担心如何管理基础结构。

如果要使用客户管理的密钥来加密数据，可以使用自己的存储帐户 (常规用途 V1 或 V2) 来存储流分析运行时所需的任何专用数据资产。 可以根据需要加密存储帐户。 流分析基础结构不会永久存储任何专用数据资产。 

此设置必须在流分析作业创建时配置，并且不能在作业的整个生命周期内修改。 不建议修改或删除流分析正在使用的存储。 如果删除存储帐户，则将永久删除所有专用数据资产，这将导致作业失败。 

不能使用流分析门户将密钥更新或轮换到存储帐户。 可以使用 REST Api 更新密钥。


### <a name="configure-storage-account-for-private-data"></a>为专用数据配置存储帐户 

加密存储帐户以保护所有数据，并明确选择专用数据的位置。 

为了帮助你满足任何管控行业或环境中的符合性义务，你可以阅读有关 [Microsoft 的符合性产品/服务的](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)详细信息。 

使用以下步骤配置专用数据资产的存储帐户。 此配置是从流分析作业而不是从存储帐户进行的。

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 在 Azure 门户的左上角选择“创建资源”。 

1.  **Analytics**   > 从 "结果" 列表中选择 "分析 **流分析作业**   "。 

1. 在 "流分析作业" 页中填写必要的详细信息，如名称、区域和缩放。 

1. 选中 " *保护我的存储帐户中此作业所需的所有专用数据资产*" 的复选框。

1. 从订阅中选择一个存储帐户。 请注意，在作业的整个生命周期内不能修改此设置。 创建作业后，也无法添加此选项。

1. 若要使用连接字符串进行身份验证，请从 "身份验证模式" 下拉列表中选择 " **连接字符串** "。 存储帐户密钥会自动从你的订阅中填充。

   ![专用数据存储帐户设置](./media/data-protection/storage-account-create.png)

1. 若要通过托管标识 (预览) 进行身份验证，请从 "身份验证模式" 下拉菜单中选择 " **托管标识** "。 如果选择托管标识，则需要将流分析作业添加到存储帐户的访问控制列表。 如果你没有为你的作业授予访问权限，则作业将无法执行任何操作。 有关如何授予访问权限的详细信息，请参阅 [使用 AZURE RBAC 向另一资源分配托管标识访问权限](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md#use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource)。

   :::image type="content" source="media/data-protection/storage-account-create-msi.png" alt-text="具有托管标识身份验证的专用数据存储帐户设置":::

## <a name="private-data-assets-that-are-stored-by-stream-analytics"></a>流分析存储的专用数据资产

流分析需要保存的任何专用数据存储在存储帐户中。 专用数据资产的示例包括： 

* 已创作的查询及其相关配置  

* 用户定义的函数 

* 流分析运行时所需的检查点

* 引用数据的快照 

还将存储你的流分析作业使用的资源的连接详细信息。 加密存储帐户以保护你的所有数据。 

为了帮助你满足任何管控行业或环境中的符合性义务，你可以阅读有关 [Microsoft 的符合性产品/服务的](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)详细信息。 

## <a name="enables-data-residency"></a>启用数据驻留 
你可以通过相应地提供存储帐户，使用此功能来强制实施任何数据驻留要求。

## <a name="known-issues"></a>已知问题
存在一个已知问题，即使用客户托管密钥的作业在使用托管标识向任何输入或输出进行身份验证时出现故障。 

## <a name="next-steps"></a>后续步骤

* [创建 Azure 存储帐户](../storage/common/storage-account-create.md)
* [理解 Azure 流分析的输入](stream-analytics-add-inputs.md)
* [Azure 流分析作业中的检查点和重播概念](stream-analytics-concepts-checkpoint-replay.md)
* [使用参考数据在流分析中查找](stream-analytics-use-reference-data.md)
