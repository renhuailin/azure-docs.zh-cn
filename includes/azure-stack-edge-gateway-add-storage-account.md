---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: 0c7e011cf8445164e0931f71e390813c9134dd89
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200929"
---
1. 在 [Azure 门户](https://portal.azure.com/)中，选择自己的 Azure Stack Edge 资源，然后转到“概览”。  设备应为“联机”状态。 转到“云存储空间网关”>“存储帐户”。

2. 在设备命令栏中，选择“+ 添加存储帐户”。 

   ![添加存储帐户](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-1.png)

3. 在“添加 Edge 存储帐户”窗格中，指定以下设置：

    1. 为设备上的 Edge 存储帐户提供唯一名称。 存储帐户名称只能包含小写的数字和字母。 不允许使用特殊字符。 存储帐户名称在设备中必须是唯一的（并非跨设备）。

    2. 提供存储帐户所持有数据的相关信息的可选说明。  
    
    3. 默认情况下，Edge 存储帐户会映射到云中的 Azure 存储帐户，存储帐户中的数据会自动推送到云中。 指定 Edge 存储帐户所映射到的 Azure 存储帐户。

    4. 创建新的容器，或者从 Azure 存储帐户的现有容器中进行选择。 写入 Edge 存储帐户的设备中的所有数据都将自动上传到映射的 Azure 存储帐户中的所选存储容器。

    5. 指定所有存储帐户选项后，选择“添加”以创建 Edge 存储帐户。 成功创建 Edge 存储帐户后，你会收到通知。 然后，新的 Edge 存储帐户会显示在 Azure 门户中的存储帐户列表中。

    <!--[Add a storage account](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-2.png)-->
    
4. 如果选择这个新存储帐户并转到访问密钥，则可以找到 blob 服务终结点和相应的存储帐户名称。 复制此信息，因为这些值以及访问密钥将帮助你连接到 Edge 存储帐户。

    ![添加存储帐户 2](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-4.png)

    可以通过[使用 Azure 资源管理器连接到设备本地 API ](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md)来获取访问密钥。 
