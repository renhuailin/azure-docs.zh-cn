---
title: 创建 Azure 事件中心架构注册表
description: 本文介绍如何在 Azure 事件中心命名空间中创建架构注册表。
ms.topic: how-to
ms.date: 06/01/2021
ms.custom: references_regions
ms.openlocfilehash: 360f81157f5431a6e6e70a25ef33e707c57cc91a
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129536605"
---
# <a name="create-an-azure-event-hubs-schema-registry--preview"></a>创建 Azure 事件中心架构注册表（预览版）
本文介绍如何在 Azure 事件中心托管的架构注册表中创建包含架构的架构组。 有关 Azure 事件中心的架构注册表功能的概述，请参阅[事件中心中的 Azure 架构注册表](schema-registry-overview.md)。

> [!NOTE]
> - 架构注册表功能目前处于预览状态，不建议用于生产工作负载 。
> - 此功能在基本层不可用。
> - 如果事件中心位于一个虚拟网络中，则除非从同一虚拟网络中的 VM 访问 Azure 门户，否则无法在门户中创建架构。 

## <a name="prerequisites"></a>先决条件
[创建事件中心命名空间](event-hubs-create.md#create-an-event-hubs-namespace)。 也可以使用现有命名空间。 

## <a name="create-a-schema-group"></a>创建架构组
1. 导航到“事件中心命名空间”页。 
1. 在左侧菜单中选择“架构注册表”。 若要创建架构组，请在工具栏上选择 “+ 架构组”。 

    :::image type="content" source="./media/create-schema-registry/namespace-page.png" alt-text="显示 Azure 门户中“架构注册表”页的图像":::
1. 在“创建架构组”页中，执行以下步骤：
    1. 为架构组输入“名称”。
    1. 对于“序列化类型”，请选取应用于架构组中所有架构的序列化格式。 目前仅支持“Avro”类型，因此请选择“Avro”。 
    1. 为组中所有架构选择“兼容性模式”。 对于“Avro”，支持向前和向后兼容性模式。 
    1. 然后，选择“创建”以创建架构组。 
    
        :::image type="content" source="./media/create-schema-registry/create-schema-group-page.png" alt-text="显示用于创建架构组的页面的图像":::
1. 在架构组列表中选择“架构组”名称。

    :::image type="content" source="./media/create-schema-registry/select-schema-group.png" alt-text="显示所选列表中的架构组的图像。":::    
1. 将看到该组的“架构组”页。

    :::image type="content" source="./media/create-schema-registry/schema-group-page.png" alt-text="显示“架构组”页的图像":::
    

## <a name="add-a-schema-to-the-schema-group"></a>向架构组添加架构
在本部分中，将使用 Azure 门户向架构组添加架构。 

1. 在“架构组”页中，选择工具栏上的“+ 架构”。 
1. 在“创建架构”页中，执行以下步骤：
    1. 为架构输入“名称”。
    1. 将以下“架构”输入到文本框中。 也可以选择包含架构的文件。
    
        ```json
        {
            "type": "record",
            "name": "AvroUser",
            "namespace": "com.azure.schemaregistry.samples",
            "fields": [
                {
                    "name": "name",
                    "type": "string"
                },
                {
                    "name": "favoriteNumber",
                    "type": "int"
                }
            ]
        }
        ```
    1. 选择“创建”。 
1. 从架构列表中选择“架构”。 

    :::image type="content" source="./media/create-schema-registry/select-schema.png" alt-text="显示所选架构的图像。":::
1. 将看到该架构的“架构概述”页。 

    :::image type="content" source="./media/create-schema-registry/schema-overview-page.png" alt-text="显示“架构概述”页的图像":::    
1. 如果架构存在多个版本，则会在“版本”下拉列表中看到它们。 选择一个版本以切换到该版本的架构。 

## <a name="create-a-new-version-of-schema"></a>创建新版本的架构

1. 在文本框中更新架构，然后选择“验证”。 在下面的示例中，已将一个新字段 `id` 添加到架构。 

    :::image type="content" source="./media/create-schema-registry/update-schema.png" alt-text="显示“更新架构”页的图像":::    
    
1. 查看验证状态和更改，然后选择“保存”。 

    :::image type="content" source="./media/create-schema-registry/compare-save-schema.png" alt-text="显示“审阅”页的图像，该页显示了“验证状态”、“更改”和“保存”":::     
1. 将看到“架构概述”页的“版本”中选择了 `2`。  

    :::image type="content" source="./media/create-schema-registry/new-version.png" alt-text="显示新版架构的图像":::    
1. 选择 `1` 查看版本 1 的架构。 


## <a name="next-steps"></a>后续步骤
有关架构注册表的详细信息，请参阅[事件中心中的 Azure 架构注册表](schema-registry-overview.md)。

