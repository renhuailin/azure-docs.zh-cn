---
title: 私密且安全地扫描数据源
description: 本文介绍如何设置专用终结点以从受限网络扫描数据源
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: cbdf2220d1b4087376bc40db5b7da167144e5d9b
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123257195"
---
# <a name="scan-your-data-sources-privately-and-securely"></a>私密且安全地扫描数据源

如果计划在专用网络、虚拟网络中和专用终结点后面扫描数据源，则必须部署 Azure Purview 引入专用终结点，以确保元数据的网络隔离从正在被扫描的数据源流向 Azure Purview 数据映射。

Azure Purview 可以使用引入专用终结点扫描 Azure 或本地环境中的数据源。 部署引入专用终结点时，需要部署三个专用终结点资源并将其链接到 Azure Purview 托管资源：

- Blob 专用终结点链接到 Azure Purview 托管存储帐户。
- 队列专用终结点链接到 Azure Purview 托管存储帐户。
- 命名空间专用终结点链接到 Azure Purview 托管事件中心命名空间。

:::image type="content" source="media/catalog-private-link/purview-private-link-architecture-ingestion.png" alt-text="显示 Azure Purview 和专用链接体系结构的关系图。":::

## <a name="deployment-checklist"></a>部署清单
使用本指南中的部署选项之一，为 Azure Purview 帐户启用引入专用终结点：

1. 选择适当的 Azure 虚拟网络和子网来部署 Azure Purview 引入专用终结点。 选择以下选项之一：
   - 在 Azure 订阅中部署[新的虚拟网络](../virtual-network/quick-create-portal.md)。
   - 在 Azure 订阅中查找现有的 Azure 虚拟网络和子网。
  
2. 为引入定义适当的 [DNS 名称解析方法](./catalog-private-link-name-resolution.md#deployment-options)，以便 Azure Purview 可以使用专用网络扫描数据源。 可以使用以下任一选项：
   - 使用本指南中进一步介绍的步骤部署新的 Azure DNS 区域。
   - 使用本指南中进一步介绍的步骤将所需的 DNS 记录添加到现有 Azure DNS 区域。
   - 完成本指南中的步骤后，在现有 DNS 服务器中手动添加所需的 DNS A 记录。
3. 使用引入专用终结点部署[新 Purview 帐户](#option-1---deploy-a-new-azure-purview-account-with-ingestion-private-endpoint)，或为[现有 Purview 帐户](#option-2---enable-ingestion-private-endpoint-on-existing-azure-purview-accounts)部署引入专用终结点。
4. 在部署 Azure Purview 引入专用终结点的同一 VNet 内部署和注册[自承载集成运行时](#deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources)。
5. 完成本指南后，根据需要调整 DNS 配置。
6. 验证 Azure Purview 管理计算机、自承载 IR VM 和数据源之间的网络和名称解析。 

## <a name="option-1---deploy-a-new-azure-purview-account-with-_ingestion_-private-endpoint"></a>选项 1 - 使用引入专用终结点部署新的 Azure Purview 帐户

1. 转到 [Azure 门户](https://portal.azure.com)，然后转到“Purview 帐户”页面。 选择“+ 创建”，创建新的 Azure Purview 帐户。

2. 填写基本信息，然后在“网络”选项卡中，将“连接方法”设置为“专用终结点”。 将“启用专用终结点”设置为“仅引入”。

      :::image type="content" source="media/catalog-private-link/purview-pe-scenario-2-1.png" alt-text="显示创建专用终结点页面选择的屏幕截图。":::

3. 通过提供要与专用终结点配对的“订阅”、“虚拟网络”和“子网”的详细信息，设置引入专用终结点。

4. 若要使用 Azure 专用 DNS 区域，请选择“专用 DNS 集成”（可选）。
   
   > [!IMPORTANT]
   > 若要允许在 Azure Purview 和数据源之间进行正确的名称解析，必须选择恰当的 Azure 专用 DNS 区域。 也可使用现有 Azure 专用 DNS 区域，或者稍后在 DNS 服务器中手动创建 DNS 记录。 有关详细信息，请参阅[为专用终结点配置 DNS 名称解析](./catalog-private-link-name-resolution.md)

5.  选择“查看 + 创建”  。 在“查看 + 创建”页面上，Azure 会验证你的配置。

6.  看到“验证通过”的消息后，选择“创建”。

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="显示创建帐户验证通过的屏幕截图。":::

## <a name="option-2---enable-_ingestion_-private-endpoint-on-existing-azure-purview-accounts"></a>选项 2 - 在现有 Azure Purview 帐户上启用引入专用终结点

1.  转到 [Azure 门户](https://portal.azure.com)，然后单击 Azure Purview 帐户，在“设置”下选择“网络”，然后选择“引入专用终结点连接”  。

2. 在引入专用终结点连接下，选择“+ 新建”，创建新的引入专用终结点。

3. 填写基本信息，选择现有虚拟网络和子网详细信息。 若要使用 Azure 专用 DNS 区域，请选择“专用 DNS 集成”（可选）。 
   
   :::image type="content" source="media/catalog-private-link/ingestion-pe-fill-details.png" alt-text="显示填写专用终结点详细信息的屏幕截图。":::
   
   > [!IMPORTANT]
   > 若要允许在 Azure Purview 和数据源之间进行正确的名称解析，必须选择恰当的 Azure 专用 DNS 区域。 也可使用现有 Azure 专用 DNS 区域，或者稍后在 DNS 服务器中手动创建 DNS 记录。 
   > 
   >有关详细信息，请参阅[为专用终结点配置 DNS 名称解析](./catalog-private-link-name-resolution.md)。


4. 选择“创建”以完成设置。

> [!NOTE]
> 仅可通过上述步骤中的 Azure Purview 门户体验来创建引入专用终结点。 无法从专用链接中心创建它们。

## <a name="deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources"></a>部署自承载集成运行时 (IR) 并扫描数据源。
为 Azure Purview 部署引入专用终结点后，需要设置并注册至少一个自承载集成运行时 (IR)：

- 当前仅基于自承载 IR 的扫描支持所有本地源类型（如 Microsoft SQL Server、Oracle、SAP 等）。 自承载 IR 必须在专用网络中运行，然后与你在 Azure 中的虚拟网络对等互连。 
   
- 对于 Azure Blob 存储和 Azure SQL 数据库等所有 Azure 源类型，必须显式选择使用与 Azure Purview 引入专用终结点部署在同一 VNet 中的自承载集成运行时来运行扫描。 

按照[创建和管理自承载集成运行时](manage-integration-runtimes.md)中的步骤设置自承载 IR。 然后，在“通过集成运行时进行连接”下拉框中选择该自承载 IR 以确保网络隔离，以在 Azure 源上设置扫描。
    
   :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="显示使用自承载 IR 运行 Azure 扫描的屏幕截图。":::

> [!IMPORTANT]
> 如果在 2021 年 8 月 18 日之后创建了 Azure Purview 帐户，请确保从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=39717)下载并安装最新版本的自承载集成运行时。
> 
## <a name="next-steps"></a>后续步骤

-  [验证专用终结点的解析](./catalog-private-link-name-resolution.md)
-  [在 Azure Purview 中管理数据源](./manage-data-sources.md)
-  [对 Azure Purview 帐户的专用终结点配置进行故障排除](./catalog-private-link-troubleshoot.md)
