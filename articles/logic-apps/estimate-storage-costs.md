---
title: 估算单租户 Azure 逻辑应用的存储成本
description: 使用逻辑应用存储计算器估算工作流的存储成本。
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: how-to
ms.date: 05/13/2021
ms.openlocfilehash: 093dd29a4e9eda9157a49c6cc44144b5e8a4bbec
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110369142"
---
# <a name="estimate-storage-costs-for-workflows-in-single-tenant-azure-logic-apps"></a>估算单租户 Azure 逻辑应用中工作流的存储成本

Azure 逻辑应用使用 [Azure 存储](/azure/storage/)进行任何存储操作。 在传统的多租户 Azure 逻辑应用中，任何存储使用情况和成本都将附加到该逻辑应用。 现在，在单租户 Azure 逻辑应用中，你可以使用自己的存储帐户。 这些存储成本将在 Azure 计费发票中单独列出。 此功能提供更高的灵活性，并可让你控制逻辑应用数据。

> [!NOTE]
> 本文适用于单租户 Azure 逻辑应用环境中的工作流。 这些工作流存在于共享同一存储的同一逻辑应用和单个租户中。 有关详细信息，请参阅[单租户与多租户以及集成服务环境](single-tenant-overview-compare.md)。

存储成本根据工作流的内容而变化。 不同的触发器、操作和有效负载会导致不同的存储操作和需求。 本文介绍当你对基于单租户的逻辑应用使用自己的 Azure 存储帐户时如何估算存储成本。 首先，可以使用逻辑应用存储计算器来[估算要执行的存储操作数量](#estimate-storage-needs)。 然后，可以在 Azure 定价计算器中使用这些数字[估算可能的存储成本](#estimate-storage-costs)。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 一个基于单租户的逻辑应用工作流。 可以[使用 Azure 门户](create-single-tenant-workflows-azure-portal.md)或[使用 Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md) 创建工作流。 如果你还没有工作流，可以在存储计算器中使用小型、中型和大型示例工作流。

* 一个要用于工作流的 Azure 存储帐户。 如果你没有存储帐户，请[创建一个存储帐户](../storage/common/storage-account-create.md)

## <a name="get-your-workflows-json-code"></a>获取工作流的 JSON 代码

如果你有一个要估算的工作流，请获取工作流的 JSON 代码：

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 转到“逻辑应用”服务并选择你的工作流。
1. 在逻辑应用菜单中的“开发工具”下，选择“逻辑应用代码视图” 。
1. 复制工作流的 JSON 代码。

## <a name="estimate-storage-needs"></a>估算存储需求

1. 转到[逻辑应用存储计算器](https://logicapps.azure.com/calculator)。

   :::image type="content" source="./media/estimate-storage-costs/storage-calculator.png" alt-text="逻辑应用存储计算器的屏幕截图，其中显示了输入界面。" lightbox="./media/estimate-storage-costs/storage-calculator.png":::

1. 输入、上传或选择基于单租户的逻辑应用工作流的 JSON 代码。

   * 如果你在上一部分复制了代码，请将它粘贴到“粘贴或上传 workflow.json”框中。 
   * 如果你在本地保存了 workflow.json 文件，请在“选择工作流”下选择“浏览文件”  。 选择该文件，然后选择“打开”。
   * 如果你还没有工作流，请在“选择工作流”下选择一个示例工作流。

1. 查看“高级选项”下的选项。 这些设置取决于工作流类型，其中可能包括：

   * 一个用于输入循环运行次数的选项。
   * 一个用于选择有效负载超过 32 KB 的所有操作的选项。

1. 对于“每月运行”，请输入每月运行工作流的次数。
1. 选择“计算”并等待计算运行完成。
1. 在“存储操作明细和计算步骤”下，查看“操作计数”估算值 。

    在两个表中，可以看到按运行和按月估算的操作计数。 将显示以下操作：

    * Blob (读取)：表示 Azure Blob 存储读取操作。
    * Blob (写入)：表示 Azure Blob 存储写入操作。
    * 队列：表示“Azure 队列”队列类 2 操作。
    * 表：表示 Azure 表存储操作。

    每个操作都有最小、最大和“最合理的猜测”计数。 请根据你的个人方案，选择用于[估算存储操作成本](#estimate-storage-costs)的最相关数字。 通常，建议使用“最合理的猜测”计数以获得准确的结果。 不过，你也可以使用最大计数来确保成本估算值具有一定的缓冲余地。

    :::image type="content" source="./media/estimate-storage-costs/storage-calculator-results.png" alt-text="逻辑应用存储计算器的屏幕截图，其中显示了包含估算的操作数量的输出。" lightbox="./media/estimate-storage-costs/storage-calculator-results.png":::

## <a name="estimate-storage-costs"></a>估算存储成本

[计算逻辑应用工作流的存储需求](#estimate-storage-needs)后，可以估算可能的每月存储成本。 可以估算以下存储操作类型的价格：

* [Blob 存储读取和写入操作](#estimate-blob-storage-operations-costs)
* [队列存储操作](#estimate-queue-operations-costs)
* [表存储操作](#estimate-table-operations-costs)

### <a name="estimate-blob-storage-operations-costs"></a>估算 Blob 存储操作成本

> [!NOTE]
> 此功能当前不可用。 目前仍可以使用计算器来估算[队列存储](#estimate-queue-operations-costs)和[表存储](#estimate-table-operations-costs)。

若要估算逻辑应用 Blob 存储操作的每月成本，请执行以下操作：

1. 转到 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)。
1. 在“产品”选项卡上，选择“存储”&gt;“存储帐户”  。 或者，在“搜索栏”搜索框中输入“存储帐户”，然后选择相应的磁贴 。
    :::image type="content" source="./media/estimate-storage-costs/pricing-calculator-storage-tile.png" alt-text="Azure 定价计算器的屏幕截图，其中显示了用于添加“存储帐户”视图的磁贴。" lightbox="./media/estimate-storage-costs/pricing-calculator-storage-tile.png":::
1. 在“已添加存储帐户”通知中，选择“查看”以查看计算器的“存储帐户”部分  。 或者，手动转到“存储帐户”部分。
1. 对于“区域”，请选择你的逻辑应用所在的区域。
1. 对于“类型”，请选择“块 Blob 存储” 。
1. 对于“性能层”，请选择你的性能层。
1. 对于“冗余”，请选择你的冗余级别。
1. 根据需要调整任何其他设置。
1. 在“写入操作”下，按原样输入逻辑应用存储计算器提供的“Blob (写入)”操作数量 。
1. 在“读取操作”下，按原样输入逻辑应用存储计算器提供的“Blob (读取)”操作数量 。
1. 查看估算的 Blob 存储操作成本。

### <a name="estimate-queue-operations-costs"></a>估算队列操作成本

若要估算逻辑应用队列操作的每月成本，请执行以下操作：

1. 转到 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)。
1. 在“产品”选项卡上，选择“存储”&gt;“存储帐户”  。 或者，在“搜索栏”搜索框中输入“存储帐户”，然后选择相应的磁贴 。
    :::image type="content" source="./media/estimate-storage-costs/pricing-calculator-storage-tile.png" alt-text="Azure 定价计算器的屏幕截图，其中显示了用于添加“存储帐户”视图的磁贴。" lightbox="./media/estimate-storage-costs/pricing-calculator-storage-tile.png":::
1. 在“已添加存储帐户”通知中，选择“查看”以查看计算器的“存储帐户”部分  。 或者，手动转到“存储帐户”部分。
1. 对于“区域”，请选择你的逻辑应用所在的区域。
1. 对于“类型”，请选择“队列存储” 。
1. 对于“存储帐户类型”，请选择你的存储帐户类型。
1. 对于“冗余”，请选择你的冗余级别。
1. 在“队列类 2 操作”下，输入逻辑应用存储计算器提供的“队列”操作数量除以 10,000 后得到的数字 。 之所以需要执行此步骤，是因为计算器采用队列操作的事务单位来计算。
1. 查看估算的队列操作成本。

### <a name="estimate-table-operations-costs"></a>估算表操作成本

若要估算逻辑应用表存储操作的每月成本，请执行以下操作：

1. 转到 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)。
1. 在“产品”选项卡上，选择“存储”&gt;“存储帐户”  。 或者，在“搜索栏”搜索框中输入“存储帐户”，然后选择相应的磁贴 。
    :::image type="content" source="./media/estimate-storage-costs/pricing-calculator-storage-tile.png" alt-text="Azure 定价计算器的屏幕截图，其中显示了用于添加“存储帐户”视图的磁贴。" lightbox="./media/estimate-storage-costs/pricing-calculator-storage-tile.png":::
1. 在“已添加存储帐户”通知中，选择“查看”以查看计算器的“存储帐户”部分  。 或者，手动转到“存储帐户”部分。
1. 对于“区域”，请选择你的逻辑应用所在的区域。
1. 对于“类型”，请选择“表存储” 。
1. 对于“层”，请选择你的性能层。
1. 对于“冗余”，请选择你的冗余级别。
1. 在“存储事务”下，输入逻辑应用存储计算器提供的“表”操作数量除以 10,000 后得到的数字 。 之所以需要执行此步骤，是因为计算器采用队列操作的事务单位来计算。
1. 查看估算的表存储操作成本。

## <a name="next-step"></a>后续步骤

> [!div class="nextstepaction"]
> [规划和管理逻辑应用的成本](plan-manage-costs.md)
