---
title: 快速入门：在 Azure Sentinel 中载入
description: 在本快速入门中，学习如何通过先启用 Azure Sentinel，再连接数据源来加入该 Sentinel。
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 10/14/2020
ms.custom: references_regions
ms.openlocfilehash: 49aef35c5f69bd5ad09c4b1154fbee75ba2aa204
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251828"
---
# <a name="quickstart-on-board-azure-sentinel"></a>快速入门：载入 Azure Sentinel

在本快速入门中，了解如何载入 Azure Sentinel。 若要载入 Azure Sentinel，首先需要启用 Azure Sentinel，然后再连接到数据源。

Azure Sentinel 随附许多适用于 Microsoft 解决方案的开箱即用的连接器，提供实时集成，包括 Microsoft 365 Defender（之前称为 Microsoft 威胁防护）解决方案、Microsoft 365 源（包括 Office 365）、Azure AD、Microsoft Defender for Identity（之前称为 Azure ATP）、Microsoft Cloud App Security 和 Azure 安全中心的 Azure Defender 警报等。 此外，内置的连接器可以拓宽非 Microsoft 解决方案的安全生态系统。 也可以使用常用事件格式 (CEF)、Syslog 或 REST-API 将数据源与 Azure Sentinel 相连接。

连接数据源后，从熟练地创建的工作簿的库中进行选择，这些工作簿基于你的数据呈现见解。 可以根据需要轻松地自定义这些工作簿。

