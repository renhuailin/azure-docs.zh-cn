---
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: include
ms.date: 05/03/2021
ms.openlocfilehash: 7b35a3c4b26640b872b161bd8130db728645f1d0
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108748241"
---
通过[服务连接](/azure/devops/pipelines/library/service-endpoints)，可以从 Azure DevOps 项目访问 Azure 订阅中的资源。

1. 在 Azure DevOps 中，转到包含目标管道的项目。 在左下角，选择“项目设置”。
1. 在“管道”下，选择“服务连接”。 在右上角，选择“新建服务连接”。
1. 在“新建服务连接”中，选择“Azure 资源管理器”。

    :::image type="content" source="./media/azure-app-configuration-service-connection/new-service-connection.png" alt-text="屏幕截图显示从新的服务连接下拉列表中选择“Azure 资源管理器”。":::
1. 在“身份验证方法”对话框中，选择“服务主体(自动)”以创建新的服务主体，或者选择“服务主体(手动)”以[使用现有服务主体](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops#use-spn&preserve-view=true)。
1. 输入订阅和资源，并为服务连接输入一个名称。

如果创建了新的服务主体，请查找分配给服务连接的服务主体的名称。 在下一步中，你将向此服务主体添加新的角色分配。

1. 访问“项目设置” > “服务连接”。
1. 选择新服务连接。
1. 选择“管理服务主体”。
1. 请注意“显示名称”中的值。

    :::image type="content" source="./media/azure-app-configuration-service-connection/service-principal-display-name.png" alt-text="屏幕截图显示服务主体显示名称。":::
