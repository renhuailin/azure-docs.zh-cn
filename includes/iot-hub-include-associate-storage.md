---
title: include 文件
description: include 文件
services: iot-hub
author: JimacoMS3
ms.service: iot-hub
ms.topic: include
ms.date: 01/22/2019
ms.author: v-jbrannian
ms.custom: include file
ms.openlocfilehash: de266fe50b163820b3dd50545546d099431daefa
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114729880"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>将 Azure 存储帐户关联到 IoT 中心

若要从设备上传文件，必须拥有与 IoT 中心关联的 Azure 存储帐户和 Azure Blob 存储容器。 将存储帐户和容器与 IoT 中心关联后，IoT 中心可以在设备请求时提供 SAS URI 的元素。 然后，设备可以使用这些元素来构造 SAS URI，并将其用于向 Azure 存储进行身份验证，以及上传文件到 blob 容器。

将 Azure 存储帐户与 IoT 中心关联：

1. 在“消息”下方，IoT 中心的左窗格中选择“文件上传” 。

    :::image type="content" source="./media/iot-hub-include-associate-storage/select-storage.png" alt-text="门户中选择“文件上传设置”。":::

1. 在“文件上传”窗格中，选择“Azure 存储容器” 。 对于本文，建议你的存储帐户和 IoT 中心位于同一区域。 
    * 如果你已有要使用的存储帐户，请从列表中选择它。 

    * 若要创建新的存储帐户，请选择“+存储帐户”。 为该存储帐户命名，并确保其位置设置为 IoT 中心的同一区域，然后选择“确定”  。 将在 IoT 中心的同一资源组中创建新帐户。 部署完成后，从列表中选择“存储帐户”。 

    选择“存储帐户”后，将打开“容器”窗格。 

1. 在“容器”窗格中，选择“blob 容器”。
    * 如果已有要使用的 blob 容器，请从列表中选择它，然后单击“选择”。 
    
    * 然后选择“新建容器”以创建新的 blob 容器。 为新容器命名。 出于本文的目的，可以将其他所有字段保留为默认值。 选择“创建”。 部署完成后，从列表中选择“容器”，然后单击“选择”。

1. 回到“文件上传”窗格，确保文件通知设置为“开启” 。 可将其他所有设置保留默认值。 选择“保存”并等待设置完成，然后进入下一部分。 

    :::image type="content" source="./media/iot-hub-include-associate-storage/file-upload-settings-small.png" alt-text="门户中确认文件上传设置。":::

有关如何创建 Azure 存储帐户的详细说明，请参阅[创建存储帐户](../articles/storage/common/storage-account-create.md)。 若要了解如何将存储帐户以及 blob 容器与 IoT 中心关联的详细信息，请参阅[使用 Azure 门户配置文件上传](../articles/iot-hub/iot-hub-configure-file-upload.md)。
