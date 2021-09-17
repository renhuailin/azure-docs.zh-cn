---
title: 用于存储的 Azure Defender - 优点和功能
description: 了解用于存储的 Azure Defender 的优点和功能。
author: memildin
ms.author: memildin
ms.date: 02/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 3ba932632e87b87b08cdaf9ec69652dbb2ca3ff4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750697"
---
# <a name="introduction-to-azure-defender-for-storage"></a>用于存储的 Azure Defender 简介

用于存储的 Azure Defender 是 Azure 原生安全智能层，用于检测试图访问或利用你的存储帐户的异常或可能有害的企图。 它利用安全 AI 的高级功能和[ Microsoft 威胁智能](https://go.microsoft.com/fwlink/?linkid=2128684)提供上下文安全警报和建议。

当活动出现异常时，会触发安全警报。 这些警报与 Azure 安全中心集成，还通过电子邮件发送给订阅管理员，其中包含可疑活动的详细信息以及有关如何调查和修正威胁的建议。

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布版 (GA)|
|定价：|用于存储的 Azure Defender 按[安全中心定价](https://azure.microsoft.com/pricing/details/security-center/)中显示的定价计费|
|受保护的存储类型：|[Blob 存储](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure 文件](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|云：|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用云<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure 政府<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure 中国世纪互联|
|||


## <a name="what-are-the-benefits-of-azure-defender-for-storage"></a>适用于存储的 Azure Defender 有哪些优点？

适用于存储的 Azure Defender 提供：

- **Azure 本机安全性** - 单击即可启用适用于存储的 Defender，可保护存储在 Azure Blob、Azure 文件存储和数据湖中的数据。 作为 Azure 本机服务，适用于存储的 Defender 可集中为 Azure 管理的所有数据资产提供安全性，并与 Azure Sentinel 等其他 Azure 安全服务集成。
- **富检测套件** - 适用于存储的 Defender 中的检测功能由 Microsoft 威胁情报提供支持，可检测最常见的存储威胁风险，例如匿名访问、泄露凭据、社会工程、权限滥用和恶意内容。
- **大规模响应** - 安全中心的自动化工具使你可以更轻松地阻止和响应已识别的威胁。 有关详细信息，请参阅[自动响应安全中心触发器](workflow-automation.md)。

:::image type="content" source="media/defender-for-storage-introduction/defender-for-storage-high-level-overview.png" alt-text="适用于存储的 Azure Defender 功能的概要性介绍。":::


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

## <a name="trigger-a-test-alert-for-azure-defender-for-storage"></a>触发 Azure Defender for Storage 的测试警报

若要在你的环境中测试 Azure Defender for Storage 发出的安全警报，请执行以下步骤，生成“从 Tor 出口节点访问存储帐户”警报：

1. 打开存储帐户，并启用 Azure Defender for Storage。
1. 从边栏中选择“容器”，打开现有容器或创建一个新容器。

    :::image type="content" source="media/defender-for-storage-introduction/opening-storage-container.png" alt-text="从 Azure 存储帐户打开 blob 容器。" lightbox="media/defender-for-storage-introduction/opening-storage-container.png":::

1. 将文件上传到该容器。

    > [!CAUTION]
    > 不要上传包含敏感数据的文件。

1. 在上传的文件上使用上下文菜单以选择“生成 SAS”。

    :::image type="content" source="media/defender-for-storage-introduction/generate-sas.png" alt-text="blob 容器中的文件的“生成 SAS”选项。":::

1. 保留默认选项，然后选择“生成 SAS 令牌和 URL”。

1. 复制生成的 SAS URL。

1. 在本地计算机上，打开 Tor 浏览器。

    > [!TIP]
    > 可以从 Tor 项目站点 [https://www.torproject.org/download/](https://www.torproject.org/download/) 下载 Tor。

1. 在 Tor 浏览器中，导航到 SAS URL。

1. 下载步骤 3 中上传的文件。

    在两个小时内，你将从安全中心获得以下安全警报：

    :::image type="content" source="media/defender-for-storage-introduction/tor-access-alert-storage.png" alt-text="有关从 Tor 出口节点访问的安全警报。":::

## <a name="next-steps"></a>后续步骤

本文介绍了用于存储的 Azure Defender。

如需相关材料，请参阅以下文章： 

- 无论警报是由安全中心生成，还是由安全中心从其他安全产品接收，你都可以导出该警报。 若要将警报导出到 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，请按照[将警报导出到 SIEM](continuous-export.md) 中的说明操作。
- [如何启用用于存储的 Advanced Defender](../storage/common/azure-defender-storage-configure.md)
- [用于存储的 Azure Defender 警报列表](alerts-reference.md#alerts-azurestorage)
- [Microsoft 的威胁情报功能](https://go.microsoft.com/fwlink/?linkid=2128684)