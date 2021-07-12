---
title: 启用 Azure 安全中心的集成式工作负载保护
description: 了解如何启用 Azure Defender，以将 Azure 安全中心的保护扩展到混合云与多云资源
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 06/07/2021
ms.openlocfilehash: 4f7900c4abc37df776c231ac9d70a09862f6d49f
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111748385"
---
# <a name="quickstart-enable-azure-defender"></a>快速入门：启用 Azure Defender

若要了解 Azure Defender 的优点，请参阅[安全中心免费版与已启用 Azure Defender 的版本的比较](security-center-pricing.md)。

## <a name="prerequisites"></a>先决条件

若要学习安全中心快速入门和教程，需要启用 Azure Defender。 

可以使用 Azure Defender 保护整个 Azure 订阅，且该保护将由订阅中的所有资源继承。

可免费试用 30 天。 有关根据你所在区域以所选货币给出的定价详细信息，请参阅[安全中心定价](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="enable-azure-defender-from-the-azure-portal"></a>从 Azure 门户启用 Azure Defender

若要启用包括威胁防护功能在内的全部安全中心功能，必须在包含适用工作负载的订阅上启用 Azure Defender。 如果在工作区级别启用它，则不会为 Azure 资源启用实时 VM 访问、自适应应用程序控制和网络检测功能。 此外，只在工作区级别提供两种 Azure Defender 计划：适用于服务器的 Azure Defender，以及适用于计算机上的 SQL Server 的 Azure Defender。

- 可以在订阅级别或资源级别启用 **用于存储帐户的 Azure Defender**
- 可以在订阅级别或资源级别启用 **用于 SQL 的 Azure Defender**。
- 你只能在资源级别启用用于开源关系数据库的 Azure Defender

### <a name="to-enable-azure-defender-on-your-subscriptions-and-workspaces"></a>若要在订阅和工作区中启用 Azure Defender：

- 若要在一个订阅中启用 Azure Defender：

    1. 从安全中心的主菜单中，选择“定价和设置”。
    1. 选择要保护的订阅或工作区。
    1. 选择要升级的“Azure Defender 打开”。
    1. 选择“保存”。

    > [!TIP]
    > 可以看到，Azure Defender 中的每个计划是单独定价的，并可以单独设置为“打开”或“关闭”。 例如，你可能想要在没有关联 Azure 应用服务计划的订阅中关闭用于应用服务的 Azure Defender。 

    :::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="门户中安全中心的定价页":::

- 在多个订阅或工作区中启用 Azure Defender：

    1. 从安全中心的边栏选择“开始使用”。

        “升级”选项卡列出了符合加入条件的订阅和工作区。

        :::image type="content" source="./media/enable-azure-defender/get-started-upgrade-tab.png" alt-text="“入门”页的“升级”选项卡"::: 

    1. 在“选择要在其中启用 Azure Defender 的订阅和工作区”列表中，选择要升级的订阅和工作区，然后选择“升级”以启用 Azure Defender。 

       - 如果选择的订阅和工作区不符合试用条件，会在下一步骤中对其进行升级，并开始收费。
       - 如果你选择了一个有符合免费试用条件的工作区，下一步将开始试用。

        :::image type="content" source="./media/enable-azure-defender/upgrade-selected-workspaces-and-subscriptions.png" alt-text="在“开始使用”页中升级所有选定的工作区和订阅":::


## <a name="disable-azure-defender"></a>禁用 Azure Defender

如果需要为订阅禁用 Azure Defender，则可采用相同的过程，但选择“关闭 Azure Defender”：
 
1. 从安全中心的菜单中，选择“定价和设置”。
1. 选择相关订阅。
1. 如果订阅已启用 Azure Defender，请打开该“Azure Defender 计划”，然后选择“关闭 Azure Defender”。 

    :::image type="content" source="./media/enable-azure-defender/disable-plans.png" alt-text="启用或禁用 Azure Defender":::

1. 选择“保存”。

> [!NOTE]
> 禁用 Azure Defender 后（无论是禁用单个计划还是一次禁用所有计划），数据收集可能会在短时间内继续。 

## <a name="next-steps"></a>后续步骤

启用 Azure Defender 后，请根据[自动预配代理和扩展](security-center-enable-data-collection.md)中所述，启用由所需代理和扩展实现的自动数据收集。