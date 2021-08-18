---
title: 保护链接服务
description: 了解如何使用托管的 VNet 预配和保护链接服务
services: synapse-analytics
author: ashinMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: seshin
ms.reviewer: jrasnick
ms.openlocfilehash: ea5be6fc0594d679e7b8dcbd2037e7b47bce993f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734716"
---
# <a name="secure-a-linked-service-with-private-links"></a>使用专用链接保护链接服务

本文介绍如何使用专用终结点保护 Synapse 中的链接服务。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**：如果还没有 Azure 订阅，可以在开始前创建一个 [免费 Azure 帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**，使用 Azure Data Lake Gen 2 作为源数据存储。 如果没有存储帐户，请参阅[创建 Azure 存储帐户](../../storage/common/storage-account-create.md)来了解创建步骤。 请确保存储帐户具有用于访问它的 Synapse Studio IP 筛选，并且你只允许所选网络访问该存储帐户。 “防火墙和虚拟网络”边栏选项卡下的设置应如下图所示。

![安全存储帐户](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>使用专用链接创建链接服务

在 Azure Synapse Analytics 中，链接服务是定义到其他服务的连接信息的一个位置。 在本部分中，你将添加 Azure Synapse Analytics 和 Azure Data Lake Gen 2 作为链接服务。

1. 打开 Synapse Studio，转到“管理”选项卡。
1. 在“外部连接”下，选择“链接服务”。
1. 若要添加链接服务，请选择“新建”。
1. 从列表中选择“Azure Data Lake Storage Gen2”磁贴，然后选择“继续”。
1. 请确保启用“交互式创作”。 启用可能需要大约 1 分钟的时间。 
1. 输入你的身份验证凭据。 帐户密钥、服务主体和托管标识是目前支持的身份验证类型。 选择“测试连接”以验证你的凭据是否正确。
1. 选择“测试连接”，它应该会失败，因为存储帐户不允许在未创建和批准专用终结点的情况下访问它。 在错误消息中，应会看到一个用于创建专用终结点的链接，可以访问该链接以转到下一部分。 如果访问该链接，请跳过下一部分。
1. 完成后，选择“创建”。

## <a name="create-a-managed-private-endpoint"></a>创建托管专用终结点

如果在测试上述连接时未选择访问超链接，请遵循以下路径。 创建一个托管专用终结点，并将该终结点连接到上面创建的链接服务。

1. 转到“管理”选项卡。
1. 转到“托管虚拟网络”部分。
1. 选择托管专用终结点下的“+ 新建”。
1. 从列表中选择“Azure Data Lake Storage Gen2”磁贴，然后选择“继续”。
1. 输入前面创建的存储帐户的名称。
1. 选择“创建”
1. 等待几秒钟后，会显示创建的专用链接需要审批。

## <a name="private-link-approval"></a>专用链接审批
1. 选择前面创建的专用终结点。 可以看到一个超链接，该超链接将引导你在存储帐户级别批准专用终结点。 另一种方法是直接转到 Azure 门户存储帐户并进入“专用终结点连接”边栏选项卡。
1. 勾选在 Studio 中创建的专用终结点，并选择“批准”。
1. 添加描述，然后选择“是”
1. 返回到 Synapse Studio 中“管理”选项卡的“托管虚拟网络”部分 。
1. 大约 1 分钟后，对专用终结点的批准才会反映出来。

## <a name="check-the-connection-works"></a>检查连接是否正常
1. 转到“管理”选项卡，然后选择已创建的链接服务。
1. 请确保“交互式创作”处于活可用动状态。
1. 选择“测试连接”。 应会看到连接成功。

现已在 Synapse 与链接服务之间建立安全的专用连接。

## <a name="next-steps"></a>后续步骤


若要进一步了解 Azure Synapse Analytics 中的托管专用终结点，请参阅[托管专用终结点](../security/synapse-workspace-managed-private-endpoints.md)。


有关 Azure Synapse Analytics 的数据集成的详细信息，请参阅[将数据引入到数据湖中](data-integration-data-lake.md)一文。
