---
author: baanders
description: 包含说明如何验证 Azure 函数已发布的文件
ms.service: digital-twins
ms.topic: include
ms.date: 7/14/2021
ms.author: baanders
ms.openlocfilehash: 543c905cc9e35d985c5b1962067690a0f895eab6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750893"
---
### <a name="verify-the-publication-of-your-function"></a>验证函数的发布

完成发布函数的过程后，可以使用这些步骤来验证发布是否成功。
 
1. 转到 [Azure 门户](https://portal.azure.com/)并使用自己的凭据登录。
2. 在窗口顶部的搜索框中，搜索函数应用名称，然后选择它。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/search-function-app.png" alt-text="显示 Azure 门户的屏幕截图。在搜索字段中，输入函数应用名称。" lightbox="../articles/digital-twins/media/includes/azure-functions/search-function-app.png":::

3. 在打开的“函数应用”页中，选择左侧菜单中的“函数”。 在列表中查找函数的名称，验证其是否已成功发布。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/view-published-functions.png" alt-text="显示 Azure 门户中已发布的函数的屏幕截图。" lightbox="../articles/digital-twins/media/includes/azure-functions/view-published-functions.png":::

    > [!Note] 
    > 你可能需要等待几分钟或刷新页面，然后你的函数才会出现在已发布函数列表中。