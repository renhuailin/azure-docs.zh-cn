---
title: include 文件
description: include 文件
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: ecd7e070c353d706865b245427758c11a7bbfa5d
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111896573"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>将 Azure 存储帐户关联到 IoT 中心

你需要拥有与 IoT 中心关联的 Azure 存储帐户。 若要了解如何创建 Azure 存储帐户，请参阅[创建存储帐户](../articles/storage/common/storage-account-create.md)。 将 Azure 存储帐户与 IoT 中心相关联时，IoT 中心会生成一个 SAS URI。 设备可以使用此 SAS URI 安全地将文件上传到 Blob 容器。 IoT 中心服务和设备 SDK 协调生成 SAS URI 的过程，并使其可供设备用来上传文件。

## <a name="create-a-container"></a>创建容器

按照以下步骤为存储帐户创建 Blob 容器：

1. 在存储帐户左侧窗格中的“数据存储”下，选择“容器”。
1. 在“容器”边栏选项卡中，选择“+ 容器”。
1. 在随即打开的“新建容器”窗格中，为容器提供一个名称，然后选择“创建”。

创建容器后，按照[使用 Azure 门户配置文件上传](../articles/iot-hub/iot-hub-configure-file-upload.md)中的说明操作。 确保有一个 Blob 容器与你的 IoT 中心关联并且文件通知已启用

![在门户中启用文件通知](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
