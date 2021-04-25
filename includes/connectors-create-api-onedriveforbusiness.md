---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 06a503e800309d8689735417931820d9f16a6e75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "87102483"
---
## <a name="prerequisites"></a>先决条件

* [OneDrive for Business](https://OneDrive.com) 帐户 

必须先在 Azure 门户中授予逻辑应用连接到 OneDrive for Business 帐户的权限，然后才可将 OneDrive for Business 帐户用于逻辑应用。

请按照以下步骤授予逻辑应用连接到 OneDrive for Business 帐户的权限：  

1. 登录到 Azure 门户。 

1. 在“Azure 服务”下，选择“逻辑应用” 。 然后从列表中选择相应逻辑应用的名称。

1. 在逻辑应用菜单中的“开发工具”下，选择“逻辑应用设计器” 。

1. 在逻辑应用设计器的下拉列表中选择“显示 Microsoft 托管的 API”，并在搜索框中输入“OneDrive for Business”。 选择要使用的触发器或操作：  

   ![逻辑应用设计器的屏幕截图，其中显示了具有 OneDrive for Business API 操作的重复触发器。](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)

2. 如果以前尚未创建任何与 OneDrive for Business 的连接，请按照提示提供 OneDrive for Business 凭据。 这些凭据用于授予逻辑应用访问 OneDrive for Business 帐户数据的权限：  

   ![逻辑应用设计器的屏幕截图，其中显示了 OneDrive for Business 的登录提示。](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)

3. 提供 OneDrive for Business 用户名和密码，对逻辑应用进行授权：  

   ![OneDrive for Business 登录页面的屏幕截图，其中显示了登录提示。](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   

4. 步骤中现在列出了此连接。 选择“保存”，然后继续创建逻辑应用。 

   ![逻辑应用设计器的屏幕截图，其中显示了列出 OneDrive for Business API 连接的触发器。](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   
