---
title: 用于存储的 Azure Defender - 优点和功能
description: 了解用于存储的 Azure Defender 的优点和功能。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 675751433657a7d630d0c42482716702f520ff82
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881378"
---
# <a name="introduction-to-azure-defender-for-storage"></a>用于存储的 Azure Defender 简介


用于存储的 Azure Defender 是 Azure 原生安全智能层，用于检测试图访问或利用你的存储帐户的异常或可能有害的企图。 它利用安全 AI 的高级功能和[ Microsoft 威胁智能](https://go.microsoft.com/fwlink/?linkid=2128684)提供上下文安全警报和建议。

当活动出现异常时，会触发安全警报。 这些警报与 Azure 安全中心集成，还通过电子邮件发送给订阅管理员，其中包含可疑活动的详细信息以及有关如何调查和修正威胁的建议。

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布 (GA)|
|定价：|**用于存储的 Azure Defender** 按 [定价页中](security-center-pricing.md)的定价计费|
|受保护的存储类型：|[Blob 存储](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure 文件](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) US Gov<br>![否](./media/icons/no-icon.png) China Gov，其他 Gov|
|||


## <a name="what-are-the-benefits-of-azure-defender-for-storage"></a>适用于存储的 Azure Defender 有哪些优点？

适用于存储的 Azure Defender 提供：

- **Azure 本机安全性** - 单击即可启用适用于存储的 Defender，可保护存储在 Azure Blob、Azure 文件存储和数据湖中的数据。 作为 Azure 本机服务，适用于存储的 Defender 可集中为 Azure 管理的所有数据资产提供安全性，并与 Azure Sentinel 等其他 Azure 安全服务集成。
- **富检测套件** - 适用于存储的 Defender 中的检测功能由 Microsoft 威胁情报提供支持，可检测最常见的存储威胁风险，例如匿名访问、泄露凭据、社会工程、权限滥用和恶意内容。
- **大规模响应** - 安全中心的自动化工具使你可以更轻松地阻止和响应已识别的威胁。 有关详细信息，请参阅[自动响应安全中心触发器](workflow-automation.md)。

:::image type="content" source="media/defender-for-storage-introduction/defender-for-storage-high-level-overview.png" alt-text="适用于存储的 Azure Defender 功能的概要性介绍":::


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>用于存储的 Azure Defender 提供哪种类型的警报？

当存在以下情况时，会触发安全警报：

- **可疑访问模式** - 例如，从 Tor 出口节点或从 Microsoft 威胁智能视为可疑的 IP 成功访问
- **可疑活动** - 例如异常数据提取或访问权限的异常更改
- **上传恶意内容** - 例如潜在的恶意软件（基于哈希信誉分析）或托管钓鱼内容

警报包含触发警报的事件的详细信息，并提供有关如何调查和消除威胁的建议。 警报可导出到 Azure Sentinel 或任何其他第三方 SIEM 或任何其他外部工具。

> [!TIP]
> 最佳做法是在订阅级别[配置用于存储的 Azure Defender](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)，但也可以[在单独的存储帐户上进行配置](../storage/common/azure-defender-storage-configure.md?tabs=azure-portal)。


## <a name="what-is-hash-reputation-analysis-for-malware"></a>什么是针对恶意软件的哈希信誉分析？

为确定上传的文件是否可疑，用于存储的 Azure Defender 使用由 [Microsoft 威胁情报](https://go.microsoft.com/fwlink/?linkid=2128684)提供支持的哈希信誉分析。 威胁防护工具不扫描上传的文件，而是检查存储日志，并将新上传的文件的哈希值与已知病毒、木马、间谍软件和勒索软件的哈希值进行比较。 

当怀疑某个文件包含恶意软件时，安全中心会显示一个警报，并且可以选择向存储所有者发送电子邮件，请求其批准删除该可疑文件。 若要设置为自动删除哈希信誉分析指示为包含恶意软件的文件，请部署[工作流自动化，以便在出现包含“可能有恶意软件上传到存储帐户”信息的警报时触发操作](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005)。

> [!NOTE]
> 若要启用安全中心的威胁保护功能，必须在包含适用工作负载的订阅上启用 Azure Defender。
>
> 可以在订阅级别或资源级别启用 **用于存储的 Azure Defender**。



## <a name="next-steps"></a>后续步骤

本文介绍了用于存储的 Azure Defender。

如需相关材料，请参阅以下文章： 

- 无论警报是由安全中心生成，还是由安全中心从其他安全产品接收，你都可以导出该警报。 若要将警报导出到 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，请按照[将警报导出到 SIEM](continuous-export.md) 中的说明操作。
- [如何启用用于存储的 Advanced Defender](../storage/common/azure-defender-storage-configure.md)
- [用于存储的 Azure Defender 警报列表](alerts-reference.md#alerts-azurestorage)
- [Microsoft 的威胁情报功能](https://go.microsoft.com/fwlink/?linkid=2128684)