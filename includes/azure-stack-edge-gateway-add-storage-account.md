---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/04/2021
ms.author: alkohli
ms.openlocfilehash: 5f39f727deaf3a53db5e2928e5af23779c298318
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97911553"
---
1. 在 [Azure 门户](https://portal.azure.com/)中，选择自己的 Azure Stack Edge 资源，然后转到“概览”。  设备应为“联机”状态。 转到“云存储空间网关”>“存储帐户”。

2. 在设备命令栏中，选择“+ 添加存储帐户”。 

   ![添加存储帐户](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-1.png)

3. 在“添加 Edge 存储帐户”窗格中，指定以下设置：

    a. 设备上 Edge 存储帐户的唯一名称。 存储帐户名称只能包含小写的数字和字母。 不允许使用特殊字符。 存储帐户名称在设备中必须是唯一的（并非跨设备）。

    b. 有关存储帐户所保存数据的信息的可选说明。  
    
    c. 默认情况下，Edge 存储帐户映射到云中的 Azure 存储帐户，存储帐户中的数据会自动推送到云中。 指定 Edge 存储帐户所映射到的 Azure 存储帐户。  

    d. 接下来，创建新的容器，或者从 Azure 存储帐户中的现有容器中进行选择。 写入 Edge 存储帐户的设备中的所有数据都将自动上传到映射的 Azure 存储帐户中的所选存储容器。

    <!--![Add a storage account](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-2.png)-->

    e. 指定所有存储帐户选项后，选择“添加”以创建 Edge 存储帐户。 成功创建 Edge 存储帐户后，你将收到通知。 然后，新的 Edge 存储帐户会显示在 Azure 门户中的存储帐户列表中。 

    
4. 如果选择这个新存储帐户并转到访问密钥，则可以找到 blob 服务终结点和相应的存储帐户名称。 复制此信息，因为这些值以及访问密钥将帮助你连接到 Edge 存储帐户。

    ![添加存储帐户 2](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-4.png)

    可以通过[使用 Azure 资源管理器连接到设备本地 API ](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md)来获取访问密钥。 
