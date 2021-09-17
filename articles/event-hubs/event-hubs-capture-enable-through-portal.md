---
title: 事件中心 - 使用 Azure 门户捕获流式处理事件
description: 本文介绍如何使用 Azure 门户捕获通过 Azure 事件中心流式处理的事件。
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: b603af17ca4e46b8004b7b2550cddd87164da157
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253633"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>启用捕获通过 Azure 事件中心流式处理的事件

Azure [事件中心捕获][capture-overview]用于自动将事件中心的流数据传递到所选的 [Azure Blob](https://azure.microsoft.com/services/storage/blobs/) 存储或 [Azure Data Lake Storage Gen1 或 Gen 2](https://azure.microsoft.com/services/data-lake-store/) 帐户。

可以使用 [Azure 门户](https://portal.azure.com)在创建事件中心时配置捕获。 可以将数据捕获到 Azure [Blob 存储](https://azure.microsoft.com/services/storage/blobs/)容器或 [Azure Data Lake Storage Gen 1 或 Gen 2](https://azure.microsoft.com/services/data-lake-store/) 帐户。

有关详细信息，请参阅[事件中心捕获概述][capture-overview]。

> [!IMPORTANT]
> 目标存储（Azure 存储或 Azure Data Lake Storage）帐户必须与事件中心位于同一订阅中。

## <a name="capture-data-to-azure-storage"></a>将数据捕获到 Azure 存储

创建事件中心以后，即可在“创建事件中心”门户屏幕中单击“启用”按钮，以便启用捕获。  然后在“捕获提供程序”框中单击“Azure 存储”，指定存储帐户和容器。  由于事件中心捕获对存储使用服务到服务身份验证，因此无需指定存储连接字符串。 资源选取器自动为存储帐户选择资源 URI。 如果使用 Azure Resource Manager，必须以字符串形式显式提供此 URI。

默认时间窗口为 5 分钟。 最小值为 1，最大值为 15。 **大小** 窗口的范围为 10-500 MB。

![捕获的时间范围][1]

> [!NOTE]
> 可以允许或禁止当捕获时间段内未发生任何事件时发出空文件。 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>将数据捕获到 Azure Data Lake Storage Gen 2 

1. 请按照[创建存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account)一文创建 Azure 存储帐户。 请在“高级”选项卡上将“分层命名空间”设置为“启用”，使其成为 Azure Data Lake Storage Gen 2 帐户  。 Azure 存储帐户必须与事件中心位于同一订阅中。
2. 创建事件中心时，请执行以下步骤： 

    1. 将“捕获”设置为“打开” 。 
    2. 选择“Azure 存储”作为捕获提供程序。 “捕获提供程序”对应的 Azure Data Lake Store 选项为 Azure Data Lake Storage Gen 1 。 要使用 Azure Data Lake Storage Gen 2，请选择“Azure 存储”。
    2. 选择“选择容器”按钮。 

        ![启用捕获到 Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. 从列表中选择 Azure Data Lake Storage Gen 2 帐户。 

    ![选择 Data Lake Storage Gen2](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. 选择“容器”（Data Lake Storage Gen 2 中的文件系统）。

    ![在存储中选择文件系统](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. 在“创建事件中心”页面上，选择“创建” 。 

    ![选择“创建”按钮](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > 你使用此用户界面 (UI) 在 Azure Data Lake Storage Gen 2 中创建的容器将显示在“存储资源管理器”中的“文件系统”下 。 同样，你在 Data Lake Storage Gen 2 帐户下创建的文件系统在此 UI 中将显示为容器。 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>将数据捕获到 Azure Data Lake Storage Gen 1 

若要将数据捕获到 Azure Data Lake Storage Gen 1，请创建一个 Data Lake Storage Gen 1 帐户和一个事件中心：

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>创建 Azure Data Lake Storage Gen 1 帐户和文件夹

1. 若要创建 Data Lake Storage 帐户，请遵循[通过 Azure 门户开始使用 Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-get-started-portal.md) 中的说明。
2. 请遵循[为事件中心分配权限](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs)部分中的说明，在 Data Lake Storage Gen 1 帐户中创建一个文件夹（要在其中捕获事件中心的数据），为事件中心分配权限，以便它可以将数据写入到 Data Lake Storage Gen 1 帐户。  


### <a name="create-an-event-hub"></a>创建事件中心

1. 事件中心必须与创建的 Data Lake Storage Gen 1 帐户位于同一 Azure 订阅。 创建事件中心，在“创建事件中心”门户页中单击“捕获”下的“启用”按钮。 
2. 在“创建事件中心”门户页的“捕获提供程序”框中选择“Azure Data Lake Store”。
3. 在靠近“Data Lake Store”下拉列表的“选择存储”中，指定此前创建的 Data Lake Storage Gen 1 帐户，然后在“Data Lake 路径”中输入已创建的数据文件夹的路径  。

    ![选择 Data Lake Storage 帐户][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>在现有的事件中心添加或配置捕获

可以在事件中心命名空间中的现有事件中心配置捕获。 若要对现有的事件中心启用“捕获”功能，或者要更改“捕获”设置，请单击命名空间以加载概览屏幕，然后单击要启用或更改“捕获”设置的事件中心。 最后，单击已打开页面左侧的“捕获”选项，然后编辑设置，如以下各图所示：

### <a name="azure-blob-storage"></a>Azure Blob 存储

![配置 Azure Blob 存储][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

![配置 Azure Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1 

![配置 Azure Data Lake Storage][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>后续步骤

- 阅读[事件中心捕获概述][capture-overview]，详细了解事件中心捕获。
- 还可以通过 Azure Resource Manager 模板配置事件中心捕获。 有关详细信息，请参阅[通过 Azure 资源管理器模板启用捕获](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)。
- [了解如何创建使用事件中心命名空间作为源的 Azure 事件网格订阅](store-captured-data-data-warehouse.md)
- [Azure Data Lake Store 入门（使用 Azure 门户）](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
