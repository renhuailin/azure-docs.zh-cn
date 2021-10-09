---
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 1/20/2021
ms.openlocfilehash: 388de1966832b21d8134a671f23840841de198e5
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209717"
---
## <a name="creating-and-running-a-scan"></a>创建和运行扫描

> [!Note] 
> 下面所示的步骤和屏幕截图演示了跨不同数据源类型管理扫描的一般过程。 选项可能会略有不同，具体取决于所使用的数据源类型。

若要创建并运行新扫描，请执行以下操作：

1. 在 [Purview Studio](https://web.purview.azure.com/resource/) 的左窗格中选择“数据映射”选项卡。

1. 选择所注册的数据源。

1. 选择“新建扫描”

1. 选择要连接到数据源的凭据。 

   :::image type="content" source="media/manage-scans/set-up-scan.png" alt-text="设置扫描":::

1. 通过选中列表中的适当项目，可以将扫描范围限定于数据源的特定部分，如文件夹、集合或架构。

   :::image type="content" source="media/manage-scans/scope-your-scan.png" alt-text="限定扫描范围":::

1. 然后为你的扫描选择扫描规则集。 可以选择系统默认项、现有的自定义项，也可以内联方式创建新项。

   :::image type="content" source="media/manage-scans/scan-rule-set.png" alt-text="扫描规则集":::

1. 选择扫描触发器。 可以设置一个计划或运行一次扫描。

   :::image type="content" source="media/manage-scans/trigger-scan.png" alt-text="trigger":::

1. 查看扫描并选择“保存并运行”。

## <a name="viewing-your-scans-and-scan-runs"></a>查看扫描和扫描运行

若要查看现有扫描，请执行以下操作：

1. 导航到管理中心。 在“源和扫描”部分下，选择“数据源” 。 

2. 选择所需的数据源。 你将看到该数据源的现有扫描列表。

3. 选择想要查看其结果的扫描。

4. 此页将向你展示先前的所有扫描运行，以及每次扫描运行的指标和状态。 它还将展示扫描是按计划执行还是手动执行、已应用分类的资产数量、发现的总资产数量、扫描的开始和结束时间，以及扫描总持续时间。

## <a name="manage-your-scans---edit-delete-or-cancel"></a>管理扫描 - 编辑、删除或取消

若要管理或删除扫描，请执行以下操作：

1. 导航到管理中心。 在“源和扫描”部分下，选择“数据源”，然后选择所需的数据源 。

2. 选择要管理的扫描。 可以通过选择“编辑”来编辑扫描。

3. 可以通过选择“删除”来删除扫描。 
