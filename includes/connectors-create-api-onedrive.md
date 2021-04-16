---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 27df83552b450b7657c0595d1d419001df98148c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "87040223"
---
## <a name="prerequisites"></a>先决条件

* 一个 [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 帐户 

必须先在 Azure 门户中授权逻辑应用连接到 OneDrive 帐户，然后才可以将 OneDrive 帐户用于逻辑应用。

请按照以下步骤授权逻辑应用连接到 OneDrive 帐户：  

1. 登录到 Azure 门户。 

1. 在“Azure 服务”下，选择“逻辑应用” 。 然后从列表中选择相应逻辑应用的名称。

1. 在逻辑应用菜单中的“开发工具”下，选择“逻辑应用设计器”。

1. 在逻辑应用设计器的下拉列表中选择“显示 Microsoft 托管的 API”，并在搜索框中输入“OneDrive”。 选择要使用的触发器或操作：

   ![逻辑应用设计器的屏幕截图，其中显示了可添加的 OneDrive API 操作的列表。](./media/connectors-create-api-onedrive/onedrive-1.png)

2. 如果你以前尚未创建任何与 OneDrive 的连接，请按照提示使用 OneDrive 凭据进行登录：  

   ![逻辑应用设计器的屏幕截图，其中显示了 OneDrive API 的登录提示。](./media/connectors-create-api-onedrive/onedrive-2.png)

3. 选择“登录”  ，并输入用户名和密码。 选择“登录”  ： 

   ![用于执行 OneDrive API 授权的 Microsoft 帐户登录页的屏幕截图。](./media/connectors-create-api-onedrive/onedrive-3.png)   

    这些凭据将用于授权逻辑应用访问 OneDrive 帐户中的数据。 

4. 选择“是”  即可授权逻辑应用使用 OneDrive 帐户：  

   ![Microsoft 帐户对逻辑应用授权的屏幕截图，其中显示了允许的操作。](./media/connectors-create-api-onedrive/onedrive-4.png)   
   
5. 步骤中现在列出了此连接。 选择“保存”，然后继续创建逻辑应用。 

   ![逻辑应用设计器的屏幕截图，其中显示了使用 OneDrive API 连接的操作编辑器。](./media/connectors-create-api-onedrive/onedrive-5.png)
