---
title: Azure 流分析中的数据保护
description: 本文介绍如何加密 Azure 流分析作业使用的专用数据。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 07/07/2021
ms.openlocfilehash: b236c433e55bb2b702497b42a1b4fc73ce520f00
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113491130"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Azure 流分析中的数据保护 

Azure 流分析是一种完全托管的平台即服务，使你能够构建实时分析管道。 所有繁重的工作，例如群集预配、缩放节点以适应使用情况以及管理内部检查点，都在后台进行管理。

## <a name="private-data-assets-that-are-stored"></a>存储的专用数据资产

Azure 流分析保留以下元数据和数据以便运行： 

* 查询定义及其相关配置  

* 用户定义的函数或聚合  

* 流分析运行时所需的检查点

* 参考数据的快照 

* 流分析作业使用的资源的连接详细信息

为帮助你在任何受监管的行业或环境中履行合规义务，你可以阅读有关 [Microsoft 的合规性产品](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)的详细信息。 

## <a name="in-region-data-residency"></a>区域中数据驻留
Azure 流分析存储上述客户数据和其他元数据。 默认情况下，Azure 流分析将客户数据存储在一个区域中，这样此服务就会自动满足区域内数据驻留要求，包括[信任中心](https://azuredatacentermap.azurewebsites.net/)内指定的要求。
此外，可以选择将与流分析作业相关的所有数据资产（客户数据和其他元数据）存储在一个区域中，方法是在所选存储帐户中对其进行加密。

## <a name="encrypt-your-data"></a>加密数据

流分析会自动在其基础架构中采用一流的加密标准来加密和保护数据。 你只需信任流分析可以安全地存储所有数据，这样就无需担心如何管理基础结构。

如果要使用客户管理的密钥来加密数据，可以使用自己的存储帐户（常规用途 V1 或 V2）来存储流分析运行时所需的任何专用数据资产。 可以根据需要加密存储帐户。 流分析基础结构不会永久存储任何专用数据资产。 

必须在创建流分析作业时配置此设置，并且在作业的整个生命周期中都不能对其进行修改。 不建议修改或删除流分析正在使用的存储。 如果删除存储帐户，则将永久删除所有专用数据资产，这将导致作业失败。 

无法使用流分析门户为存储帐户更新或轮换密钥。 可以使用 REST API 更新密钥。


### <a name="configure-storage-account-for-private-data"></a>配置专用数据的存储帐户 

加密存储帐户以保护所有数据，并显式选择专用数据的位置。 

为帮助你在任何受监管的行业或环境中履行合规义务，你可以阅读有关 [Microsoft 的合规性产品](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)的详细信息。 

使用以下步骤配置专用数据资产的存储帐户。 此配置通过流分析作业（而不是存储帐户）进行。

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 在 Azure 门户的左上角选择“创建资源”。 

1. 从结果列表中选择“分析” >“流分析作业”  ****  **** 。 

1. 在流分析作业页面中填写必要的详细信息，如名称、区域和规模。 

1. 选中显示“在我的存储帐户中保护此作业所需的全部专用数据资产”的复选框。

1. 从订阅中选择一个存储帐户。 请注意，作业的整个生命周期内都不能修改此设置。 创建作业后，也无法添加此选项。

1. 若要使用连接字符串进行身份验证，请从“身份验证模式”下拉列表中选择“连接字符串”。 存储帐户密钥会自动从你的订阅中填充。

   ![专用数据存储帐户设置](./media/data-protection/storage-account-create.png)

1. 若要使用托管标识（预览版）进行身份验证，请从“身份验证模式”下拉列表中选择“托管标识”。 如果选择托管标识，则需要使用“存储 Blob 数据参与者”角色将流分析作业添加到存储帐户的访问控制列表中。 如果不向作业授予访问权限，作业将无法执行任何操作。 有关如何授予访问权限的详细信息，请参阅[使用 Azure RBAC 授予托管标识对另一资源的访问权限](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md#use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource)。

   :::image type="content" source="media/data-protection/storage-account-create-msi.png" alt-text="具有托管标识身份验证的专用数据存储帐户设置":::

## <a name="private-data-assets-that-are-stored-by-stream-analytics"></a>流分析存储的专用数据资产

需要流分析保留的任何专用数据都存储在存储帐户中。 专用数据资产的示例包括： 

* 已创建的查询及其相关配置  

* 用户定义的函数 

* 流分析运行时所需的检查点

* 参考数据的快照 

流分析作业使用的资源的连接详细信息也将存储。 加密存储帐户以保护你的所有数据。 

为帮助你在任何受监管的行业或环境中履行合规义务，你可以阅读有关 [Microsoft 的合规性产品](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)的详细信息。 

## <a name="enables-data-residency"></a>启用数据驻留 
通过提供相应的存储帐户，可以使用此功能强制执行任何数据驻留要求。

## <a name="next-steps"></a>后续步骤

* [创建 Azure 存储帐户](../storage/common/storage-account-create.md)
* [理解 Azure 流分析的输入](stream-analytics-add-inputs.md)
* [Azure 流分析作业中的检查点和重播概念](stream-analytics-concepts-checkpoint-replay.md)
* [使用参考数据在流分析中查找](stream-analytics-use-reference-data.md)