>[!IMPORTANT]
> 有关在使用 Azure Sentinel 时产生费用的信息，请参阅 [Azure Sentinel 的定价](https://azure.microsoft.com/pricing/details/azure-sentinel/)和 [Azure Sentinel 的成本和收费](azure-sentinel-billing.md)。

## <a name="global-prerequisites"></a>全局先决条件

- 如果没有可用的 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- Log Analytics 工作区。 了解如何[创建 Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)。 有关 Log Analytics 工作区的详细信息，请参阅[设计 Azure 监视日志部署](../azure-monitor/logs/design-logs-deployment.md)。

- 若要启用 Azure Sentinel，需要获取 Azure Sentinel 工作区所在订阅的参与者权限。 
- 若要使用 Azure Sentinel，需要获取工作区所属资源组的“参与者”或“读取者”权限。
- 连接特定数据源可能需要其他权限。
- Azure Sentinel 是付费服务。 有关详细信息，请参阅[关于 Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058)。

有关详细信息，请参阅[关于部署 Azure Sentinel 的预部署活动和先决条件](prerequisites.md)。

### <a name="geographical-availability-and-data-residency"></a>地理可用性和数据驻留

- Azure Sentinel 可在 [Log Analytics 的大多数 GA 区域](https://azure.microsoft.com/global-infrastructure/services/?products=monitor)的工作区中运行，但中国和德国（主权）区域除外。 新的 Log Analytics 区域可能需要一些时间来加入 Azure Sentinel 服务。 

- 由 Azure Sentinel 生成的数据，例如事件、书签和分析规则，可能包含来源于客户 Log Analytics 工作区的一些客户数据。 根据工作区所在的地理位置，此 Azure Sentinel 生成的数据将保存在下表所列的地理位置中：

    | 工作区地理位置 | Azure Sentinel 生成的数据地理位置 |
    | --- | --- |
    | 美国<br>印度 | 美国 |
    | 欧洲<br>法国 | 欧洲 |
    | 澳大利亚 | 澳大利亚 |
    | United Kingdom | United Kingdom |
    | Canada | Canada |
    | 日本 | 日本 |
    | 亚太区 | 亚太 * |
    | 巴西 | 巴西 * |
    | 挪威 | 挪威 |
    | 非洲 | 非洲 |
    | 韩国 | 韩国 |
    | 德国 | 德国 |
    | 阿拉伯联合酋长国 | 阿拉伯联合酋长国 |
    | 瑞士 | 瑞士 |
    |

    \*目前仅在亚太地区的东南亚区域（新加坡）和巴西地区的巴西南部区域（圣保罗州）提供单个区域数据驻留。 对于所有其他区域，客户数据可以存储在 Sentinel 所在的工作区地理位置中的任何位置。

    > [!IMPORTANT]
    > - 通过启用使用机器学习 (ML) 引擎的某些规则，你可以向 Microsoft 授予在你的 Azure Sentinel 工作区的地理位置之外复制相关引入数据的权限，因为机器学习引擎可能需要该权限才能处理这些规则。

## <a name="enable-azure-sentinel"></a>启用 Azure Sentinel <a name="enable"></a>

1. 登录到 Azure 门户。 确保已选中在其中创建 Azure Sentinel 的订阅。

1. 搜索“Azure Sentinel”并将其选中。

   ![服务搜索](./media/quickstart-onboard/search-product.png)

1. 选择 **添加** 。

1. 选择要使用的工作区，或创建新工作区。 可以在多个工作区上运行 Azure Sentinel，但将数据隔离到单个工作区。

   ![选择工作区](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Azure 安全中心创建的默认工作区将不会显示在列表中；无法在其上安装 Azure Sentinel。
   >

   >[!IMPORTANT]
   >
   > - 部署到工作区后，Azure Sentinel 当前不支持将该工作区移至其他资源组或订阅。 
   >
   >   如果已移动工作区，请禁用“分析”下的所有活动规则，并在五分钟后重新启用这些规则。 重申一下，这在大多数情况下应该是有效的，但不支持这样做，风险由你自己承担。

1. 选择“添加 Azure Sentinel”。

## <a name="connect-data-sources"></a>连接数据源

Azure Sentinel 连接到服务并将事件和日志转发到 Azure Sentinel，以便引入服务和应用中的数据。 对于计算机和虚拟机，可以安装用于收集日志并将其转发到 Azure Sentinel 的 Log Analytics 代理。 对于防火墙和代理，Azure Sentinel 会在 Linux Syslog 服务器上安装 Log Analytics 代理，代理将从该服务器收集日志文件并将其转发到 Azure Sentinel。 
 
1. 在主菜单上，选择“数据连接器”。 随即打开数据连接器库。

1. 此库是所有可连接数据源的列表。 选择一个数据源，然后单击“打开连接器页面”按钮。

1. 连接器页将显示有关如何配置连接器的说明，以及可能需要的任何其他说明。<br>
例如，如果选择可将日志从 Azure AD 流式传输到 Azure Sentinel 的 Azure Active Directory 数据源，则可以选择你想要获取的日志类型，以便登录日志和/或审核日志。 <br> 有关详细信息，请按照安装说明或[参阅相关的连接指南](data-connectors-reference.md)。 有关数据连接器的信息，请参阅 [Azure Sentinel 数据连接器](connect-data-sources.md)。

1. 连接器页上“后续步骤”选项卡显示了数据连接器附带的相关内置工作簿、示例查询和分析规则模板。 你可以按原样使用它们，也可以对其进行修改，无论哪种方式，你都可以立即获取数据的有趣见解。 <br>

连接数据源后，数据开始流式传输到 Azure Sentinel，并准备好供你开始使用。 你可以在[内置仪表板](get-visibility.md)中查看日志并开始在 Log Analytics 中构建查询以[调查数据](investigate-cases.md)。

有关详细信息，请参阅[数据收集最佳做法](best-practices-data.md)。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- 备用部署选项：

    - [通过 API 部署 Azure Sentinel](/rest/api/securityinsights/)
    - [通过 PowerShell 部署 Azure Sentinel](https://www.powershellgallery.com/packages/Az.SecurityInsights/0.1.0)
    - [通过 ARM 模板部署 Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-all-in-one-accelerator/ba-p/1807933)

- **入门**：
    - [Azure Sentinel 入门](get-visibility.md)
    - [创建自定义分析规则以检测威胁](detect-threats-custom.md)
    - [使用通用事件格式连接外部解决方案](connect-common-event-format.md)

